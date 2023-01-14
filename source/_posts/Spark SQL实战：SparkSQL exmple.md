---
title: 'Spark SQL实战：SparkSQL exmple'
date: 2020-03-13 18:00:00
tags: 
- Spark
- Spark实战
- Spark SQL
categories: Spark
---

#### 1.需求：
使用Spark SQL，读取文件并查询数据表
#### 2.代码：
##### (1)pom.xml
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
##### (2)SparkSQLExample.scala
```java
package spark.sqlshizhan

import org.apache.log4j.{Level, Logger}
import org.apache.spark.sql.SparkSession


/**
 * @ClassName SparkSQLExample
 * @MethodDesc: TODO SparkSQLExample功能介绍
 * @Author Movle
 * @Date 5/18/20 9:34 下午
 * @Version 1.0
 * @Email movle_xjk@foxmail.com
 **/
case class Student1(sno:String,sname:String,ssex:String,sbirthday:String,sclass:String)

case class Course(cno:String,cname:String,tno:String)

case class Score(sno:String,cno:String,degree:String)

case class Teacher(tno:String,tname:String,tsex:String,tbirthday:String,tprof:String,tdepart:String)

import java.text.SimpleDateFormat
object SparkSQLExample {

  def main(args: Array[String]): Unit = {
    System.setProperty("hadoop.home.dir","/Users/macbook/Documents/hadoop/hadoop-2.8.4")

    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    //Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)

    def getDate(time: String) = {
      val now: Long = System.currentTimeMillis()
      val df: SimpleDateFormat = new SimpleDateFormat(time)
      df.format(now)
    }

    val spark = SparkSession.builder().master("local").appName("SparkSQLExample").getOrCreate()

    import spark.sqlContext.implicits._

    spark.sparkContext.textFile("/users/macbook/TestInfo/Student.csv")
      .map(_.split(","))
      .map(x=>Student1(x(0),x(1),x(2),x(3),x(4)))
      .toDF
      .createOrReplaceTempView("Student1")

    spark.sparkContext.textFile("/users/macbook/TestInfo/Course.csv")
        .map(_.split(","))
        .map(x => Course(x(0),x(1),x(2)))
        .toDF
        .createOrReplaceTempView("Course")

    spark.sparkContext.textFile("/users/macbook/TestInfo/Score.csv")
        .map(_.split(","))
        .map(x => Score(x(0),x(1),x(2)))
        .toDF
        .createOrReplaceTempView("Score")
    spark.sparkContext.textFile("/users/macbook/TestInfo/Teacher.csv")
        .map(_.split(","))
        .map(x => Teacher(x(0),x(1),x(2),x(3),x(4),x(5)))
        .toDF
        .createOrReplaceTempView("Teacher")


    //查询整个teacher表
    spark.sql("select * from Teacher").show()

    // 查询Student表中所有记录的sname ssex class列
    spark.sql("select sname,ssex,sclass from student").show()

    //查询教师表中不重复的depart列
    spark.sql("select distinct tdepart from teacher").show(false)
    spark.sql("select tdepart from teacher group by tdepart").show(false)

    //查询score表中成绩在60 80 之间的所有记录
    spark.sql("select * from score where degree >= 60 and degree <= 80").show()
    spark.sql("select * from score where degree between 60 and 80").show()

    //查询score表中成绩为 85 86 或 88 的记录
    spark.sql("select * from score where degree = '85' or degree='86' OR degree='88'").show()
    spark.sql("select * from score where degree =85 or degree=86 OR degree=88").show()

    //以class降序、升序排列查询
    spark.sql("select * from student order by sclass desc").show()
    spark.sql("select * from student order by sclass").show()

    //以cno升序 degree降序查询score表中的数据
    spark.sql("select * from score t order by t.sno asc, t.degree desc").show()

    //查询Score表中的最高分的学生学号和课程
    spark.sql("select * from score order by Int(degree) desc limit 1").show()
    spark.sql("select * from score order by Int(degree) desc").show()

    //查询每门课的平均成绩
    spark.sql("select cno,avg(degree) from score group by cno").show()

    //查询score表中至少有5名学生选修的课，并且名字以 3 开头的课程 的平均分数
    spark.sql("select cno,avg(degree) from score where cno like '3%' group by cno having count(cno) >= 5").show()

    //查询所有学生中的sname cname degree
    spark.sql("select s.sname, t.degree,c.cname from score t " +
      "join student s on t.sno=s.sno " +
      "join course c on c.cno=t.cno").show(false)

    //查询score中选择多门课程的同学中，分数为非最高分成绩的记录
    spark.sql("select * from score where " +
      "sno in (select sno from score t group by t.sno having count(1) > 1) " +
      " and degree != (select max(degree) from score)").show()

    //查询和学号为108的同学同年出生的所有学生的sno sname sbirthday 列
    spark.sql("select sno,sname,sbirthday from student where substring(sbirthday,0,4) = (" +
      " select substring(t.sbirthday,0,4) from student t where sno='108')").show()

    //查询选修某课程的同学人数多于5人的教师姓名
    spark.sql("select tname from teacher e " +
      " join course c on e.tno = c.tno " +
      " join (select cno from score group by cno having count(cno) > 5) t on c.cno = t.cno").show()

    //查询成绩比该课程平均成绩低的同学的成绩表
    spark.sql("select s.* from score s where s.degree < (select avg(degree) from score c where s.cno = c.cno)").show()

    //查询所有没有讲课的教师的tname 和 depart
    spark.sql("select tname , tdepart from teacher t where t.tno not in (select tno from course c where c.cno in (select cno from score))").show(false)

    //查询至少有2名男生的班号
    spark.sql("select sclass from student t where ssex='male' group by sclass having count(ssex) >= 2").show()

    //查询student表中不姓 王 的同学记录
    spark.sql("select * from student t where sname not like('Wang%')").show()

    //查询student表中每个学生的姓名和年龄
    spark.sql("select sname, (cast(" + getDate("yyyy") + " as int) - cast(substring(sbirthday,0,4) as int)) as age from student t").show()

    spark.close()
  }

}

```
#### 3.结果：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZkNjQ4ZTg2ZTQ0NjQ0MjMucG5n?x-oss-process=image/format,png)
