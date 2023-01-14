---
title: '[Spark]-Spark SQL实战:使用SparkSession创建DataFrame执行sql'
date: 2020-03-13 11:00:00
tags: 
- Spark
- Spark实战
- Spark SQL
categories: Spark
---

#### 1.需求：
在IDEA中编写代码，创建DataFrame 执行sql命令：

#### 2.数据源：

##### (1)student.txt
```txt
1	tom  15
2	lucy	20
3	mike	18
```
#### 3.编写代码：
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
##### (2)Demo1.scala

```java
package day1209

import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.types.StructType
import org.apache.spark.sql.types.StructField
import org.apache.spark.sql.types.IntegerType
import org.apache.spark.sql.types.StringType
import org.apache.spark.sql.Row

/**
 * 创建DataFrame 执行sql
 */

object Demo1 {
  def main(args: Array[String]): Unit = {
    //创建Spark Session对象
    val spark = SparkSession.builder().master("local").appName("UnderstandSparkSession").getOrCreate()

    //从指定地址读取文件 创建RDD
    val personRDD = spark.sparkContext.textFile("/users/macbook/TestInfo/student.txt").map(_.split("\t"))

    //指定schema
    val schema = StructType(
      List(
        StructField("id", IntegerType),
        StructField("name", StringType),
        StructField("age", IntegerType)))

    //将RDD转换为 rowRDD
    val rowRDD = personRDD.map(p => Row(p(0).toInt, p(1).trim(), p(2).toInt))

    //创建DataFrame 将schema与row对应
    val personDataFrame = spark.createDataFrame(rowRDD, schema)

    //注册视图
    personDataFrame.createOrReplaceTempView("t_person")

    //执行SQL
    val df = spark.sql("select * from t_person order by age desc")

    //显示结果
    df.show()

    //停止Spark
    spark.stop()
  }
}
```

#### 4.运行结果：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY2MzZmYWEwM2E4NzZjODkucG5n?x-oss-process=image/format,png)
