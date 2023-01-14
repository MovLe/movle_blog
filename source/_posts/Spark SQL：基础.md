---
title: 'Spark SQL:基础'
date: 2020-03-11 10:00:00
tags: 
- Spark
- Spark SQL
categories: Spark
---


### 一.Spark SQL简介
&nbsp;&nbsp;&nbsp;&nbsp;Spark SQL是Spark用来处理结构化数据的一个模块，它提供了一个编程抽象叫做DataFrame并且作为分布式SQL查询引擎的作用。
&nbsp;&nbsp;&nbsp;&nbsp;为什么要学习Spark SQL?Hive，它将Hive SQL转换成MapReduce然后提交到集群上执行，大大简化了编写MapReduce的程序的复杂性，由于MapReduce这种计算模型执行效率比较慢。所以Spark SQL的应运而生，它是将Spark SQL转换成RDD，然后提交到集群执行，执行效率非常快！同时Spark SQL也支持从Hive中读取数据。

### 二.Spark SQL的特点：
#### 1.容易整合(集成):
安装Spark的时候，已经集成好了。不需要单独安装

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk4MzQzODVlMGFmYzU4MjIucG5n?x-oss-process=image/format,png)

#### 2.统一的数据访问方式
JDBC、JSON、Hive、parquet文件（一种列式存储文件，是SparkSQL默认的数据源）

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc5MDhlMjI1Zjg5NTlkMzkucG5n?x-oss-process=image/format,png)


#### 3.兼容Hive:
可以将Hive中的数据，直接读取到Spark SQL中处理。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBhZDA2ZTkyMjM5NzhlYzQucG5n?x-oss-process=image/format,png)


#### 4.标准的数据连接:JDBC

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY4NWMxNThlZGEwYmZiZDYucG5n?x-oss-process=image/format,png)


### 三.基本概念：表：Datasets和DataFrames

#### 1.表 = 表结构  +  数据		
&nbsp;&nbsp;&nbsp;&nbsp;DataFrame = Schema(表结构) + RDD（代表数据）

#### 2.DataFrame
&nbsp;&nbsp;&nbsp;&nbsp;DataFrame是组织成命名列的数据集。它在概念上等同于关系数据库中的表，但在底层具有更丰富的优化。DataFrames可以从各种来源构建，

例如：
* 结构化数据文件
* hive中的表
* 外部数据库或现有RDDs 

DataFrame API支持的语言有Scala，Java，Python和R

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJlNTkwMTA2MTU3YzA3NGMucG5n?x-oss-process=image/format,png)


&nbsp;&nbsp;&nbsp;&nbsp;从上图可以看出，DataFrame多了数据的结构信息，即schema。RDD是分布式的 Java对象的集合。DataFrame是分布式的Row对象的集合。DataFrame除了提供了比RDD更丰富的算子以外，更重要的特点是提升执行效率、减少数据读取以及执行计划的优化

#### 3.Datasets
&nbsp;&nbsp;&nbsp;&nbsp;Dataset是数据的分布式集合。Dataset是在Spark 1.6中添加的一个新接口，是DataFrame之上更高一级的抽象。它提供了RDD的优点（强类型化，使用强大的lambda函数的能力）以及Spark SQL优化后的执行引擎的优点。一个Dataset 可以从JVM对象构造，然后使用函数转换(map， flatMap，filter等)去操作。 Dataset API 支持Scala和Java。 Python不支持Dataset API。


### 四.创建DataFrames
#### 1.第一种方式：使用case class样本类创建DataFrames
##### (1)定义表的Schema
注意：由于mgr和comm列中包含null值，简单起见，将对应的case class类型定义为String
```java
scala> case class Emp(empno:Int,ename:String,job:String,mgr:String,hiredate:String,sal:Int,comm:String,depno:Int)
```

##### (2)读入数据
```java
//从hdfs中读入
scala> val lines = sc.textFile("hdfs://hadoop1:9000/emp.csv").map(_.split(","))

//从本地读入
scala> val lines = sc.textFile("/opt/module/datas/TestFile/emp.csv").map(_.split(","))
```
/opt/module/datas/TestFile 

##### (3)把每行数据映射到Emp中。把表结构和数据，关联。
```java
scala> val allEmp = lines.map(x => Emp(x(0).toInt,x(1),x(2),x(3),x(4),x(5).toInt,x(6),x(7).toInt))
```
##### (4)生成DataFrame
```java
scala> val allEmpDF = allEmp.toDF

//展示 
scala> allEmpDF.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFmZDU1NTU0Yzc3Nzc3ZDIucG5n?x-oss-process=image/format,png)

#### 2.第二种方式：使用SparkSession

##### (1)什么是SparkSession
&nbsp;&nbsp;&nbsp;&nbsp;Apache Spark 2.0引入了SparkSession，其为用户提供了一个统一的切入点来使用Spark的各项功能，并且允许用户通过它调用DataFrame和Dataset相关API来编写Spark程序。最重要的是，它减少了用户需要了解的一些概念，使得我们可以很容易地与Spark交互。
&nbsp;&nbsp;&nbsp;&nbsp;在2.0版本之前，与Spark交互之前必须先创建SparkConf和SparkContext。然而在Spark 2.0中，我们可以通过SparkSession来实现同样的功能，而不需要显式地创建SparkConf, SparkContext 以及 SQLContext，因为这些对象已经封装在SparkSession中。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlhMDk4MDMzYjY3OTI0N2MucG5n?x-oss-process=image/format,png)

##### (2)使用StructType，来创建Schema

```java
import org.apache.spark.sql.types._

val myschema = StructType(
				List(
				StructField("empno", DataTypes.IntegerType), 
				StructField("ename", DataTypes.StringType),
				StructField("job", DataTypes.StringType),
				StructField("mgr", DataTypes.IntegerType),
				StructField("hiredate", DataTypes.StringType),
				StructField("sal", DataTypes.IntegerType),
				StructField("comm", DataTypes.IntegerType),
				StructField("deptno", DataTypes.IntegerType)))
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNlYjM3MGNmYjk0ZDg5NmMucG5n?x-oss-process=image/format,png)

**注意，需要：import org.apache.spark.sql.types._**

##### (3)读取文件：
```java
val lines= sc.textFile("/opt/module/datas/TestFile/emp.csv").map(_.split(","))
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThjNzZjOTY2YzY3OTZiM2MucG5n?x-oss-process=image/format,png)

##### (4)数据与表结构匹配
```java
import org.apache.spark.sql.Row

val allEmp = lines.map(x => Row(x(0).toInt,x(1),x(2),x(3).toInt,x(4),x(5).toInt,x(6).toInt,x(7).toInt))
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVmMTA5ZTFmZmU1YTIwYzEucG5n?x-oss-process=image/format,png)

**注意，需要：import org.apache.spark.sql.Row**

##### (5)创建DataFrames
```java
val df2 = spark.createDataFrame(allEmp,myschema)

df2.show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEyODQwODNhOTJhMjJjNjAucG5n?x-oss-process=image/format,png)

#### 3.方式三，直接读取一个带格式的文件：Json
##### (1)读取文件：
```java
val df3 = spark.read.json("/opt/module/datas/TestFile/emp.json")

df3.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYxYTRmOWZhZTY5M2VmMTUucG5n?x-oss-process=image/format,png)

##### (2)另一种方式

```java
val df4 = spark.read.format("json").load("/opt/module/datas/TestFile/emp.json")

df4.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgyZGE4NTUyMDMyMjdmZjIucG5n?x-oss-process=image/format,png)


### 五.操作DataFrame
DataFrame操作也称为无类型的Dataset操作

#### 1.DSL语句
##### (1)
```java
df1.show

df1.printSchema
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBlZGU0OWE4MTg5YzcyMGMucG5n?x-oss-process=image/format,png)
##### (2)
```java
df1.select("ename","sal").show

df1.select($"ename",$"sal",$"sal"+100).show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA5ZDc4ZTZkOTkxOWFjNTAucG5n?x-oss-process=image/format,png)

##### (3)$代表 取出来以后，再做一些操作

```java
df1.filter($"sal">2000).show

df1.groupBy($"depno").count.show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFmYjUwNjNhMTVkN2Y3ZTQucG5n?x-oss-process=image/format,png)

**完整的例子，请参考：**
[http://spark.apache.org/docs/2.1.0/api/scala/index.html#org.apache.spark.sql.Dataset](http://spark.apache.org/docs/2.1.0/api/scala/index.html#org.apache.spark.sql.Dataset)

#### 2.SQL语句
	
注意：不能直接执行sql。需要生成一个视图，再执行SQL。

##### (1)将DataFrame注册成表(视图)：
```java
df1.createOrReplaceTempView("emp")
```

##### (2)执行查询：
```sql
spark.sql("select * from emp").show

spark.sql("select * from emp where sal > 2000").show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBlOGVjNjQyMzMxYjIwY2IucG5n?x-oss-process=image/format,png)

```sql
spark.sql("select * from emp where depno=10").show

spark.sql("select depno,count(1) from emp group by depno").show 

spark.sql("select depno,sum(sal) from emp group by depno").show
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgwMGI1MzllMTFhZjIxMTcucG5n?x-oss-process=image/format,png)

```sql
df1.createOrReplaceTempView("emp12345")

spark.sql("select e.depno from emp12345 e").show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY1MzU2NDNlOGIyNzE2NTYucG5n?x-oss-process=image/format,png)

#### 3.多表查询
```java
case class Dept(deptno:Int,dname:String,loc:String)

val lines = sc.textFile("/opt/module/datas/TestFile/dept.csv").map(_.split(","))

val allDept = lines.map(x=>Dept(x(0).toInt,x(1),x(2)))

val df2 = allDept.toDF

df2.create

df2.createOrReplaceTempView("dept")

spark.sql("select dname,ename from emp12345,dept where emp12345.depno=dept.deptno").show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE4MWMzNGNmNTdkNzUwZDQucG5n?x-oss-process=image/format,png)

### 六.视图
#### 1.视图是一个虚表，不存储数据
		
#### 2.两种类型视图：			
##### (1)普通视图（本地视图）：只在当前Session有效
			
##### (2)全局视图：在不同Session中都有用。全局视图创建在命名空间中：global_temp 类似于一个库。
&nbsp;&nbsp;&nbsp;&nbsp;上面使用的是一个在Session生命周期中的临时views。在Spark SQL中，如果你想拥有一个临时的view，并想在不同的Session中共享，而且在application的运行周期内可用，那么就需要创建一个全局的临时view。并记得使用的时候加上global_temp作为前缀来引用它，因为全局的临时view是绑定到系统保留的数据库global_temp上。
###### (a)创建一个普通的view和一个全局的view
```java
df1.createOrReplaceTempView("emp1")

df1.createGlobalTempView("emp2")
```
###### (b)在当前会话中执行查询，均可查询出结果。
```java
spark.sql("select * from emp1").show
spark.sql("select * from global_temp.emp2").show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVkMTlkYmNjOWFhZDliNWUucG5n?x-oss-process=image/format,png)

###### (c )开启一个新的会话，执行同样的查询
```java
spark.newSession.sql("select * from emp1").show     //（运行出错）
spark.newSession.sql("select * from global_temp.emp2").show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE0ZWJlMDhmMDgyOGYxYTEucG5n?x-oss-process=image/format,png)

### 七.创建Datasets
&nbsp;&nbsp;&nbsp;&nbsp;DataFrame的引入，可以让Spark更好的处理结构数据的计算，但其中一个主要的问题是：缺乏编译时类型安全。为了解决这个问题，Spark采用新的Dataset API (DataFrame API的类型扩展)。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThjYzRmNDFhMjA2ZjNmMDIucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;Dataset是一个分布式的数据收集器。这是在Spark1.6之后新加的一个接口，兼顾了RDD的优点(强类型，可以使用功能强大的lambda)以及Spark SQL的执行器高效性的优点。所以可以把DataFrames看成是一种特殊的Datasets，即：Dataset(Row)

#### 1.方式一：使用序列
##### (1)定义case class
```java
scala >case class MyData(a:Int,b:String)
```
##### (2).生成序列，并创建DataSet
```java
scala >val ds = Seq(MyData(1,"Tom"),MyData(2,"Mary")).toDS
```
##### (3).查看结果
```java
scala >ds.show

ds.collect
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg0ZDk0ZDE2ZjIxNzhiZDEucG5n?x-oss-process=image/format,png)

#### 2.方式二：使用JSON数据
##### (1)定义case class
```java
case class Person(name: String, age: BigInt)
```
##### (2)通过JSON数据生成DataFrame
```java
val df = spark.read.format("json").load("/opt/module/datas/TestFile/people.json")
```
##### (3)将DataFrame转成DataSet
```java
df.as[Person].show
df.as[Person].collect
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRhODZlZjFjOGY0MGNhMGQucG5n?x-oss-process=image/format,png)

#### 3.方式三：使用其他数据(RDD的操作和DataFrame操作结合)

##### (1)需求：分词；查询出长度大于3的单词
###### (a)读取数据，并创建DataSet
```java
val linesDS = spark.read.text("/opt/module/datas/TestFile/test_WordCount.txt").as[String]
```
###### (b)对DataSet进行操作：分词后，查询长度大于3的单词
```java
val words = linesDS.flatMap(_.split(" ")).filter(_.length > 3)

words.show

words.collect
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQzMDUzNzIwMDAwMjhmN2IucG5n?x-oss-process=image/format,png)

##### (2)需求：执行WordCount程序
```java
val result = linesDS.flatMap(_.split(" ")).map((_,1)).groupByKey(x => x._1).count

result.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc2YmNkYzM3NDJmNjJiMTkucG5n?x-oss-process=image/format,png)
    
排序：
```java
result.orderBy($"value").show

result.orderBy($"count(1)").show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ2MDljMjVhMGM0NzRkNDYucG5n?x-oss-process=image/format,png)

### 八.Datasets的操作案例
#### 1.使用emp.json 生成DataFrame

##### (1)数据：emp.json

##### (2)使用emp.json 生成DataFrame
```java
val empDF = spark.read.json("/opt/module/datas/TestFile/emp.json")

emp.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk5N2RlYWQyNTFhZjAxMWMucG5n?x-oss-process=image/format,png)
  
查询工资大于3000的员工
```java
empDF.where($"sal" >= 3000).show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY3NjdmZDNmNDA2YjczZGEucG5n?x-oss-process=image/format,png)

##### (3)创建case class,生成DataSets
```java
case class Emp(empno:Long,ename:String,job:String,hiredate:String,mgr:String,sal:Long,comm:String,deptno:Long)

val empDS = empDF.as[Emp]
```
##### (4)查询数据

```java
//查询工资大于3000的员工
empDS.filter(_.sal > 3000).show

//查看10号部门的员工 
empDS.filter(_.deptno == 10).show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ3OTE4MTZlZGEzMzllMGEucG5n?x-oss-process=image/format,png)

#### 2.多表查询
##### (1)创建部门表
```java
val deptRDD=sc.textFile("/opt/module/datas/TestFile/dept.csv").map(_.split(","))

case class Dept(deptno:Int,dname:String,loc:String)

val deptDS = deptRDD.map(x=>Dept(x(0).toInt,x(1),x(2))).toDS

deptDS.show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAzMzMzMmUwMzMzZjM0YmIucG5n?x-oss-process=image/format,png)

##### (2)创建员工表
```java
case class Emp(empno:Int,ename:String,job:String,mgr:String,hiredate:String,sal:Int,comm:String,deptno:Int)

val empRDD = sc.textFile("/opt/module/datas/TestFile/emp.csv").map(_.split(","))

val empDS = empRDD.map(x => Emp(x(0).toInt,x(1),x(2),x(3),x(4),x(5).toInt,x(6),x(7).toInt)).toDS

empDS.show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNkNDhiNTMyMjFkNWIyNzYucG5n?x-oss-process=image/format,png)

##### (3)执行多表查询：等值链接
```java
val result = deptDS.join(empDS,"deptno")

result.show
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEyNGIyNWM1NjIyYzU2MzYucG5n?x-oss-process=image/format,png)

##### (4)另一种写法：注意有三个等号
```java
val result1 = deptDS.joinWith(empDS,deptDS("deptno")=== empDS("deptno"))

result1.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBiNjgxZTBiZTY2NjQ1NmQucG5n?x-oss-process=image/format,png)

joinWith和join的区别是连接后的新Dataset的schema会不一样

##### (5)多表条件查询：
```java
val result = deptDS.join(empDS,"deptno").where("deptno==10") 

result.show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk4NWJiYjVjMDZkYmU1ZDIucG5n?x-oss-process=image/format,png)
