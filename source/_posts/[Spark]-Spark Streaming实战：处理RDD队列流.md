---
title: '[Spark]-SparkStreaming实战：处理RDD队列流'
date: 2020-03-15 17:00:00
tags: 
- Spark
- Spark实战
- Spark Streaming
categories: Spark
---

#### 1.需求：
利用SparkStreaming处理RDD队列流
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
##### (2)RDDQueueStream.scala
```java
import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import scala.collection.mutable.Queue
import org.apache.spark.rdd.RDD

object RDDQueueStream {

  def main(args: Array[String]): Unit = {

    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    val conf = new SparkConf().setAppName("RDDQueueStream").setMaster("local[2]")

    val ssc = new StreamingContext(conf,Seconds(1))

    //需要一个RDD队列
    val rddQueue = new Queue[RDD[Int]]()


    for( i <- 1 to 3){
      rddQueue += ssc.sparkContext.makeRDD(1 to 10)

      Thread.sleep(5000)
    }

    //从队列中接收数据 创建DStream
    val inputDStream = ssc.queueStream(rddQueue)

    val result = inputDStream.map(x=>(x,x*2))

    result.print()

    ssc.start()
    ssc.awaitTermination()

  }
}
```

#### 3.运行：

#### 4.结果：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThlZWFkNzdlOWZlNzBhNDMucG5n?x-oss-process=image/format,png)
