---
title: 'Spark Streaming实战：设置检查点，写一个wordcount程序并计数，计算端口号1235中的信息'
date: 2020-03-15 12:00:00
tags: 
- Spark
- Spark实战
- Spark Streaming
categories: Spark
---

#### 1.需求：
用spark Streaming写一个wordcount程序，计算发往端口号1235中的信息(单词计数)

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
    </dependencies>
```
##### (2)MyTotalNetworkWordCount.scala
```java
package day1211

import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.storage.StorageLevel

object MyTotalNetworkWordCount {

  def main(args: Array[String]): Unit = {

    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    val conf = new SparkConf().setMaster("local[2]").setAppName("MyTotalNetworkWordCount")

    val ssc = new StreamingContext(conf,Seconds(3))

    //设置检查点目录，保存之前的状态信息
    ssc.checkpoint("hdfs://192.168.1.121:9000/TestFile/chkp0826")

    val lines = ssc.socketTextStream("192.168.1.121", 1235, StorageLevel.MEMORY_ONLY)

    val words = lines.flatMap(_.split(" "))

    val wordPair = words.map((_,1))

    /**
     * 两个参数：
     * 第一个参数：当前的值是多少
     * 第二个参数：之前的结果是多少
     */
    val addFunc = (curreValues:Seq[Int],previousValues:Option[Int]) => {
      //进行累加运算
      // 1、把当前值的序列进行累加
      val currentTotal = curreValues.sum

      //2、在之前的值上再累加

      Some( currentTotal + previousValues.getOrElse(0) )

    }

    //进行累加运算
    val total = wordPair.updateStateByKey(addFunc)

    total.print()

    ssc.start()
    ssc.awaitTermination()

  }
}

```
#### 3.运行程序，并往端口号1235发送信息：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThlNmUyYTFiNTk2YTdmNWYucG5n?x-oss-process=image/format,png)
#### 4.结果：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUzNTc3ZTZiYzc0OGM4NzUucG5n?x-oss-process=image/format,png)

