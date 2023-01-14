---
title: 'Spark Core实战：使用JDBC RDD操作数据库'
date: 2020-03-12 18:00:00
tags: 
- Spark
- Spark实战
- SparkCore
categories: Spark
---

#### 1.需求：
使用JDBC RDD 操作数据库
#### 2.在数据库中建表并插入数据：
```sql
create table emp(
                id int(11), 
                ename varchar(20), 
                deptno int(11), 
                sal int(11));

insert into  emp values(1,"Tom",10,2500);
insert into  emp values(2,"Movle",11,1000);
insert into  emp values(2,"Mike",10,1500);
insert into  emp values(2,"jack",11,500);
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVlZDIyMmFjYmY4ZmQ3YTQucG5n?x-oss-process=image/format,png)

#### 3.添加JDBC驱动

#### 4.写代码：

##### (1)MyJDBCRddDemo.scala

```java
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.rdd.JdbcRDD
import java.sql.DriverManager

/**
 * 使用JDBC RDD 操作数据库
 */


object MyJDBCRddDemo {

  val connection = () => {
    Class.forName("com.mysql.jdbc.Driver").newInstance()
    DriverManager.getConnection("jdbc:mysql://localhost:3306/company?serverTimezone=UTC&characterEncoding=utf-8", "root", "123456")
  }

  def main(args: Array[String]): Unit = {

    //创建Spark对象
    val conf = new SparkConf().setAppName("My JDBC Rdd Demo").setMaster("local")
    val sc = new SparkContext(conf)

    val mysqlRDD = new JdbcRDD(sc,connection,"select * from emp where sal > ? and sal <= ?",900,2000, 2, r=>{
      //获取员工的姓名和薪水
      val ename = r.getString(2)
      val sal = r.getInt(4)
      (ename,sal)
    })

    val result = mysqlRDD.collect()
    println(result.toBuffer)
    sc.stop
  }
}

```

#### 5.结果：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRkNzVhOWM3ZGVjOTkyOTYucG5n?x-oss-process=image/format,png)