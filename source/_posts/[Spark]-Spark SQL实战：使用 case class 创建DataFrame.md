---
title: '[Spark]-Spark SQL实战:使用 case class 创建DataFrame'
date: 2020-03-13 14:00:00
tags: 
- Spark
- Spark实战
- Spark SQL
categories: Spark
---

#### 1.需求：
使用 case class 创建DataFrame

#### 2.数据源：

##### (1)student.txt
```txt
1	tom  15
2	lucy	20
3	mike	18
```

#### 3.编写代码
##### (1)添加依赖：
pom.xml

```xml
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
```
##### (2)Demo2.scala

```java
package day1209

import org.apache.spark.sql.SparkSession

/**
 * 使用 case class 创建DataFrame
 */
object Demo2 {
  def main(args: Array[String]): Unit = {

    val spark = SparkSession.builder().master("local").appName("CaseClassDemo").getOrCreate()

    val lineRDD = spark.sparkContext.textFile("/users/macbook/TestInfo/student.txt").map(_.split("\t"))

    //RDD 和 表结构关联
    val studentRDD = lineRDD.map(x => Student(x(0).toInt,x(1),x(2).toInt))

    //生成DataFrame
    import spark.sqlContext.implicits._
    val studentDF = studentRDD.toDF

    studentDF.createOrReplaceTempView("student")

    spark.sql("select * from student").show

    spark.stop()

  }
}
//定义 case class 相当于schema
case class Student(stuId:Int,stuName:String,stuAge:Int)
```

#### 4.结果：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcwMTk4ZmY0NmQ1YmJjMzYucG5n?x-oss-process=image/format,png)

