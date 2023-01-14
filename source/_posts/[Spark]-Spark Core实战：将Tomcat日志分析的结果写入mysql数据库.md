---
title: '[Spark]-Spark Core实战-将Tomcat日志分析的结果写入mysql数据库'
date: 2020-03-12 17:00:00
tags: 
- Spark
- Spark实战
- SparkCore
categories: Spark
---

#### 1.Tomcat日志和前面一样

#### 2.需求：
将Tomcat日志分析的结果：jps的名称和个数统计，并插入mysql数据库
#### 3.在mysql(本地，我的是MacOS)中建库建表：

```sql
create database company;

create table mydata(
          jsname varchar(50),
          countNumber int(11));
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY3NGYwNTMyMGU1MzM3NjIucG5n?x-oss-process=image/format,png)

#### 4.编写代码：
##### (1)添加pom依赖：
```xml
<!-- https://mvnrepository.com/artifact/org.apache.spark/spark-core -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.11</artifactId>
            <version>2.1.0</version>
        </dependency>
```
##### (2)在项目中加入JDBC驱动包

![JDBC驱动包](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUxNjQwNWNkMjMwODFkNDgucG5n?x-oss-process=image/format,png)

##### (3)MyTomcatLogCountToMysql.scala

```java
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import java.sql.Connection
import java.sql.PreparedStatement
import java.sql.DriverManager


object MyTomcatLogCountToMysql {

  def main(args: Array[String]): Unit = {

    //创建Spark对象
    val conf = new SparkConf().setMaster("local").setAppName("My Tomcat Log Count To Mysql")
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
          val line1 = line.substring(index1 + 1, index2) // GET /MyDemoWeb/web.jsp HTTP/1.1

          //得到两个空格之间的东西
          val index3 = line1.indexOf(" ")
          val index4 = line1.lastIndexOf(" ")
          val line2 = line1.substring(index3 + 1, index4) // /MyDemoWeb/web.jsp

          //得到jsp的名字
          val jspName = line2.substring(line2.lastIndexOf("/") + 1)

          (jspName, 1)
        })

    rdd1.foreachPartition(saveToMysql)

    sc.stop()
  }

  //定义一个函数 针对分区进行操作
  def saveToMysql(it: Iterator[(String, Int)]) = {

    var conn: Connection = null
    var pst: PreparedStatement = null

    //创建连接
    conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/company?serverTimezone=UTC&characterEncoding=utf-8", "root", "123456")

    //把数据保存到mysql中
    pst = conn.prepareStatement("insert into mydata values (?,?) ")

    it.foreach(data => {
      pst.setString(1, data._1)
      pst.setInt(2,data._2)
      pst.executeUpdate()
    })

  }

}
```

#### 5.结果：
![1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFkZmMwMmNhZGNkNGE5NzcucG5n?x-oss-process=image/format,png)
![2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlhOTIyOTM1OTBhYzM1YmEucG5n?x-oss-process=image/format,png)

