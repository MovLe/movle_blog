---
title: 'Spark SQL实战：将结果写入 Mysql'
date: 2020-03-13 15:00:00
tags: 
- Spark
- Spark实战
- Spark SQL
categories: Spark
---

#### 1.需求：
读取本地student.txt，并创建DataFrame，并将结果写入mysql数据库中

#### 2.数据源：

student.txt
```txt
1	tom  15
2	lucy	20
3	mike	18
```

#### 3.写代码：

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

##### (2)Demo3.scala

```java
package day1209

import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.types.StructType
import org.apache.spark.sql.types.StructField
import org.apache.spark.sql.types.IntegerType
import org.apache.spark.sql.types.StringType
import org.apache.spark.sql.Row
import java.util.Properties

/**
 * 将结果写入 Mysql
 */
object Demo3 {
  def main(args: Array[String]): Unit = {
    //创建Spark Session对象
    val spark = SparkSession.builder().master("local").appName("Save to Mysql").getOrCreate()

    //从指定地址读取文件 创建RDD
    val personRDD = spark.sparkContext.textFile("/users/macbook/TestInfo/student.txt").map(_.split("\t"))

    //指定schema
    val schema = StructType(
      List(
        StructField("id", IntegerType),
        StructField("sname", StringType),
        StructField("age", IntegerType)))

    //将RDD转换为 rowRDD
    val rowRDD = personRDD.map(p => Row(p(0).toInt, p(1).trim(), p(2).toInt))

    //创建DataFrame 将schema与row对应
    val personDataFrame = spark.createDataFrame(rowRDD, schema)

    //注册视图
    personDataFrame.createOrReplaceTempView("t_person")

    //执行SQL
    val result = spark.sql("select * from t_person order by age desc")



    //显示结果
    //df.show()

    //把结果保存在Mysql中
    val props = new Properties()
    props.setProperty("user", "root")
    props.setProperty("password", "000000")

    result.write.mode("append").jdbc("jdbc:mysql://192.168.1.121:3306/company?serverTimezone=UTC&characterEncoding=utf-8", "student", props)

    spark.stop()

  }
}
```

#### 4.结果：
![执行前查询student表](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU5OGVmMWY3OTdhMDZkMjAucG5n?x-oss-process=image/format,png)
![执行后查询student表](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNlYjcwMmI1OGFmNjNkYTAucG5n?x-oss-process=image/format,png)
