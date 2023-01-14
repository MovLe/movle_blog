---
title: 'Spark Core实战-创建自定义分区'
date: 2020-03-12 16:00:00
tags: 
- Spark
- Spark实战
- SparkCore
categories: Spark
---


#### 1.Tomcat日志格式：
```txt
192.168.88.1 - - [30/Jul/2017:12:53:43 +0800] "GET /MyDemoWeb/ HTTP/1.1" 200 259
192.168.88.1 - - [30/Jul/2017:12:53:43 +0800] "GET /MyDemoWeb/head.jsp HTTP/1.1" 200 713
192.168.88.1 - - [30/Jul/2017:12:53:43 +0800] "GET /MyDemoWeb/body.jsp HTTP/1.1" 200 240
192.168.88.1 - - [30/Jul/2017:12:54:37 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:38 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:38 +0800] "GET /MyDemoWeb/java.jsp HTTP/1.1" 200 240
192.168.88.1 - - [30/Jul/2017:12:54:40 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:40 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:41 +0800] "GET /MyDemoWeb/mysql.jsp HTTP/1.1" 200 241
192.168.88.1 - - [30/Jul/2017:12:54:41 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:42 +0800] "GET /MyDemoWeb/web.jsp HTTP/1.1" 200 239
192.168.88.1 - - [30/Jul/2017:12:54:42 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:52 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:52 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:53 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:54 +0800] "GET /MyDemoWeb/mysql.jsp HTTP/1.1" 200 241
192.168.88.1 - - [30/Jul/2017:12:54:54 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:54 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:56 +0800] "GET /MyDemoWeb/web.jsp HTTP/1.1" 200 239
192.168.88.1 - - [30/Jul/2017:12:54:56 +0800] "GET /MyDemoWeb/java.jsp HTTP/1.1" 200 240
192.168.88.1 - - [30/Jul/2017:12:54:57 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:57 +0800] "GET /MyDemoWeb/java.jsp HTTP/1.1" 200 240
192.168.88.1 - - [30/Jul/2017:12:54:58 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:58 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:59 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:54:59 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:55:00 +0800] "GET /MyDemoWeb/mysql.jsp HTTP/1.1" 200 241
192.168.88.1 - - [30/Jul/2017:12:55:00 +0800] "GET /MyDemoWeb/oracle.jsp HTTP/1.1" 200 242
192.168.88.1 - - [30/Jul/2017:12:55:02 +0800] "GET /MyDemoWeb/web.jsp HTTP/1.1" 200 239
192.168.88.1 - - [30/Jul/2017:12:55:02 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
```
#### 2.需求：按照jsp的名字，将访问日志进行分区

#### 3.分析：

一个文件就是一个分区，并且一个文件中只包含一个jsp的名字
 jsp名字看成key 访问日志看成value

#### 4.代码：

##### (1)添加依赖：
pom.xml

```xml
 <!-- https://mvnrepository.com/artifact/org.apache.spark/spark-core -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>2.1.0</version>
        </dependency>
```

##### (2)MyTomcatLogPartitioner.scala
```java
package day1208

import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.Partitioner
import scala.collection.mutable.HashMap

/**
 *
 * 创建自定义分区
 *
 * 需求：按照jsp的名字，将访问日志进行分区。
 *
 * 一个文件就是一个分区，并且一个文件中只包含一个jsp的名字
 *
 * jsp名字看成key 访问日志看成value
 *
 */

object MyTomcatLogPartitioner {

  def main(args: Array[String]): Unit = {
    //创建Spark对象
    System.setProperty("hadoop.home.dir", "/Users/macbook/Documents/hadoop/hadoop-2.8.4")

    val conf = new SparkConf().setMaster("local").setAppName("My Tomcat Log Partitioner")
    val sc = new SparkContext(conf)

    /**
     * 读入日志 解析
     * 192.168.88.1 - - [30/Jul/2017:12:54:40 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
     *
     */
    val rdd1 = sc.textFile("/users/macbook/TestInfo/localhost_access_log.txt")
      .map(
        line => {
          //解析字符串，找到jsp名字
          //得到两个双引号之间的东西
          val index1 = line.indexOf("\"")
          val index2 = line.lastIndexOf("\"")
          val line1 = line.substring(index1 + 1, index2) // GET /MyDemoWeb/web.jsp HTTP/1.1

          //得到两个空格之间的东西
          val index3 = line1.indexOf(" ")
          val index4 = line1.lastIndexOf(" ")
          val line2 = line1.substring(index3 + 1, index4) // /MyDemoWeb/web.jsp

          //得到jsp的名字
          val jspName = line2.substring(line2.lastIndexOf("/") + 1)

          (jspName, line)
        })

    //自定义分区规则 新建一个类

    val rdd2 = rdd1.map(_._1).distinct().collect()

    //创建分区规则
    val myPartitioner = new MyWebPartitioner(rdd2)

    //对rdd1进行分区
    val rdd3 = rdd1.partitionBy(myPartitioner)

    //输出
    rdd3.saveAsTextFile("/users/macbook/TestInfo/1208/test_partition")

    sc.stop()

  }

}

class MyWebPartitioner(jspList : Array[String]) extends Partitioner{

  //定义一个集合来保存分区的条件 即保存jsp分到哪个区
  val partitionMap = new HashMap[String,Int]()

  var partId = 0 //分区号

  for(jsp <- jspList){
    partitionMap.put(jsp,partId)
    partId += 1 //分区号加一
  }

  //返回有多少个分区
  def numPartitions :Int = partitionMap.size

  //根据jsp 返回对应的分区
  def getPartition(key:Any):Int = partitionMap.getOrElse(key.toString(), 0)



}
```
#### 5.结果：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTllMmFkODc1ODA3ZTBjZGEucG5n?x-oss-process=image/format,png)
