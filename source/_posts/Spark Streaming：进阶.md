---
title: 'Spark Streaming：进阶'
date: 2020-03-10 18:00:00
tags: 
- Spark
- Spark Streaming
categories: Spark
---

### 一.StreamingContext对象详解

#### 1.初始化StreamingContext
##### (1)方式一：从SparkConf对象中创建
```java
val conf = new SparkConf().setAppName("MyNetworkWordCount").setMaster("local[2]")

val src = new StreamContext(conf, Second(5))
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRiYTQ3OGExMDI5OGQzNGQucG5n?x-oss-process=image/format,png)

##### (2)方式二:从一个现有的SparkContext实例中创建
```java
scala >import org.apache.spark.streaming.{Second,StreamingContext}

scala >val ssc=new StreamContext(sc,Second(1))
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc5NmU2MDcwYzRkNjgyMzAucG5n?x-oss-process=image/format,png)



#### 2.程序中的几点说明：
* appName参数是应用程序在集群UI上显示的名称。 

* master是Spark，Mesos或YARN集群的URL，或者一个特殊的“local [*]”字符串来让程序以本地模式运行。

* 当在集群上运行程序时，不需要在程序中硬编码master参数，而是使用spark-submit提交应用程序并将master的URL以脚本参数的形式传入。但是，对于本地测试和单元测试，您可以通过“local[*]”来运行Spark Streaming程序(请确保本地系统中的cpu核心数够用) 

* StreamingContext会内在的创建一个SparkContext的实例(所有Spark功能的起始点)，你可以通过ssc.sparkContext访问到这个实例。

* 批处理的时间窗口长度必须根据应用程序的延迟要求和可用的集群资源进行设置。

#### 3.请务必记住以下几点：
* 一旦一个StreamingContextt开始运作，就不能设置或添加新的流计算。

* 一旦一个上下文被停止，它将无法重新启动。

* 同一时刻，一个JVM中只能有一个StreamingContext处于活动状态。

* StreamingContext上的stop()方法也会停止SparkContext。 要仅停止StreamingContext(保持SparkContext活跃)，请将stop() 方法的可选参数stopSparkContext设置为false。

* 只要前一个StreamingContext在下一个StreamingContext被创建之前停止(不停止SparkContext)，SparkContext就可以被重用来创建多个StreamingContext。

### 二.离散流(DStreams)：Discretized Streams

#### 1.DiscretizedStream或DStream 是Spark Streaming对流式数据的基本抽象。
它表示连续的数据流，这些连续的数据流可以是从数据源接收的输入数据流，也可以是通过对输入数据流执行转换操作而生成的经处理的数据流。在内部，DStream由一系列连续的RDD表示，如下图：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRkZTFhNTM3NmM1N2VjNzQucG5n?x-oss-process=image/format,png)

#### 2.举例分析：
在之前的NetworkWordCount的例子中，我们将一行行文本组成的流转换为单词流，具体做法为：将flatMap操作应用于名为lines的 DStream中的每个RDD上，以生成words DStream的RDD。如下图所示：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWIzODg1YTcxNjBlMWM3YWIucG5n?x-oss-process=image/format,png)



但是DStream和RDD也有区别，下面画图说明：

![RDD的结构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYyYTM4MjcyMjA5YWY2MWUucG5n?x-oss-process=image/format,png)

![DStream的结构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE5Yjg5NDQ2OGIyMWRiNWYucG5n?x-oss-process=image/format,png)


### 三.DStream中的转换操作(transformation)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFmMzQxZDU2ODhhMjViMTAucG5n?x-oss-process=image/format,png)


最后两个transformation算子需要重点介绍一下：

#### 1.transform(func)

(1)通过RDD-to-RDD函数作用于源DStream中的各个RDD，可以是任意的RDD操作，从而返回一个新的RDD

(2)举例：在NetworkWordCount中，也可以使用transform来生成元组对

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFhMTQ2MzU4YzQ1MTZkYWIucG5n?x-oss-process=image/format,png)



#### 2.updateStateByKey(func)
##### (1)操作允许不断用新信息更新它的同时保持任意状态。
* 定义状态-状态可以是任何的数据类型
* 定义状态更新函数-怎样利用更新前的状态和从输入流里面获取的新值更新状态

##### (2)重写NetworkWordCount程序，累计每个单词出现的频率(注意：累计)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg0MzAxMDYyYjIxODBkYWEucG5n?x-oss-process=image/format,png)

##### (3)输出结果：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJkMDA2OGFkYzNhODAwNzMucG5n?x-oss-process=image/format,png)



#### 3.注意：
如果在IDEA中，不想输出log4j的日志信息，可以将log4j.properties文件(放在src的目录下)的第一行改为：
```shell
log4j.rootCategory=ERROR,console
```

### 四.窗口操作
&nbsp;&nbsp;&nbsp;&nbsp;Spark Streaming还提供了窗口计算功能，允许您在数据的滑动窗口上应用转换操作。下图说明了滑动窗口的工作方式：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNmYTEzZWJiMWQ5ZDRlMjcucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;如图所示，每当窗口滑过originalDStream时，落在窗口内的源RDD被组合并被执行操作以产生windowed DStream的RDD。在上面的例子中，操作应用于最近3个时间单位的数据，并以2个时间单位滑动。这表明任何窗口操作都需要指定两个参数。
* 窗口长度(windowlength) - 窗口的时间长度(上图的示例中为：3)。
* 滑动间隔(slidinginterval) - 两次相邻的窗口操作的间隔(即每次滑动的时间长度)(上图示例中为：2)。

&nbsp;&nbsp;&nbsp;&nbsp;这两个参数必须是源DStream的批间隔的倍数(上图示例中为：1)。

&nbsp;&nbsp;&nbsp;&nbsp;我们以一个例子来说明窗口操作。 假设您希望对之前的单词计数的示例进行扩展，每10秒钟对过去30秒的数据进行wordcount。为此，我们必须在最近30秒的pairs DStream数据中对(word, 1)键值对应用reduceByKey操作。这是通过使用reduceByKeyAndWindow操作完成的。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQxNGI2YzllYWY4OWZmOGMucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;一些常见的窗口操作如下表所示。所有这些操作都用到了上述两个参数 - windowLength和slideInterval。

##### (1)window(windowLength, slideInterval)
* 基于源DStream产生的窗口化的批数据计算一个新的DStream

##### (2)countByWindow(windowLength, slideInterval)
* 返回流中元素的一个滑动窗口数

##### (3)reduceByWindow(func, windowLength, slideInterval)
* 返回一个单元素流。利用函数func聚集滑动时间间隔的流的元素创建这个单元素流。函数必须是相关联的以使计算能够正确的并行计算。


##### (4)reduceByKeyAndWindow(func, windowLength, slideInterval, [numTasks])
* 应用到一个(K,V)对组成的DStream上，返回一个由(K,V)对组成的新的DStream。每一个key的值均由给定的reduce函数聚集起来。注意：在默认情况下，这个算子利用了Spark默认的并发任务数去分组。你可以用numTasks参数设置不同的任务数

##### (5)reduceByKeyAndWindow(func, invFunc, windowLength, slideInterval, [numTasks])
* 上述reduceByKeyAndWindow() 的更高效的版本，其中使用前一窗口的reduce计算结果递增地计算每个窗口的reduce值。这是通过对进入滑动窗口的新数据进行reduce操作，以及“逆减(inverse reducing)”离开窗口的旧数据来完成的。一个例子是当窗口滑动时对键对应的值进行“一加一减”操作。但是，它仅适用于“可逆减函数(invertible reduce functions)”，即具有相应“反减”功能的减函数(作为参数invFunc)。 像reduceByKeyAndWindow一样，通过可选参数可以配置reduce任务的数量。 请注意，使用此操作必须启用检查点。

##### (6)countByValueAndWindow(windowLength, slideInterval, [numTasks])
* 应用到一个(K,V)对组成的DStream上，返回一个由(K,V)对组成的新的DStream。每个key的值都是它们在滑动窗口中出现的频率。


### 五.输入DStreams和接收器
#### 1.输入DStreams表示从数据源获取输入数据流的DStreams。
在NetworkWordCount例子中，lines表示输入DStream，它代表从netcat服务器获取的数据流。每一个输入流DStream和一个Receiver对象相关联，这个Receiver从源中获取数据，并将数据存入内存中用于处理。
**输入DStreams表示从数据源获取的原始数据流**
#### 2.Spark Streaming拥有两类数据源：
##### (1)基本源(Basic sources)：这些源在StreamingContext API中直接可用。例如文件系统、套接字连接、Akka的actor等
##### (2)高级源(Advanced sources)：这些源包括Kafka,Flume,Kinesis,Twitter等等。

#### 3.下面通过具体的案例，详细说明：

##### (1)文件流：通过监控文件系统的变化，若有新文件添加，则将它读入并作为数据流

需要注意的是：
* 这些文件具有相同的格式
* 这些文件通过原子移动或重命名文件的方式在dataDirectory创建
* 如果在文件中追加内容，这些追加的新数据也不会被读取。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE2NzIwMWM2MTdlNmY2ZmUucG5n?x-oss-process=image/format,png)


注意：要演示成功，需要在原文件中编辑，然后拷贝一份。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBhMWI0NTI0NjI5N2YzYjMucG5n?x-oss-process=image/format,png)

##### (2)RDD队列流
&nbsp;&nbsp;&nbsp;&nbsp;使用streamingContext.queueStream(queueOfRDD)创建基于RDD队列的DStream，用于调试Spark Streaming应用程序。
```
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMyY2ZhMWM5MTZhYjMzMTUucG5n?x-oss-process=image/format,png)


##### (3)套接字流：通过监听Socket端口来接收数据

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ4NzY3OTRhOWE3NTdkZDcucG5n?x-oss-process=image/format,png)

### 六.DStreams的输出操作
#### 1.输出操作允许DStream的操作推到如数据库、文件系统等外部系统中。
因为输出操作实际上是允许外部系统消费转换后的数据，它们触发的实际操作是DStream转换。
#### 2.目前，定义了下面几种输出操作：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg4NzBlZWUxNzk2NjI2NmEucG5n?x-oss-process=image/format,png)

##### (1)foreachRDD的设计模式

DStream.foreachRDD是一个强大的原语，发送数据到外部系统中。

###### (a)第一步：创建连接，将数据写入外部数据库(使用之前的NetworkWordCount，改写之前输出结果的部分，如下)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM3MTAzNDAxNDI4ZjkyNzkucG5n?x-oss-process=image/format,png)

出现以下Exception：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRkMmUwOTJhZTM0MWQ2ZmYucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;原因是：Connection对象不是一个可被序列化的对象，不能RDD的每个Worker上运行；即：Connection不能在RDD分布式环境中的每个分区上运行，因为不同的分区可能运行在不同的Worker上。所以需要在每个RDD分区上单独创建Connection对象。

###### (b)第二步：在每个RDD分区上单独创建Connection对象，如下：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRiZjQxMGExOTk0OTgwMTkucG5n?x-oss-process=image/format,png)

### 七.DataFrame和SQL操作
&nbsp;&nbsp;&nbsp;&nbsp;可以很方便地使用DataFrames和SQL操作来处理流数据。必须使用当前的StreamingContext对应的SparkContext创建一个SparkSession。此外，必须这样做的另一个原因是使得应用可以在driver程序故障时得以重新启动，这是通过创建一个可以延迟实例化的单例SparkSession来实现的。

&nbsp;&nbsp;&nbsp;&nbsp;在下面的示例中，使用DataFrames和SQL来修改之前的wordcount示例并对单词进行计数。我们将每个RDD转换为DataFrame，并注册为临时表，然后在这张表上执行SQL查询。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ3ZDBjMDcwY2QwZjVkYjAucG5n?x-oss-process=image/format,png)


### 八.缓存/持久化
&nbsp;&nbsp;&nbsp;&nbsp;与RDD类似，DStreams还允许开发人员将流数据保留在内存中。也就是说，在DStream上调用persist() 方法会自动将该DStream的每个RDD保留在内存中。如果DStream中的数据将被多次计算(例如，相同数据上执行多个操作)，这个操作就会很有用。对于基于窗口的操作，如reduceByWindow和reduceByKeyAndWindow以及基于状态的操作，如updateStateByKey，数据会默认进行持久化。 因此，基于窗口的操作生成的DStream会自动保存在内存中，而不需要开发人员调用persist()。

&nbsp;&nbsp;&nbsp;&nbsp;对于通过网络接收数据（例如Kafka，Flume，sockets等）的输入流，默认持久化级别被设置为将数据复制到两个节点进行容错。

&nbsp;&nbsp;&nbsp;&nbsp;请注意，与RDD不同，DStreams的默认持久化级别将数据序列化保存在内存中。

### 九.检查点支持
&nbsp;&nbsp;&nbsp;&nbsp;流数据处理程序通常都是全天候运行，因此必须对应用中逻辑无关的故障(例如，系统故障，JVM崩溃等)具有弹性。为了实现这一特性，Spark Streaming需要checkpoint足够的信息到容错存储系统，以便可以从故障中恢复。

#### 1.一般会对两种类型的数据使用检查点：
##### (1)元数据检查点(Metadatacheckpointing)
将定义流计算的信息保存到容错存储中（如HDFS）。这用于从运行streaming程序的driver程序的节点的故障中恢复。元数据包括以下几种：
* 配置（Configuration） - 用于创建streaming应用程序的配置信息。
* DStream操作（DStream operations） - 定义streaming应用程序的DStream操作集合。
* 不完整的batch（Incomplete batches） - jobs还在队列中但尚未完成的batch。

##### (2)数据检查点(Datacheckpointing)
将生成的RDD保存到可靠的存储层。对于一些需要将多个批次之间的数据进行组合的stateful变换操作，设置数据检查点是必需的。在这些转换操作中，当前生成的RDD依赖于先前批次的RDD，这导致依赖链的长度随时间而不断增加，由此也会导致基于血统机制的恢复时间无限增加。为了避免这种情况，stateful转换的中间RDD将定期设置检查点并保存到到可靠的存储层（例如HDFS）以切断依赖关系链。

&nbsp;&nbsp;&nbsp;&nbsp;总而言之，元数据检查点主要用于从driver程序故障中恢复，而数据或RDD检查点在任何使用stateful转换时是必须要有的。

#### 2.何时启用检查点：
对于具有以下任一要求的应用程序，必须启用检查点：
##### (1)使用状态转：
如果在应用程序中使用updateStateByKey或reduceByKeyAndWindow(具有逆函数)，则必须提供检查点目录以允许定期保存RDD检查点。

##### (2)从运行应用程序的driver程序的故障中恢复：
元数据检查点用于使用进度信息进行恢复。

#### 3.如何配置检查点：
&nbsp;&nbsp;&nbsp;&nbsp;可以通过在一些可容错、高可靠的文件系统（例如，HDFS，S3等）中设置保存检查点信息的目录来启用检查点。这是通过使用streamingContext.checkpoint(checkpointDirectory)完成的。设置检查点后，您就可以使用上述的有状态转换操作。此外，如果要使应用程序从驱动程序故障中恢复，您应该重写streaming应用程序以使程序具有以下行为：

(a)当程序第一次启动时，它将创建一个新的StreamingContext，设置好所有流数据源，然后调用start()方法。

(b)当程序在失败后重新启动时，它将从checkpoint目录中的检查点数据重新创建一个StreamingContext。
使用StreamingContext.getOrCreate可以简化此行为


#### 4.改写之前的WordCount程序，使得每次计算的结果和状态都保存到检查点目录下

通过查看HDFS中的信息，可以看到相关的检查点信息，如下：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUxNzI2M2VmMzg1NGFjZTEucG5n?x-oss-process=image/format,png)
