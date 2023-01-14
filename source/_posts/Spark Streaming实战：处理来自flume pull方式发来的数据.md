---
title: 'SparkStreaming实战：处理来自flume pull方式发来的数据'
date: 2020-03-15 19:00:00
tags: 
- Spark
- Spark实战
- Spark Streaming
categories: Spark
---

#### 1.需求：
处理来自flume pull方式发来的数据

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
##### (2)option2

option2
```txt
#定义agent名，source，channel，sink的名称
a1.sources=r1
a1.channels =c1
a1.sinks=k1

#具体定义source
a1.sources.r1.type= spooldir
a1.sources.r1.spoolDir= /opt/TestFolder/logs
a1.sources.r1.fileSuffix = .COMPLETED

#具体定义channel1
a1.channels.c1.type = memory
a1.channels.c1.capacity=10000
a1.channels.c1.transactionCapacity = 100

#具体定义sink
a1.sinks.k1.type = org.apache.spark.streaming.flume.sink.SparkSink
a1.sinks.k1.channels =c1
a1.sinks.k1.hostname=192.168.31.132
a1.sinks.k1.port=1234

#组装source, channel,sink

a1.sources.r1.channels = c1
a1.sinks.k1.channel =c1
```

##### (3)FlumeLogPul.scala

```java
import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.streaming.flume.FlumeUtils
import org.apache.spark.storage.StorageLevel

object FlumeLogPull {
  def main(args: Array[String]): Unit = {
    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    val conf = new SparkConf().setAppName("FlumeLogPull").setMaster("local[2]")
    val ssc = new StreamingContext(conf,Seconds(1))

    val flumeEvent = FlumeUtils.createPollingStream(ssc, "192.168.31.211", 1234,StorageLevel.MEMORY_ONLY_SER)

    val lineDStream = flumeEvent.map( e => {
      new String(e.event.getBody.array)
    })

    lineDStream.print()

    ssc.start()
    ssc.awaitTermination()
  }
}

```
#### 3.将spark-streaming-flume-sink_2.11-2.1.0.jar拷贝到flume的jar目录下
#### 4.运行：
##### (1)运行SparkStreaming程序：
##### (2)开启flume
```shell
bin/flume-ng agent -n a1 -c conf -f myconf/option2 -Dflume.root.logger=INFO,console
```

#### 5.结果：