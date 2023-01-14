---
title: 'SparkStreaming实战：处理文件流'
date: 2020-03-15 16:00:00
tags: 
- Spark
- Spark实战
- Spark Streaming
categories: Spark
---

#### 1.需求：
利用SparkStreaming处理文件流：

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
##### (2)FileStreaming.scala
```java
package day1211

import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.storage.StorageLevel

object FileStreaming {
  def main(args: Array[String]): Unit = {

    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    //local[2]代表开启两个线程
    val conf = new SparkConf().setAppName("MyNetwordWordCount").setMaster("local[2]")

    //接收两个参数，第一个conf，第二个是采样时间间隔
    val ssc = new StreamingContext(conf, Seconds(3))

    //监控目录 如果文件系统发生变化 就读取进来
    val lines = ssc.textFileStream("/Users/macbook/TestInfo/test_file_stream")

    lines.print()

    ssc.start()
    ssc.awaitTermination()
  }
}
```


#### 3.运行
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRiNDkxMjllNTQ2Zjc3ZWUucG5n?x-oss-process=image/format,png)
#### 4.结果：
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlkMmRjOWMyMTdhNjUwZmMucG5n?x-oss-process=image/format,png)
