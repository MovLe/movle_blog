---
title: 'SparkStreaming实战：处理来自flume push方式发来的数据'
date: 2020-03-15 18:00:00
tags: 
- Spark
- Spark实战
- Spark Streaming
categories: Spark
---

#### 1.需求：
SparkStreaming处理来自flume push方式发来的数据,即flume将数据推给spark Streaming

#### 2.代码：
##### (1)pom.xml
```xml
<dependencies>
        <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-core -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>2.1.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-sql -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-sql_2.11</artifactId>
            <version>2.1.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-streaming -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming_2.11</artifactId>
            <version>2.1.0</version>

        </dependency>
        <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-streaming-flume -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-streaming-flume_2.11</artifactId>
            <version>2.1.0</version>
        </dependency>

    </dependencies>
```
##### (2)flume文件option
option
```txt
#bin/flume-ng agent -n a1 -f myconf/option -c conf -Dflume.root.logger=INFO,console
#定义agent名，source，channel，sink的名称
a1.sources=r1
a1.channels =c1
a1.sinks=k1

#具体定义source
a1.sources.r1.type= spooldir
a1.sources.r1.spoolDir= /opt/TestFolder/logs

#具体定义channel1
a1.channels.c1.type = memory
a1.channels.c1.capacity=10000
a1.channels.c1.transactionCapacity = 100

#具体定义sink
a1.sinks = k1
a1.sinks.k1.type = avro
a1.sinks.k1.channels =c1
a1.sinks.k1.hostname=192.168.31.211
a1.sinks.k1.port=1236

#组装source, channel,sink

a1.sources.r1.channels = c1
a1.sinks.k1.channel =c1
```
##### (3) MyFlumeStream.scala
```java
import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.streaming.flume.FlumeUtils

object MyFlumeStream {

  def main(args: Array[String]): Unit = {

    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    val conf = new SparkConf().setAppName("MyFlumeStream").setMaster("local[2]")

    val ssc = new StreamingContext(conf,Seconds(3))

    //创建 flume event 从 flume中接收push来的数据 ---> 也是DStream
    //flume将数据push到了 ip 和 端口中
    val flumeEventDstream = FlumeUtils.createStream(ssc, "192.168.1.121", 1236)

    val lineDStream = flumeEventDstream.map( e => {
      new String(e.event.getBody.array)
    })

    lineDStream.print()

    ssc.start()
    ssc.awaitTermination()
  }
}
```
#### 3.运行：
##### (1)运行SparkStreaming程序：
##### (2)开启flume
```shell
bin/flume-ng agent -n a1 -c conf -f jobconf/option -Dflume.root.logger=INFO,console
```

#### 4.向/opt/TestFolder/logs中添加数据，查看结果：

```shell
cp a.txt /opt/TestFolder/logs
```
![添加数据](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU0ZjFmZjcxNDA4YmE0OWQucG5n?x-oss-process=image/format,png)
#### 5.查看结果
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg3NjUxMjg1MDE2YmQ0MWYucG5n?x-oss-process=image/format,png)
