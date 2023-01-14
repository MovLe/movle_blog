---
title: 'Hadoop之优化'
date: 2019-01-02 15:50:00
tags: Hadoop
categories: Hadoop
---
### 一.MapReduce 跑的慢的原因
Mapreduce 程序效率的瓶颈在于两点：
#### 1.计算机性能
CPU、内存、磁盘健康、网络

#### 2.I/O 操作优化
(1)数据倾斜
(2)map和reduce数设置不合理
(3)map运行时间太长，导致reduce等待过久
(4)小文件过多
(5)大量的不可分块的超大文件
(6)spill次数过多
(7)merge次数过多等。

### 二. MapReduce优化方法
MapReduce优化方法主要从六个方面考虑：数据输入、Map阶段、Reduce阶段、IO传输、数据倾斜问题和常用的调优参数。
#### 1.数据输入
(1)合并小文件：在执行mr任务前将小文件进行合并，大量的小文件会产生大量的map任务，增大map任务装载次数，而任务的装载比较耗时，从而导致mr运行较慢。

(2)采用CombineTextInputFormat来作为输入，解决输入端大量小文件场景。

#### 2 Map阶段
##### (1)减少溢写（spill）次数：
通过调整io.sort.mb及sort.spill.percent参数值，增大触发spill的内存上限，减少spill次数，从而减少磁盘IO。

##### (2)减少合并（merge）次数：
通过调整io.sort.factor参数，增大merge的文件数目，减少merge的次数，从而缩短mr处理时间。

##### (3)在map之后，不影响业务逻辑前提下，先进行combine处理，减少 I/O。

#### 3 Reduce阶段
##### (1)合理设置map和reduce数：
&nbsp;&nbsp;&nbsp;&nbsp;两个都不能设置太少，也不能设置太多。太少，会导致task等待，延长处理时间；太多，会导致 map、reduce任务间竞争资源，造成处理超时等错误。

##### (2)设置map、reduce共存：
&nbsp;&nbsp;&nbsp;&nbsp;调整slowstart.completedmaps参数，使map运行到一定程度后，reduce也开始运行，减少reduce的等待时间。

##### (3)规避使用reduce：
&nbsp;&nbsp;&nbsp;&nbsp;因为reduce在用于连接数据集的时候将会产生大量的网络消耗。

##### (4)合理设置reduce端的buffer：
&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，数据达到一个阈值的时候，buffer中的数据就会写入磁盘，然后reduce会从磁盘中获得所有的数据。也就是说，buffer和reduce是没有直接关联的，中间多个一个写磁盘->读磁盘的过程，既然有这个弊端，那么就可以通过参数来配置，使得buffer中的一部分数据可以直接输送到reduce，从而减少IO开销：mapred.job.reduce.input.buffer.percent，默认为0.0。当值大于0的时候，会保留指定比例的内存读buffer中的数据直接拿给reduce使用。这样一来，设置buffer需要内存，读取数据需要内存，reduce计算也要内存，所以要根据作业的运行情况进行调整。

#### 4 IO传输
(1)采用数据压缩的方式，减少网络IO的的时间。安装Snappy和LZO压缩编码器。

(2)使用SequenceFile二进制文件。

#### 5 数据倾斜问题
##### (1)数据倾斜现象

* 数据频率倾斜——某一个区域的数据量要远远大于其他区域。
* 数据大小倾斜——部分记录的大小远远大于平均值。

##### (2)如何收集倾斜数据
在reduce方法中加入记录map输出键的详细情况的功能。
```java
public static final String MAX_VALUES = "skew.maxvalues"; 
private int maxValueThreshold; 
 
@Override
public void configure(JobConf job) { 
     maxValueThreshold = job.getInt(MAX_VALUES, 100); 
} 
@Override
public void reduce(Text key, Iterator<Text> values,
                     OutputCollector<Text, Text> output, 
                     Reporter reporter) throws IOException {
     int i = 0;
     while (values.hasNext()) {
         values.next();
         i++;
     }

     if (++i > maxValueThreshold) {
         log.info("Received " + i + " values for key " + key);
     }
}
```

##### (3)减少数据倾斜的方法
* 方法1：抽样和范围分区
可以通过对原始数据进行抽样得到的结果集来预设分区边界值。

* 方法2：自定义分区
基于输出键的背景知识进行自定义分区。例如，如果map输出键的单词来源于一本书。且其中某几个专业词汇较多。那么就可以自定义分区将这这些专业词汇发送给固定的一部分reduce实例。而将其他的都发送给剩余的reduce实例。

* 方法3：Combine
使用Combine可以大量地减小数据倾斜。在可能的情况下，combine的目的就是聚合并精简数据。

* 方法4：采用Map Join，尽量避免Reduce Join。

#### 6 常用的调优参数
##### (1)资源相关参数
###### (a).以下参数是在用户自己的mr应用程序中配置就可以生效（mapred-default.xml）

|配置参数|参数说明|
|:-:|:-:|
|mapreduce.map.memory.mb|一个Map Task可使用的资源上限（单位:MB），默认为1024。如果Map Task实际使用的资源量超过该值，则会被强制杀死。|
|mapreduce.reduce.memory.mb|一个Reduce Task可使用的资源上限（单位:MB），默认为1024。如果Reduce Task实际使用的资源量超过该值，则会被强制杀死。|
|mapreduce.map.cpu.vcores|每个Map task可使用的最多cpu core数目，默认值: 1|
|mapreduce.reduce.cpu.vcores|每个Reduce task可使用的最多cpu core数目，默认值: 1|
|mapreduce.reduce.shuffle.parallelcopies|每个reduce去map中拿数据的并行数。默认值是5|
|mapreduce.reduce.shuffle.merge.percent|buffer中的数据达到多少比例开始写入磁盘。默认值0.66|
|mapreduce.reduce.shuffle.input.buffer.percent|buffer大小占reduce可用内存的比例。默认值0.7|
|mapreduce.reduce.input.buffer.percent|指定多少比例的内存用来存放buffer中的数据，默认值是0.0|

###### (b).应该在yarn启动之前就配置在服务器的配置文件中才能生效（yarn-default.xml）

|配置参数|参数说明|
|:-:|:-:|
|yarn.scheduler.minimum-allocation-mb	  1024|给应用程序container分配的最小内存|
|yarn.scheduler.maximum-allocation-mb	  8192|给应用程序container分配的最大内存|
|yarn.scheduler.minimum-allocation-vcores	1|每个container申请的最小CPU核数|
|yarn.scheduler.maximum-allocation-vcores	32|每个container申请的最大CPU核数|
|yarn.nodemanager.resource.memory-mb   8192|给containers分配的最大物理内存|

###### (c ).shuffle性能优化的关键参数，应在yarn启动之前就配置好（mapred-default.xml）

|配置参数|参数说明|
|:-:|:-:|
|mapreduce.task.io.sort.mb   100|shuffle的环形缓冲区大小，默认100m|
|mapreduce.map.sort.spill.percent   0.8|环形缓冲区溢出的阈值，默认80%|

##### (2)容错相关参数(mapreduce性能优化)

|配置参数|参数说明|
|:-:|:-:|
|mapreduce.map.maxattempts|每个Map Task最大重试次数，一旦重试参数超过该值，则认为Map Task运行失败，默认值：4。|
|mapreduce.reduce.maxattempts|每个Reduce Task最大重试次数，一旦重试参数超过该值，则认为Map Task运行失败，默认值：4。|
|mapreduce.task.timeout|Task超时时间，经常需要设置的一个参数，该参数表达的意思为：如果一个task在一定时间内没有任何进入，即不会读取新的数据，也没有输出数据，则认为该task处于block状态，可能是卡住了，也许永远会卡主，为了防止因为用户程序永远block住不退出，则强制设置了一个该超时时间（单位毫秒），默认是600000。如果你的程序对每条输入数据的处理时间过长（比如会访问数据库，通过网络拉取数据等），建议将该参数调大，该参数过小常出现的错误提示是“AttemptID:attempt_14267829456721_123456_m_000224_0 Timed out after 300 secsContainer killed by the ApplicationMaster.”|

### 三. HDFS小文件优化方法
#### 1 HDFS小文件弊端
&nbsp;&nbsp;&nbsp;&nbsp;HDFS上每个文件都要在namenode上建立一个索引，这个索引的大小约为150byte，这样当小文件比较多的时候，就会产生很多的索引文件，一方面会大量占用namenode的内存空间，另一方面就是索引文件过大是的索引速度变慢。

#### 2 解决方案
##### (1)Hadoop Archive:
&nbsp;&nbsp;&nbsp;&nbsp;是一个高效地将小文件放入HDFS块中的文件存档工具，它能够将多个小文件打包成一个HAR文件，这样就减少了namenode的内存使用。

##### (2)Sequence file：
&nbsp;&nbsp;&nbsp;&nbsp;sequence file由一系列的二进制key/value组成，如果key为文件名，value为文件内容，则可以将大批小文件合并成一个大文件。

##### (3)CombineFileInputFormat：
&nbsp;&nbsp;&nbsp;&nbsp;CombineFileInputFormat是一种新的inputformat，用于将多个文件合并成一个单独的split，另外，它会考虑数据的存储位置。

##### (4)开启JVM重用
&nbsp;&nbsp;&nbsp;&nbsp;对于大量小文件Job，可以开启JVM重用会减少45%运行时间。
&nbsp;&nbsp;&nbsp;&nbsp;JVM重用理解：一个map运行一个jvm，重用的话，在一个map在jvm上运行完毕后，jvm继续运行其他map。
&nbsp;&nbsp;&nbsp;&nbsp;具体设置：mapreduce.job.jvm.numtasks值在10-20之间。
