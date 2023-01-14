---
title: '[Spark]-Spark Streaming：基础'
date: 2020-03-10 17:00:00
tags: 
- Spark
- Spark Streaming
categories: Spark
---


#### 1.Spark Streaming简介

&nbsp;&nbsp;&nbsp;&nbsp;Spark Streaming是核心Spark API的扩展，可实现可扩展、高吞吐量、可容错的实时数据流处理。数据可以从诸如Kafka，Flume，Kinesis或TCP套接字等众多来源获取，并且可以使用由高级函数(如map，reduce，join和window)开发的复杂算法进行流数据处理。最后，处理后的数据可以被推送到文件系统，数据库和实时仪表板。而且，您还可以在数据流上应用Spark提供的机器学习和图处理算法。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI5MjhiMjJjMGMwYTE1ZjEucG5n?x-oss-process=image/format,png)

#### 2.Spark Streaming的特点

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMwOTgzOTE1MTg1NGY5YTQucG5n?x-oss-process=image/format,png)

##### (1)易用：集成在Spark中
##### (2)容错性：底层RDD，RDD本身就具备容错机制。
##### (3)支持多种编程语言：Java Scala Python

#### 3.Spark Streaming的内部结构
&nbsp;&nbsp;&nbsp;&nbsp;在内部，它的工作原理如下。Spark Streaming接收实时输入数据流，并将数据切分成批，然后由Spark引擎对其进行处理，最后生成“批”形式的结果流。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI3MTAxMmJmYjMwZDMyMjUucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;Spark Streaming将连续的数据流抽象为discretizedstream或DStream。在内部，DStream 由一个RDD序列表示。