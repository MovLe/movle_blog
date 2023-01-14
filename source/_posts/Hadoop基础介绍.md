---
title: 'Hadoop基础介绍'
date: 2019-01-01 09:49:00
tags: Hadoop
categories: Hadoop
---

### 一.Hadoop概述
#### 1.Hadoop的优势

* 高可靠性：
因为Hadoop假设计算元素和存储会出现故障，因为它维护多个工作数据副本，在出现故障时可以对失败的节点重新分布处理。

* 高扩展性：
在集群间分配任务数据，可方便的扩展数以千计的节点。

* 高效性：
在MapReduce的思想下，Hadoop是并行工作的，以加快任务处理速度。

* 高容错性：
自动保存多份副本数据，并且能够自动将失败的任务重新分配。

#### 2.Hadoop组成

![hadoop组成](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFmNmRlYjRkNmZmYTA2OWYucG5n?x-oss-process=image/format,png)

* Hadoop HDFS：Hadoop Distributed File System
    一个高可靠、高吞吐量的分布式文件系统。
* Hadoop MapReduce：
一个分布式的离线并行计算框架。
* Hadoop YARN：
作业调度与集群资源管理的框架。
* Hadoop Common：
支持其他模块的工具模块（Configuration、RPC、序列化机制、日志操作）。



#### 3.YARN架构概述
##### (1)ResourceManager(rm)：
处理客户端请求、启动/监控ApplicationMaster、监控NodeManager、资源分配与调度

##### (2)NodeManager(nm)：
单个节点上的资源管理、处理来自ResourceManager的命令、处理来自ApplicationMaster的命令

##### (3)ApplicationMaster：
数据切分、为应用程序申请资源，并分配给内部任务、任务监控与容错

##### (4)Container：
对任务运行环境的抽象，封装了CPU、内存等多维资源以及环境变量、启动命令等任务运行相关的信息

#### 4.MapReduce架构概述
##### (1)MapReduce将计算过程分为两个阶段：Map和Reduce
* Map阶段并行处理输入数据
* Reduce阶段对Map结果进行汇总



### 二.Hadoop
#### 1.hadoop启动方式：
##### (1)启动hdfs集群：

```shell
start-dfs.sh
```
##### (2)启动yarn集群：

```shell
start-yarn.sh
```
##### (3)启动hadoop集群：

```shell
start-all.sh
```

**Hadoop启动和停止命令：**
以下命令都在$HADOOP_HOME/sbin下，如果直接使用，记得配置环境变量

|作用|命令|
|-|-|
|启动/停止历史服务器|mr-jobhistory-daemon.sh start\|stop historyserver|
|启动/停止总资源管理器|yarn-daemon.sh start\|stop resourcemanager|
|启动/停止节点管理器|yarn-daemon.sh start\|stop nodemanager
|启动/停止 NN 和 DN|start\|stop-dfs.sh|
|启动/停止 RN 和 NM|start\|stop-yarn.sh|
|启动/停止 NN、DN、RN、NM|start\|stop-all.sh|
|启动/停止 NN|hadoop-daemon.sh start\|stop namenode|
|启动/停止 DN|hadoop-daemon.sh start\|stop datanode|

#### 2.namenode工作机制
![namenode工作机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRjNjQxODliOWQ2NDQ5MmMucG5n?x-oss-process=image/format,png)

##### (1)触发checkpoint的条件
* 定时的时间
* edits中数据已满

##### (2)配置checkpoint时间(hdfs-site.xml)：

```xml
<property>
    <name>dfs.namenode.checkpoint.period</name>
    <value>7200</value>
</property>
```
#### 3.datanode工作机制
![datanode工作机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZkZTU3OWExYTkyMTNjZTkucG5n?x-oss-process=image/format,png)


#### 4.Hadoop-MapReduce
##### (1)MapReduce分布式计算程序的编程框架。基于hadoop的数据分析的应用。
##### (2)MR优点：
* 框架易于编程
* 可靠容错（集群）
* 可以处理海量数据(T+ PB+)
* 拓展性，可以通过动态的增减节点来拓展计算能力

#### 5.MapReduce的思想
例如有数据:海量单词 

```txt
hello reba 
hello mimi 
hello liya
mimi big
```
解决方式：
##### (1)每个单词记录一次(map阶段)
&lt;hello,1&gt; &lt;reba,1&gt; &lt;hello,1&gt; &lt;mimi,1&gt;
##### (2)相同单词的key不变，value累加求和即可(reduce阶段) 
&lt;hello,1+1+1&gt;

**mapreduce思想**：
![mapreduce](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRlZTRhNTg0NDRjYTk3N2UucG5n?x-oss-process=image/format,png)

#### 6.MapReduce程序：
命令格式：
* hadoop jar 程序压缩包 函数名称 函数参数

```shell
hadoop jar hadoop-mapreduce-examples-2.8.4.jar wordcount /in/a.txt /out         //运行wordcount函数，计算HDFS中/in/a.txt文件中单词计数 ，将结果保存在/out目录下
```
#### 7.Hadoop数据类型：
##### (1)我们看到的wordcount程序中的泛型中的数据类型其实是hadoop的序列化的数据类型。
##### (2)为什么要进行序列化?用java的类型行不行?
* Java的序列化:Serliazable太重。 hadoop自己开发了一套序列化机制。Writable，精简高效。海量数据。
##### (3)Hadoop序列化类型与Java数据类型


| Java数据类型 | Hadoop序列化类型 |
| --- | --- |
| int | IntWritable |
| long | LongWritable |
| boolean | BooleanWritable |
| byte | ByteWritable |
| double | DoubleWritable |
| String | Text |
| float | FloatWritable |

#### 8.两种模式测试wordcount程序
* 本地模式：需要在本地搭建Hadoop（winutils 见另一篇文章）
* 集群模式：将在windows写好的wordcount程序打包上传到集群测试运行。

##### (1)Maptask决定机制：

![maptask决定机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTljMTVkY2YyZDBmMWFhM2IucG5n?x-oss-process=image/format,png)

#### 9.Yarn
##### (1)Yarn工作流程
![yarn工作流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBkYTE1YzE3ZDA5NjM4YWIucG5n?x-oss-process=image/format,png)

##### (2)yarn架构介绍

![yarn架构介绍](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI4MzBlZDczMjhhNjQ1NDQucG5n?x-oss-process=image/format,png)

#### 10.小文件优化
##### (1)如果企业中存在海量的小文件数据 TextInputFormat按照文件规划切片，文件不管多小都是一个单独的切片，启动mapt ask任务去执行，这样会产生大量的maptask，浪费资源。
##### (2)优化手段:
小文件合并大文件，如果不动这个小文件内容。
#### 11.分区(实例程序IDEAlogs1)
##### (1)总结:
1)自定义类继承partitioner<key,value> 
2)重写方法getPartition()
3)业务逻辑
4)在driver类中加入setPartitionerClass 
5)注意:需要指定setNumReduceTasks(个数=分区数+1)

#### 12.排序(实例程序logs2)
需求:每个分区内进行排序?
总结: 1)实现WritableComparable接口 2)重写compareTo方法
combineTextInputFormat设置切片的大小 maptask
#### 13.mapreduce流程

![mapreduce流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg0YjEyOGM5MGQ0ZWUwYjEucG5n?x-oss-process=image/format,png)

#### 14.combiner合并
##### (1)combiner是一个组件 
* 注意:是Mapper和Reducer之外的一种组件 但是这个组件的父类是Reduer
##### (2)如果想使用combiner继承Reduer即可
##### (3)通过编写combiner发现与reducer代码相同 只需在driver端指定 setCombinerClass(WordCountReduer.class)
* 注意:前提是不能影响业务逻辑

```shell
<a,1><c,1> <a,2><a,1> = <a,3> 
```
数学运算: (3 + 5 + 7)/3 = 5
        (2 + 6)/2 = 4
不进行局部累加:(3 + 5 + 7 + 2 + 6)/5 = 23/5 
进行了局部累加:(5+4)/2 = 9/2=4.5 不等于 23/5=4.6
#### 15.shuffle机制
![shuffle机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJkZTVlZTdiMWMyOWI4MjUucG5n?x-oss-process=image/format,png)

#### 16.数据压缩
##### (1)为什么对数据进行压缩? 
* mapreduce操作需要对大量数据进行传输
* 压缩技术有效的减少底层存储系统读写字节数，hdfs。 
* 压缩提高网络带宽和磁盘空间效率。
* 数据压缩节省资源，减少网络I/O。 
* 通过压缩可以影响到mapreduce性能。(小文件优化，combiner)代码角度进行优化。

注意:利用好压缩提高性能，运用不好会降低性能。 压缩 -> 解压缩
##### (2)mapreduce常用的压缩编码

| 压缩格式 | 是否需要安装 | 文件拓展名 | 是否可以切分 |
| --- | --- | --- | --- |
| DEFAULT | 直接使用 | .deflate | 否 |
| bzip2 | 直接使用 | .bz2 | 是 |
| Gzip | 直接使用 | .gz | 否 |
| LZO | 需要安装 | .lzo | 是 |
| Snappy | 需要安装 | .snappy | 否 |

##### (3)性能测试：

| 压缩格式 | 源文件大小 | 压缩后大小 | 压缩速度 | 解压速度 |
| --- | --- | --- | --- | --- |
| gzip | 8.3GB | 1.8GB | 20MB/s | 60MB/s |
| LZO | 8.3GB | 3GB | 50MB/s | 70MB/s |
| bzip2 | 8.3GB | 1.1GB | 3MB/s | 10MB/s |
##### (4)数据压缩发生的阶段
###### (a)数据源
&nbsp;&nbsp;&nbsp;&nbsp;->数据传输，可以进行数据压缩
###### (b)mapper阶段
&nbsp;&nbsp;&nbsp;&nbsp;mapper端输出压缩
&nbsp;&nbsp;&nbsp;&nbsp;->数据传输，可以进行压缩（一般数据源到mapper的压缩与Mapper此处压缩的效率是差不多的， 选择在mapper压缩较好 ）
###### (c )reducer阶段
&nbsp;&nbsp;&nbsp;&nbsp;reduce端输出压缩
&nbsp;&nbsp;&nbsp;&nbsp;->数据传输，可以进行数据压缩
##### (d)结果数据
* 设置map端输出压缩

```shell
conf.setBoolean("mapreduce.map.output.compress",true);      //开启压缩
conf.setClass("mapreduce.map.output.compress.codec",BZip2Codec.class,CompressionCodec.class);  //设置压缩方式，压缩编码
```
* 设置reduce端输出压缩：

```shell
FileOutputFormat.setCompressOutput(job,true);   //设置reduce端输出压缩

FileOutputFormat.setOutputCompressorClass(job,BZip2Codec.class);    //设置压缩方式
```


##### (5)压缩编码使用的场景
###### (a).Gzip压缩方式
* 压缩率比较高，并且压缩解压缩速度很快;
* hadoop自身支持的压缩方式，用gzip格式处理数据就像直接处理文本数据是完全一样的;
* 在linux系统自带gzip命令，使用很方便简洁。
* 不支持split 使用每个文件压缩之后大小需要在128M以下(块大小) 200M->设置块大小

###### (b).LZO压缩方式
* 压缩解压速度比较快并且，压缩率比较合理;
* 支持split 在linux系统不可以直接使用，但是可以进行安装。
* 压缩率比gzip和bzip2要弱，hadoop本身不支持。 需要安装。

###### (c ).Bzio2压缩方式
* 支持压缩，具有很强的压缩率。hadoop本身支持。
* linux中可以安装。
* 压缩解压缩速度很慢。

###### (d).Snappy压缩方式
* 压缩解压缩速度很快。而且有合理的压缩率。 
* 不支持split

#### 17.数据倾斜
##### (1)如何规避数据倾斜：
* 在Map阶段之前就将数据合并

#### 18.优化-MapReduce程序的编写过程中考虑的问题
##### (1)优化目的：
* 提高程序运行的效率

##### (2)优化方案：如何优化MR程序
影响MR程序的因素：
###### (a).硬件
* 提升硬件(CPU/磁盘(固态、机械)/内存/网络...)

###### (b).I/O优化：传输
* maptask与reducetask合理设置个数
* 数据倾斜(reducetask-》merge)：避免出现数据倾斜
* 大量小文件情况 (combineTextInputFormat)
* combiner优化(不影响业务逻辑)

##### (3)具体优化方式：MR(数据接入、Map、Reduce、IO传输、处理倾斜、参数优化)
###### (a).数据接入：
* 解决方式：小文件的话 进行合并 ，namenode存储元数据信息，sn

###### (b).Map:会发生溢写，如果减少溢写次数也能达到优化,溢写内存增加这样就减少了溢写次数
* 解决方式：修改mapred-site.xml

```shell
mapreduce.task.io.sort.mb     // 100,调大优化

mapreduce.map.sort.spill.percent    //0.8，调大优化
```
###### (c).combiner:
* 解决方式：map后优化

###### (d).reduce:
* reduceTask设置合理的个数
* 写mr程序可以合理避免写reduce阶段 
* 设置map/reduce共存,修改配置文件:mapred-site.xml

```shell
mapreduce.job.reduce.slowstart.completedmaps //默认时0.05，减少它即优化
```
###### (e).I/O传输：
* 解决方式：压缩

###### (f).数据倾斜：
* map端合并。手动的对数据进行分段处理，合理的分区。

##### (4)其他优化方式-非程序：
###### (a).JVM重用：不关JVM 一个map运行一个jvm,开启重用，在运行完这个map后JVM继续运行其它map。 例如线程池
* 修改属性

```shell
mapreduce.job.jvm.numtasks    //20,设置合理的数值，能减少40%左右的运行时间·
```

#### 19.大数据算法：(见其他文件)
##### (1)冒泡排序
##### (2)双冒泡排序
##### (3)快速排序
##### (4)归并排序