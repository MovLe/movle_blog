---
title: 'Spark Core实战：解析Tomcat日志'
date: 2020-03-12 14:00:00
tags: 
- Spark
- Spark实战
- SparkCore
categories: Spark
---

#### 1.Tomcat日志格式：

localhost_access_log.txt
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

#### 2.需求：
找到访问量最高的两个网页

#### 3.分析：
* 第一步：对网页的访问量求和   和WordCount类似
* 第二步：排序，降序

#### 4.编写代码：

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

##### (2)MyTomcatLogCount.scala

```java
package day1208

import org.apache.spark.SparkContext
import org.apache.spark.SparkConf

/**
 * 解析Tomcat日志
 * 192.168.88.1 - - [30/Jul/2017:12:54:41 +0800] "GET /MyDemoWeb/hadoop.jsp HTTP/1.1" 200 242
		192.168.88.1 - - [30/Jul/2017:12:54:42 +0800] "GET /MyDemoWeb/web.jsp HTTP/1.1" 200 239

		需求：
		找到访问量最高的两个网页

		第一步：对网页的访问量求和   和WordCount类似
		第二步：排序，降序
 *
 *
 */

object MyTomcatLogCount {

  def main(args: Array[String]): Unit = {

    val conf = new SparkConf().setMaster("local").setAppName("My Tomcat Log Count")
    val sc = new SparkContext(conf)

    /**
     * 读入日志，解析，找到访问jsp网页
     * 192.168.88.1 - - [30/Jul/2017:12:54:42 +0800] "GET /MyDemoWeb/web.jsp HTTP/1.1" 200 239
     */

    val rdd1 = sc.textFile("/users/macbook/TestInfo/localhost_access_log.txt")
      .map(
        /**
         * 找到网页名字
         *
         * 并计数
         *
         * line 代表读进来的每一行数据
         */

        line => {
          //解析字符串，找到jsp名字
          //得到两个双引号之间的东西
          val index1 = line.indexOf("\"")
          val index2 = line.lastIndexOf("\"")
          val line1 = line.substring(index1+1, index2) // GET /MyDemoWeb/web.jsp HTTP/1.1

          //得到两个空格之间的东西
          val index3 = line1.indexOf(" ")
          val index4 = line1.lastIndexOf(" ")
          val line2 = line1.substring(index3+1, index4) // /MyDemoWeb/web.jsp

          //得到jsp的名字
          val jspName = line2.substring(line2.lastIndexOf("/")+1)

          (jspName,1)
        }
      )

    /**
     * 按照jsp的名字 进行聚合操作
     */

    val rdd2 = rdd1.reduceByKey(_+_)//得到每个jsp的访问量

    //使用value排序
    val rdd3 = rdd2.sortBy(_._2,false)

    //取出访问量最高的两个网页
    rdd3.take(2).foreach(println)

    sc.stop()

    //销售  ：  勇气输出岗
    //技术 ： 头脑
  }

}

```
#### 5.运行结果：
![运行结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA5MTljNGZjZjNhODNhY2UucG5n?x-oss-process=image/format,png)

