---
title: 'Spark SQL:性能优化'
date: 2020-03-11 12:00:00
tags: 
- Spark
- Spark SQL
categories: Spark
---


#### 1.在内存中缓存数据
&nbsp;&nbsp;&nbsp;&nbsp;性能调优主要是将数据放入内存中操作。通过spark.cacheTable("tableName")或者dataFrame.cache()。使用spark.uncacheTable("tableName")来从内存中去除table。

**Demo案例：**
##### (1)从Oracle数据库中读取数据，生成DataFrame
```java
val mysqlDF = spark.read.format("jdbc")
        .option("url","jdbc:mysql://192.168.109.1:3306/company?serverTimezone=UTC&characterEncoding=utf-8")
        .option("dbtable","company.emp")
        .option("user","root")
        .option("password","000000").load
```
```java
scala> val mysqlDF = spark.read.format("jdbc").option("url","jdbc:mysql://192.168.109.1:3306/company?serverTimezone=UTC&characterEncoding=utf-8").option("user","root").option("password","000000").option("dbtable","emp").option("driver","com.mysql.jdbc.Driver").load
```

##### (2)将DataFrame注册成表：    
```java
mysqlDF.registerTempTable("emp")
```

注意：必须注册成一张表，才可以缓存

##### (3)执行查询，并通过Web Console监控执行的时间
```sql
spark.sql("select * from emp").show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRjODJkYzJkOTE0ZDA0YmYucG5n?x-oss-process=image/format,png)

##### (4)将表进行缓存，并查询两次，并通过Web Console监控执行的时间
```sql
spark.sqlContext.cacheTable("emp")

spark.sql("select * from emp").show
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE0ZjkxOGNjMDU5NzllOWMucG5n?x-oss-process=image/format,png)


##### (5)清空缓存：
```java
spark.sqlContext.cacheTable("emp")

spark.sqlContext.clearCache
```
#### 2.性能优化相关参数
##### (1)将数据缓存到内存中的相关优化参数
```sql
spark.sql.inMemoryColumnarStorage.compressed
//默认为 true
//Spark SQL 将会基于统计信息自动地为每一列选择一种压缩编码方式。

spark.sql.inMemoryColumnarStorage.batchSize
//默认值：10000
//缓存批处理大小。缓存数据时, 较大的批处理大小可以提高内存利用率和压缩率，但同时也会带来 OOM（Out Of Memory）的风险。
```
##### (2)其他性能相关的配置选项(不过不推荐手动修改，可能在后续版本自动的自适应修改)
```sql
spark.sql.files.maxPartitionBytes
//默认值：128 MB
//读取文件时单个分区可容纳的最大字节数

spark.sql.files.openCostInBytes
//默认值：4M
//打开文件的估算成本, 按照同一时间能够扫描的字节数来测量。当往一个分区写入多个文件的时候会使用。高估更好, 这样的话小文件分区将比大文件分区更快 (先被调度)。

spark.sql.autoBroadcastJoinThreshold
//默认值：10M
//用于配置一个表在执行 join 操作时能够广播给所有 worker 节点的最大字节大小。通过将这个值设置为 -1 可以禁用广播。注意，当前数据统计仅支持已经运行了 ANALYZE TABLE <tableName> COMPUTE STATISTICS noscan 命令的 Hive Metastore 表。

spark.sql.shuffle.partitions
//默认值：200
//用于配置 join 或聚合操作混洗(shuffle)数据时使用的分区数。
```
