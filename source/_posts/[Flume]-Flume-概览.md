---
title: '[Flume]-Flume概览'
date: 2019-07-03 11:00:00
tags: Flume
categories: Flume
---

### 一.flume概览
#### 1.概述:
&nbsp;&nbsp;&nbsp;&nbsp;Flume是一种分布式，可靠且可用的服务，用于有效地收集，聚合和移动大量日志数据。它具有基于流数据流的简单灵活的架构。它具有可靠的可靠性机制和许多故障 转移和恢复机制，具有强大的容错性。它使用简单的可扩展数据模型，允许在线分析应用程序。

#### 2.大数据架构

* 数据采集(爬虫\日志数据\flume) 
* 数据存储(hdfs/hive/hbase(nosql)) 
* 数据计算(mapreduce/hive/sparkSQL/sparkStreaming/flink) 
* 数据可视化

#### 3.Flume基于流式架构，容错性强，也很灵活简单。

#### 4.Flume、Kafka用来实时进行数据收集，Spark、Flink用来实时处理数据，impala用来实时查询。
### 二.flume角色

#### 1.source

* 数据源，用户采集数据，source产生数据流，同时会把产生的数据流传输到channel。

#### 2.channel

* 传输通道，用于桥接source和sink

#### 3.sink

* 下沉，用于收集channel传输的数据，将数据源传递到目标源

#### 4.event

* 在flume中使用事件作为传输的基本单元

![Flume角色](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdlZGQwZjAxNmUwNzAzNGMucG5n?x-oss-process=image/format,png)

#### 5.Flume常用的Type
##### (1)source

|名称|	含义|注意点|
|:-:|:-:|:-:|
|avro|avro协议的数据源|主要用于agent to agent之间的连接|	
|exec	|unix命令|可以命令监控文件 tail -F|
|spooldir|监控一个文件夹|不能含有子文件夹，不监控windows文件夹,处理完文件不能再写数据到文件 ,文件名不能冲突|
|TAILDIR	|既可以监控文件也可以监控文件夹|支持断点续传功能， 重点使用这个|
|netcat	|监听某个端口	||
|kafka	|监控卡夫卡数据||

##### (2)sink

|名称	|含义|	注意点|
|:-:|:-:|:-:|
|kafka|写到kafka中||	
|HDFS|将数据写到HDFS中||	
|logger|输出到控制台||	
|avro|	avro协议|	配合avro source使用|

##### (3)channel:

|名称	|含义	|注意点|
|---|---|---|
|memory|	存在内存中	||
|kafka|将数据存到kafka中||	
|file|	存在本地磁盘文件中||

#### 6.flume的启动参数
##### (1)命令

|参数|	描述|
|:-:|:-:|
|help|打印帮助信息|
|agent|运行一个Flume Agent|
|avro-client|运行一个Avro Flume 客户端|
|version|	显示Flume版本。

##### (2)全局选项

|参数|	描述|
|:-:|:-:|
|--conf,-c <conf>|	在<conf>目录使用配置文件。指定配置文件放在什么目录|
|--classpath,-C <cp>|	追加一个classpath|
|--dryrun,-d	|不真正运行Agent，而只是打印命令一些信息。|
|--plugins-path <dirs>|	插件目录列表。默认：$FLUME_HOME/plugins.d|
|-Dproperty=value	 |设置一个JAVA系统属性值。|
|-Xproperty=value	 |设置一个JAVA -X的选项。|

##### (3)Agent选项

|参数|描述|
|:-:|:-:|
|--conf-file ,-f &lt;file&gt;|	指定配置文件，这个配置文件必须在全局选项的--conf参数定义的目录下。（必填）|
|--name,-n &lt;name&gt;|	Agent的名称（必填）|
|--help,-h|	帮助|

日志相关：
* -Dflume.root.logger=INFO,console  

该参数将会把flume的日志输出到console,为了将其输出到日志文件(默认在FLUME_HOME/logs),可以将console改为LOGFILE形式,具体的配置可以修改$FLUME_HOME/conf/log4j.properties

* -Dflume.log.file=./wchatAgent.logs   

该参数直接输出日志到目标文件

##### (4)Avro客户端选项

|参数|	描述|
|:-:|:-:|
|--rpcProps,-P <file>|连接参数的配置文件。|
|--host,-H <host>	|Event所要发送到的Hostname。|
|--port,-p <port>	|Avro Source的端口。|
|--dirname <dir>	|Avro Source流到达的目录。|
|--filename,-F <file>	|Avro Source流到达的文件名。|
|--headerFile,-R <file>|  设置一个JAVA -X的选项。|

启动Avro客户端要么指定--rpcProps，要么指定--host和--port

### 三.Flume传输过程：

&nbsp;&nbsp;&nbsp;&nbsp;source监控某个文件或数据流，数据源产生新的数据，拿到该数据后，将数据封装在一个Event中，并put到channel后commit提交，channel队列先进先出，sink去channel队列中拉取数据，然后写入到HDFS中。