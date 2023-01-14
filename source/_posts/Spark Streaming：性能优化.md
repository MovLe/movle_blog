---
title: 'Spark Streaming：性能优化'
date: 2020-03-10 20:00:00
tags: 
- Spark
- Spark Streaming
categories: Spark
---

#### 1.减少批数据的执行时间

在Spark中有几个优化可以减少批处理的时间：
##### (1)数据接收的并行水平
&nbsp;&nbsp;&nbsp;&nbsp;通过网络(如kafka，flume，socket等)接收数据需要这些数据反序列化并被保存到Spark中。如果数据接收成为系统的瓶颈，就要考虑并行地接收数据。注意，每个输入DStream创建一个receiver(运行在worker机器上)接收单个数据流。创建多个输入DStream并配置它们可以从源中接收不同分区的数据流，从而实现多数据流接收。例如，接收两个topic数据的单个输入DStream可以被切分为两个kafka输入流，每个接收一个topic。这将在两个worker上运行两个receiver，因此允许数据并行接收，提高整体的吞吐量。多个DStream可以被合并生成单个DStream，这样运用在单个输入DStream的transformation操作可以运用在合并的DStream上。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM1MmI1NDBmMjBiNjk4ZDIucG5n?x-oss-process=image/format,png)
##### (2)数据处理的并行水平

&nbsp;&nbsp;&nbsp;&nbsp;如果运行在计算stage上的并发任务数不足够大，就不会充分利用集群的资源。默认的并发任务数通过配置属性来确定spark.default.parallelism。

##### (3)数据序列化

&nbsp;&nbsp;&nbsp;&nbsp;可以通过改变序列化格式来减少数据序列化的开销。在流式传输的情况下，有两种类型的数据会被序列化：
* 输入数据
* 由流操作生成的持久RDD

&nbsp;&nbsp;&nbsp;&nbsp;在上述两种情况下，使用Kryo序列化格式可以减少CPU和内存开销。


#### 2.设置正确的批容量
&nbsp;&nbsp;&nbsp;&nbsp;为了Spark Streaming应用程序能够在集群中稳定运行，系统应该能够以足够的速度处理接收的数据（即处理速度应该大于或等于接收数据的速度）。这可以通过流的网络UI观察得到。批处理时间应该小于批间隔时间。

&nbsp;&nbsp;&nbsp;&nbsp;根据流计算的性质，批间隔时间可能显著的影响数据处理速率，这个速率可以通过应用程序维持。可以考虑WordCountNetwork这个例子，对于一个特定的数据处理速率，系统可能可以每2秒打印一次单词计数(批间隔时间为2秒)，但无法每500毫秒打印一次单词计数。所以，为了在生产环境中维持期望的数据处理速率，就应该设置合适的批间隔时间(即批数据的容量)。

&nbsp;&nbsp;&nbsp;&nbsp;找出正确的批容量的一个好的办法是用一个保守的批间隔时间（5-10,秒）和低数据速率来测试你的应用程序。

#### 3.内存调优
在这一节，重点介绍几个强烈推荐的自定义选项，它们可以减少Spark Streaming应用程序垃圾回收的相关暂停，获得更稳定的批处理时间。
##### (1)Default persistence level of DStreams：
&nbsp;&nbsp;&nbsp;&nbsp;和RDDs不同的是，默认的持久化级别是序列化数据到内存中(DStream是StorageLevel.MEMORY_ONLY_SER，RDD是StorageLevel.MEMORY_ONLY)。即使保存数据为序列化形态会增加序列化/反序列化的开销，但是可以明显的减少垃圾回收的暂停。

##### (2)Clearing persistent RDDs：
&nbsp;&nbsp;&nbsp;&nbsp;默认情况下，通过Spark内置策略(LUR)，Spark Streaming生成的持久化RDD将会从内存中清理掉。如果spark.cleaner.ttl已经设置了，比这个时间存在更老的持久化RDD将会被定时的清理掉。正如前面提到的那样，这个值需要根据Spark Streaming应用程序的操作小心设置。然而，可以设置配置选项spark.streaming.unpersist为true来更智能的去持久化(unpersist)RDD。这个配置使系统找出那些不需要经常保有的RDD，然后去持久化它们。这可以减少Spark RDD的内存使用，也可能改善垃圾回收的行为。

##### (3)Concurrent garbage collector：
&nbsp;&nbsp;&nbsp;&nbsp;使用并发的标记-清除垃圾回收可以进一步减少垃圾回收的暂停时间。尽管并发的垃圾回收会减少系统的整体吞吐量，但是仍然推荐使用它以获得更稳定的批处理时间。
