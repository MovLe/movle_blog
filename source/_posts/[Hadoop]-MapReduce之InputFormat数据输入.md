---
title: '[Hadoop]-MapReduce之InputFormat数据输入'
date: 2019-01-01 17:51:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
#### 1.Job提交流程和切片源码详解
##### (1).job提交流程源码详解

```shell
waitForCompletion()
     
    submit();        
	connect();	// 1建立连接
		
	new Cluster(getConfiguration());  // 1）创建提交job的代理
			
	initialize(jobTrackAddr, conf); // （1）判断是本地yarn还是远程
	
    submitter.submitJobInternal(Job.this, cluster)       // 2 提交job
	
	Path jobStagingArea = JobSubmissionFiles.getStagingDir(cluster, conf);  // 1）创建给集群提交数据的Stag路径
	
	JobID jobId = submitClient.getNewJobID();    // 2）获取jobid ，并创建job路径

    copyAndConfigureFiles(job, submitJobDir);	// 3）拷贝jar包到集群
	rUploader.uploadFiles(job, jobSubmitDir);

    writeSplits(job, submitJobDir);// 4）计算切片，生成切片规划文件
	maps = writeNewSplits(job, jobSubmitDir);
	input.getSplits(job);
                          // 5）向Stag路径写xml配置文件
    writeConf(conf, submitJobFile);
	conf.writeXml(out);
                        // 6）提交job,返回提交状态
    status = submitClient.submitJob(jobId, submitJobDir.toString(), job.getCredentials());
```
##### (2).FileInputFormat源码解析(input.getSplits(job))(**这里留一个坑**)
(a)找到你数据存储的目录。
(b)开始遍历处理（规划切片）目录下的每一个文件
(c )遍历第一个文件ss.txt
```
    (a)获取文件大小fs.sizeOf(ss.txt);
    (b)计算切片大小computeSliteSize(Math.max(minSize,Math.min(maxSize,blocksize)))=blocksize=128M
    (c)默认情况下，切片大小=blocksize
    (d)开始切，形成第1个切片：ss.txt—0:128M 第2个切片ss.txt—128:256M 第3个切片ss.txt—256M:300M（每次切片时，都要判断切完剩下的部分是否大于块的1.1倍，不大于1.1倍就划分一块切片）
    (e)将切片信息写到一个切片规划文件中
    (f)整个切片的核心过程在getSplit()方法中完成。
    (g)数据切片只是在逻辑上对输入数据进行分片，并不会再磁盘上将其切分成分片进行存储。InputSplit只记录了分片的元数据信息，比如起始位置、长度以及所在的节点列表等。
    (h)注意：block是HDFS物理上存储的数据，切片是对数据逻辑上的划分。
```
(d)提交切片规划文件到yarn上，yarn上的MrAppMaster就可以根据切片规划文件计算开启maptask个数。

#### 2.FileInputFormat切片机制

##### (1).FileInputFormat中默认的切片机制：

(a)简单地按照文件的内容长度进行切片

(b)切片大小，默认等于block大小

(c)切片时不考虑数据集整体，而是逐个针对每一个文件单独切片

比如待处理数据有两个文件：
```
file1.txt    320M

file2.txt    10M
```
经过FileInputFormat的切片机制运算后，形成的切片信息如下：  
```
file1.txt.split1--  0~128

file1.txt.split2--  128~256

file1.txt.split3--  256~320

file2.txt.split1--  0~10M
```

##### (2).FileInputFormat切片大小的参数配置
(a)通过分析源码，在FileInputFormat中，计算切片大小的逻辑:Math.max(minSize, Math.min(maxSize, blockSize)); 

(b)切片主要由这几个值来运算决定
* mapreduce.input.fileinputformat.split.minsize=1 默认值为1

* mapreduce.input.fileinputformat.split.maxsize= Long.MAXValue 默认值Long.MAXValue,因此，默认情况下，切片大小=blocksize。

* maxsize（切片最大值）：参数如果调得比blocksize小，则会让切片变小，而且就等于配置的这个参数的值。

* minsize（切片最小值）：参数调的比blockSize大，则可以让切片变得比blocksize还大。

##### (3).获取切片信息API
```java
// 根据文件类型获取切片信息
FileSplit inputSplit = (FileSplit) context.getInputSplit();

// 获取切片的文件名称
String name = inputSplit.getPath().getName();
```

#### 3.CombineTextInputFormat切片机制
&nbsp;&nbsp;&nbsp;&nbsp;关于大量小文件的优化策略
##### (1).默认情况下TextInputformat对任务的切片机制是按文件规划切片，不管文件多小，都会是一个单独的切片，都会交给一个maptask，这样如果有大量小文件，就会产生大量的maptask，处理效率极其低下。
##### (2).优化策略
* (a)最好的办法，在数据处理系统的最前端（预处理/采集），将小文件先合并成大文件，再上传到HDFS做后续分析。

* (b)补救措施：如果已经是大量小文件在HDFS中了，可以使用另一种InputFormat来做切片（CombineTextInputFormat），它的切片逻辑跟TextFileInputFormat不同：它可以将多个小文件从逻辑上规划到一个切片中，这样，多个小文件就可以交给一个maptask。
* (c)优先满足最小切片大小，不超过最大切片大小
```txt
CombineTextInputFormat.setMaxInputSplitSize(job, 4194304);// 4m
CombineTextInputFormat.setMinInputSplitSize(job, 2097152);// 2m
举例：0.5m+1m+0.3m+5m=2m + 4.8m=2m + 4m + 0.8m
```

##### (3).具体实现步骤
```java
//  如果不设置InputFormat,它默认用的是TextInputFormat.class
job.setInputFormatClass(CombineTextInputFormat.class)
CombineTextInputFormat.setMaxInputSplitSize(job, 4194304);// 4m
CombineTextInputFormat.setMinInputSplitSize(job, 2097152);// 2m
```
##### (4).案例实操

#### 4.InputFormat接口实现类
&nbsp;&nbsp;&nbsp;&nbsp;MapReduce任务的输入文件一般是存储在HDFS里面。输入的文件格式包括：基于行的日志文件、二进制格式文件等。这些文件一般会很大，达到数十GB，甚至更大。

&nbsp;&nbsp;&nbsp;&nbsp;InputFormat常见的接口实现类包括：TextInputFormat、KeyValueTextInputFormat、NLineInputFormat、CombineTextInputFormat和自定义InputFormat等。

##### (1).TextInputFormat

&nbsp;&nbsp;&nbsp;&nbsp;TextInputFormat是默认的InputFormat。每条记录是一行输入。键是LongWritable类型，存储该行在整个文件中的字节偏移量。值是这行的内容，不包括任何行终止符（换行符和回车符）
&nbsp;&nbsp;&nbsp;&nbsp;以下是一个示例，比如，一个分片包含了如下4条文本记录。
```txt
Rich learning form
Intelligent learning engine
Learning more convenient
From the real demand for more close to the enterprise
```
&nbsp;&nbsp;&nbsp;&nbsp;每条记录表示为以下键/值对：
```txt
(0,Rich learning form)
(19,Intelligent learning engine)
(47,Learning more convenient)
(72,From the real demand for more close to the enterprise)
```
很明显，键并不是行号。一般情况下，很难取得行号，因为文件按字节而不是按行切分为分片。

##### (2).KeyValueTextInputFormat

&nbsp;&nbsp;&nbsp;&nbsp;每一行均为一条记录，被分隔符分割为key，value。可以通过在驱动类中设置conf.set(KeyValueLineRecordReader.KEY_VALUE_SEPERATOR, " ");来设定分隔符。默认分隔符是tab（\t）。
&nbsp;&nbsp;&nbsp;&nbsp;以下是一个示例，输入是一个包含4条记录的分片。其中——>表示一个（水平方向的）制表符
```
line1 ——>Rich learning form
line2 ——>Intelligent learning engine
line3 ——>Learning more convenient
line4 ——>From the real demand for more close to the enterprise
```
&nbsp;&nbsp;&nbsp;&nbsp;每条记录表示为以下键/值对：
```
(line1,Rich learning form)
(line2,Intelligent learning engine)
(line3,Learning more convenient)
(line4,From the real demand for more close to the enterprise)
```
&nbsp;&nbsp;&nbsp;&nbsp;此时的键是每行排在制表符之前的Text序列。
##### (3).NLineInputFormat

&nbsp;&nbsp;&nbsp;&nbsp;如果使用NlineInputFormat，代表每个map进程处理的InputSplit不再按block块去划分，而是按NlineInputFormat指定的行数N来划分。即输入文件的总行数/N=切片数(20)，如果不整除，切片数=商+1。
&nbsp;&nbsp;&nbsp;&nbsp;以下是一个示例，仍然以上面的4行输入为例。
```
Rich learning form
Intelligent learning engine
Learning more convenient
From the real demand for more close to the enterprise
```
&nbsp;&nbsp;&nbsp;&nbsp;例如，如果N是2，则每个输入分片包含两行。开启2个maptask。
```
(0,Rich learning form)
(19,Intelligent learning engine)
```
&nbsp;&nbsp;&nbsp;&nbsp;另一个 mapper 则收到后两行：
```
(47,Learning more convenient)
(72,From the real demand for more close to the enterprise)
```
&nbsp;&nbsp;&nbsp;&nbsp;这里的键和值与TextInputFormat生成的一样。

#### 5.自定义InputFormat
##### (1).概述

(a)自定义一个类继承FileInputFormat

(b)改写RecordReader，实现一次读取一个完整文件封装为KV。

(c )在输出时使用SequenceFileOutPutFormat输出合并文件。

##### (2).案例实操