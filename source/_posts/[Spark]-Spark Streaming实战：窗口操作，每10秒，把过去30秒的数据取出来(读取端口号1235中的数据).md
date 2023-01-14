---
title: '[Spark]-Spark Streaming实战：窗口操作，每10秒，把过去30秒的数据取出来(读取端口号1235中的数据)'
date: 2020-03-15 12:00:00
tags: 
- Spark
- Spark实战
- Spark Streaming
categories: Spark
---

#### 1.需求：
窗口操作，每10秒，把过去30秒的数据取出来
* 窗口长度：30秒
* 滑动距离：10秒

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
##### (2)MyNetWorkWordCountByWindow.scala

```java
package day1211

import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.storage.StorageLevel

/**
 * 需求：每10秒，把过去30秒的数据取出来
 *
 * 窗口长度：30秒
 *
 * 滑动距离：10秒
 *
 */
object MyNetWorkWordCountByWindow {

  def main(args: Array[String]): Unit = {
    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    val conf = new SparkConf().setAppName("MyNetWorkWordCountByWindow").setMaster("local[2]")

    val ssc = new StreamingContext(conf,Seconds(1))

    val lines = ssc.socketTextStream("192.168.1.121", 1235, StorageLevel.MEMORY_ONLY)

    val words = lines.flatMap(_.split(" ")).map((_,1))

    /**
     * reduce By Key And Window
     * 三个参数
     * 1、要进行什么操作
     * 2、窗口的大小
     * 3、窗口滑动的距离
     */

    val result = words.reduceByKeyAndWindow((x:Int,y:Int)=>(x+y),Seconds(30),Seconds(10))

    result.print()

    ssc.start()
    ssc.awaitTermination()
  }

}
```

#### 3.运行：往端口中发送数据
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc2MmZiN2RhYjBkMTRlMjAucG5n?x-oss-process=image/format,png)

#### 4.结果
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZhMTEyNzljZjNjNGM0MDUucG5n?x-oss-process=image/format,png)

