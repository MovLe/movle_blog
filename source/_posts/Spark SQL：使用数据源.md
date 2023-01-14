---
title: 'Spark SQL:使用数据源'
date: 2020-03-11 11:00:00
tags: 
- Spark
- Spark SQL
categories: Spark
---

&nbsp;&nbsp;&nbsp;&nbsp;在Spark SQL中，可以使用各种各样的数据源进行操作。Spark SQL 用于处理结构化的数据
 
### 一.通用的Load/Save函数(load函数式加载数据，save函数式存储数据)
&nbsp;&nbsp;&nbsp;注意：使用load或者save函数时，默认的数据源都是 Parquet文件。列式存储文件

#### 1.通用的Load/Save函数
##### (1)读取Parquet文件
```java
val usersDF = spark.read.load("/root/resources/users.parquet")
```
##### (2)查询Schema和数据
```java
scala> usersDF.printSchema

scala> usersDF.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM5NThiMGYzMmQ1MTI3NjMucG5n?x-oss-process=image/format,png)

##### (3)查询用户的name和喜爱颜色，并保存
```java
usersDF.select($"name",$"favorite_color").write.save("/root/result/parquet")
```

##### (4)验证结果
```java
scala >val testResult = spark.read.load("/root/result/parquet/part-00000-8ffaac2e-aa81-4e63-89aa-15a8e4948a37.snappy.parquet")

scala >testResult.printSchema

scala >testResult.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNmYmU3ODU0OTg4NGU4ZTQucG5n?x-oss-process=image/format,png)

#### 2.显式指定文件格式：加载json格式
##### (1)直接加载：
```java
val usersDF = spark.read.load("/root/resources/people.json")  //会出错
```
上面这个会出错
```java
val usersDF = spark.read.format("json").load("/root/resources/people.json")
```

#### 3.存储模式（Save Modes）
&nbsp;&nbsp;&nbsp;&nbsp;可以采用SaveMode执行存储操作，SaveMode定义了对数据的处理模式。需要注意的是，这些保存模式不使用任何锁定，不是原子操作。此外，当使用Overwrite方式执行时，在输出新数据之前原数据就已经被删除。SaveMode详细介绍如下表：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ2YTAyOGMwMjVkN2U0N2YucG5n?x-oss-process=image/format,png)
Demo：
```java
usersDF.select($"name").write.save("/root/result/parquet1")
```
上面出错：因为/root/result/parquet1已经存在
```java
usersDF.select($"name").write.mode("overwrite").save("/root/result/parquet1")
```
#### 4.将结果保存为表
```java
usersDF.select($"name").write.saveAsTable("table1")
```
也可以进行分区、分桶等操作：partitionBy、bucketBy

### 二.Parquet文件(列式存储文件，是Spark SQL默认的数据源)

#### 1.什么是parquet文件？

##### (1)Parquet是列式存储格式的一种文件类型，列式存储有以下的核心：
* 可以跳过不符合条件的数据，只读取需要的数据，降低IO数据量。
* 压缩编码可以降低磁盘存储空间。由于同一列的数据类型是一样的，可以使用更高效的压缩编码（例如Run Length Encoding和Delta Encoding）进一步节约存储空间。
* 只读取需要的列，支持向量运算，能够获取更好的扫描性能。
* Parquet格式是Spark SQL的默认数据源，可通过spark.sql.sources.default配置

##### (2)Parquet是一个列格式而且用于多个数据处理系统中。
Spark SQL提供支持对于Parquet文件的读写，也就是自动保存原始数据的schema。当写Parquet文件时，所有的列被自动转化为nullable，因为兼容性的缘故。

#### 2.把其他文件，转换成Parquet文件()
&nbsp;&nbsp;&nbsp;&nbsp;读入json格式的数据，将其转换成parquet格式，并创建相应的表来使用SQL进行查询。

```java
scala >val empDF = spark.read.json("/opt/module/datas/TestFile/emp.json")

scala >empDF.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThjNDdhNmU2ZTU5MTk3ZGIucG5n?x-oss-process=image/format,png)
```java
scala >empDF.write.mode("overwrite").save("/opt/module/datas/TestFile/myresult/parquet")

//save和parquet都可以写入，是一样的
scala >empDF.write.mode("overwrite").parquet("/opt/module/datas/TestFile/myresult/parquet")

scala >val emp1=spark.read.parquet("/opt/module/datas/TestFile/myresult/parquet")

scala >emp1.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZiZDgxNGFmNmQwMTk2NjIucG5n?x-oss-process=image/format,png)
```java
scala >emp1.createOrReplaceTempView("emptable")

scala >spark.sql("select * from emptable where deptno =10 and sal >1500").show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg4ZTAwMzcxMDAxYjdkOWUucG5n?x-oss-process=image/format,png)

#### 3.支持Schema的合并：
&nbsp;&nbsp;&nbsp;&nbsp;Parquet支持Schema evolution(Schema演变，即：合并)。用户可以先定义一个简单的Schema，然后逐渐的向Schema中增加列描述。通过这种方式，用户可以获取多个有不同Schema但相互兼容的Parquet文件。
Demo:
```java
val df1= sc.makeRDD(1 to 5).map(i => (i,i*2)).toDF("single","double")

df1.show

sc.makeRDD(1 to 5)

sc.makeRDD(1 to 5).collect

df1.write.parquet("/opt/module/datas/TestFile/test_table/key=1")
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWViODVkOTA5M2M1MTg0MzgucG5n?x-oss-process=image/format,png)
```java
val df2 = sc.makeRDD(6 to 10).map(i=>(i,i*3)).toDF("single","triple")

df2.show

df2.write.parquet("/opt/module/datas/TestFile/test_table/key=2")
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM2NTFhMDE3M2I1YzJlNTIucG5n?x-oss-process=image/format,png)
```java
val df3 = spark.read.parquet("/opt/module/datas/TestFile/test_table")

df3.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFjMGQ5ODIyM2MwMDNjZDIucG5n?x-oss-process=image/format,png)
```java
val df3 = spark.read.option("mergeSchema",true).parquet("/opt/module/datas/TestFile/test_table")

df3.show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE0OGRiOTMzMTVhN2ZhZjcucG5n?x-oss-process=image/format,png)

#### 4.JSON 文件
&nbsp;&nbsp;&nbsp;&nbsp;Spark SQL能自动解析JSON数据集的Schema，读取JSON数据集为DataFrame格式。读取JSON数据集方法为SQLContext.read().json()。该方法将String格式的RDD或JSON文件转换为DataFrame。

&nbsp;&nbsp;&nbsp;&nbsp;需要注意的是，这里的JSON文件不是常规的JSON格式。JSON文件每一行必须包含一个独立的、自满足有效的JSON对象。如果用多行描述一个JSON对象，会导致读取出错。读取JSON数据集示例如下：

##### (1)Demo1：使用Spark自带的示例文件 --> people.json 文件
```java
//定义路径：
val path ="/opt/module/datas/TestFile/people.json"

//读取Json文件，生成DataFrame：
val peopleDF = spark.read.json(path)

//打印Schema结构信息：
peopleDF.printSchema()

//创建临时视图：
peopleDF.createOrReplaceTempView("people")

//执行查询
spark.sql("SELECT name FROM people WHERE age=18").show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ4ZGFmOTZlMmFiNDM4NmYucG5n?x-oss-process=image/format,png)

#### 5.使用JDBC(通过JDBC操作关系型数据库，mysql中的数据，通过JDBC加载到Spark中进行分析和处理)

&nbsp;&nbsp;&nbsp;&nbsp;Spark SQL同样支持通过JDBC读取其他数据库的数据作为数据源。

#### (一)Demo演示：使用Spark SQL读取Oracle数据库中的表。
##### (1)启动Spark Shell的时候，指定Oracle数据库的驱动
```shell
bin/spark-shell --master spark://hadoop:7077 --jars /opt/soft/mysql-connector-java-5.1.27.jar --driver-class-path /opt/soft/mysql-connector-java-5.1.27.jar 
```

##### (2)读取mysql数据库中的数据

###### (a)方式一：
```java
val mysqlDF = spark.read.format("jdbc").
         option("url","jdbc:mysql://192.168.1.120:3306/company?serverTimezone=UTC&characterEncoding=utf-8").
         option("dbtable","emp").
         option("user","root").
         option("password","000000").load

mysqlDF.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUyMDhhNjViNGMxMGUxMTMucG5n?x-oss-process=image/format,png)

###### (b)方式二：定义 Properities类 
```java
//导入需要的类：
import java.util.Properties   

//定义属性：               
val mysqlProps = new Properties()
mysqlProps.setProperty("user","root")
mysqlProps.setProperty("password","000000")
//读取数据：

val mysqlDF1 = spark.read.jdbc("jdbc:mysql://192.168.1.120:3306/company?serverTimezone=UTC&characterEncoding=utf-8","emp",mysqlProps)

mysqlDF1.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ4NTE3N2Y3MTkyM2M4OGEucG5n?x-oss-process=image/format,png)

#### 6.使用Hive Table(把Hive中的数据，读取到Spark SQL 中)
##### (1)首先，搭建好Hive的环境(需要Hadoop)
###### (a)搭建台Hive，一台Hive Server(hadoop2)，一台Hive Client(hadoop1)
这两台hive中其他配置文件一样，知识hive-site.xml有区别
其中Hive Server的hive-site.xml配置如下:

```xml
<configuration> 
    <property> 
        <name>hive.metastore.warehouse.dir</name>  
        <value>/user/hive/warehouse</value> 
    </property>  
    <property> 
        <name>javax.jdo.option.ConnectionURL</name>  
        <value>jdbc:mysql://hadoop1:3306/hive?serverTimezone=UTC</value> 
    </property>  
    <property> 
        <name>javax.jdo.option.ConnectionDriverName</name>  
        <value>com.mysql.jdbc.Driver</value> 
    </property>  
    <property> 
        <name>javax.jdo.option.ConnectionUserName</name>  
        <value>root</value> 
    </property>  
    <property> 
        <name>javax.jdo.option.ConnectionPassword</name>  
        <value>123456</value> 
    </property>  
    <property> 
        <name>hive.querylog.location</name>  
        <value>/data/hive/iotmp</value> 
    </property>  
    <property> 
        <name>hive.server2.logging.operation.log.location</name>  
    <value>/data/hive/operation_logs</value> 
    </property>  
    <property> 
        <name>datanucleus.readOnlyDatastore</name>  
        <value>false</value> 
    </property>  
    <property> 
        <name>datanucleus.fixedDatastore</name>  
        <value>false</value> 
    </property>  
    <property> 
        <name>datanucleus.autoCreateSchema</name>  
        <value>true</value> 
    </property>  
    <property> 
        <name>datanucleus.autoCreateTables</name>  
        <value>true</value> 
    </property>  
    <property> 
        <name>datanucleus.autoCreateColumns</name>  
        <value>true</value> 
    </property> 
    <property>
        <name>datanucleus.schema.autoCreateAll</name>
        <value>true</value>
    </property>
</configuration>
```

Hive Client 中hive-site.xml配置如下：
```xml
<configuration>
          <property>
                <name>hive.metastore.warehouse.dir</name>
                <value>/user/hive/warehouse</value>
          </property>
          <property>
                <name>hive.metastore.local</name>
                <value>false</value>
          </property>
          <property>
                <name>hive.metastore.uris</name>
                <value>thrift://192.168.1.122:9083</value>
          </property>
</configuration>    
```
![Hive Client](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlmNDI3ZTM5NWJiMzJiOTAucG5n?x-oss-process=image/format,png)

##### (2)配置Spark SQL支持Hive
###### (a)只需要将以下文件拷贝到$SPARK_HOME/conf的目录下，即可
* $HIVE_HOME/conf/hive-site.xml(拷贝Hive Client中的hive-site.xml)
* $HADOOP_CONF_DIR/core-site.xml
* $HADOOP_CONF_DIR/hdfs-site.xml

##### (3)启动hive：
启动Hive Server
```shell
cd /opt/module/hive-1.2.1

bin/hive --service metastore
```
![Hive Server](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc4YTBjMGYzNmExOTk4Y2IucG5n?x-oss-process=image/format,png)

启动Hive Client
```shell
cd /opt/module/hive-1.2.1

bin/hive
```
![Hive Client](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM1NTYzNDlhNzE3NTA5MzYucG5n?x-oss-process=image/format,png)

##### (4)使用Spark Shell操作Hive
###### (a)启动Spark Shell的时候，需要使用--jars指定mysql的驱动程序

启动Spark
```shell
cd /opt/module/spark-2.1.0-bin-hadoop2.7

bin/spark-shell --master://hadoop1:7077

spark.sql("select * from default.emp").show
```
![查询Hive中的表](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc0ZGVkZGYyYWQ1ZTAxNDQucG5n?x-oss-process=image/format,png)

###### (b)创建表

```sql
spark.sql("create table movle.src (key INT, value STRING) row format 	delimited fields terminated by ','")
```
![创建表1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFjODhmZWM1MjY2MzMyMzQucG5n?x-oss-process=image/format,png)


![创建表2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU4OGIxNzNmZDdkNGQyZmUucG5n?x-oss-process=image/format,png)

###### (c )导入数据
```sql
spark.sql("load data local path '/root/temp/data.txt' into table src")
```
###### (d)查询数据
```sql
spark.sql("select * from src").show
```
##### (4)使用spark-sql操作Hive
###### (a)启动spark-sql的时候，需要使用--jars指定mysql的驱动程序
###### (b)操作Hive
```sql
spark.sql("show tables").show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE1ZjQxMThmNDY1NDVjYTEucG5n?x-oss-process=image/format,png)