---
title: 'Spark Streaming实战:写一个wordcount程序，统计从netcat中向端口发送的数据'
date: 2020-03-15 11:00:00
tags: 
- Spark
- Spark实战
- Spark Streaming
categories: Spark
---

#### 1.需求：
通过spark streaming统计端口号1234中的信息

#### 2.编写代码：
##### (1)添加依赖：
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
    </dependencies>
```
记得去掉spark-streaming中的<scope>provided</scope>

##### (2)MyNetwordWordCount.scala

```java
package day1211
import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.storage.StorageLevel

/**
 * 知识点汇总：
 * 1、创建StreamingContext-->本质，核心：创建DStream
 *
 * 2、DStream的表现形式：就是一个RDD
 * 	操作DSteam和操作RDD是一样的。
 *
 * 3、使用DStream把连续的数据流编程不连续的RDD
 */
object MyNetwordWordCount {

  def main(args: Array[String]): Unit = {
    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    //local[2]代表开启两个线程
    val conf = new SparkConf().setAppName("MyNetwordWordCount").setMaster("local[2]")

    //接收两个参数，第一个conf，第二个是采样时间间隔
    val ssc = new StreamingContext(conf,Seconds(3))

    //创建DStream 从netcat服务器上接收数据 因为接收字符串，所以使用textStream
    val lines = ssc.socketTextStream("192.168.1.121", 1234, StorageLevel.MEMORY_ONLY)

    val words = lines.flatMap(_.split(" "))

    val wordCount = words.map((_,1)).reduceByKey(_+_)
    //    val wordCount = words.transform(x => x.map((_,1))).reduceByKey(_+_)

    wordCount.print()

    ssc.start()

    //等待任务结束
    ssc.awaitTermination()

  }

}
```

#### 3.运行此程序，同时用netcat向1234端口号中发送信息，
![1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWIxM2Q3NTIzMjBkNzk0YWIucG5n?x-oss-process=image/format,png)
#### 4.结果：
![1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE2OWFkYWI4MzM4ODc2ZDcucG5n?x-oss-process=image/format,png)