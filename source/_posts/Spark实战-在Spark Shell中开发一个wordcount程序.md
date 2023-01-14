---
title: 'Spark实战-在Spark Shell中开发一个wordcount程序'
date: 2020-03-12 12:00:00
tags: 
- Spark
- Spark实战
categories: Spark
---

#### 1.读取一个本地文件，将结果打印到屏幕上。
注意：示例必须只有一个worker 且本地文件与worker在同一台服务器上。
```shell
scala> sc.textFile("/opt/TestFile/test_WordCount.txt").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
```			
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUwZmVmMTQwZDMzOTYzZjQucG5n?x-oss-process=image/format,png)		
#### 2.读取一个hdfs文件，进行WordCount操作，并将结果写回hdfs
```shell
scala> sc.textFile("hdfs://hadoop:9000/TestFile/test_WordCount.txt").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).saveAsTextFile("hdfs://hadoop:9000/output/1208")
			
[root@hadoop sbin]# hadoop dfs -ls /output/1208
```	
![输入命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjZTJkYjY3YzMzOTUzMTQucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJlM2EyYmU2MWY3MTRmZjcucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY3MzUzYmQxMDYxNTgyMmIucG5n?x-oss-process=image/format,png)

#### 3.单步运行WordCount  ----->  RDD
```shell			
scala> val rdd1 = sc.textFile("/opt/TestFile/test_WordCount.txt")

scala> rdd1.collect

scala> val rdd2 = rdd1.flatMap(_.split(" "))

scala> rdd2.collect

scala> val rdd3 = rdd2.map((_,1))

scala> rdd3.collect

scala> val rdd4 = rdd3.reduceByKey(_+_)

scala> rdd4.collect
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU5NGM3ZDYxMTUwNWQyMjgucG5n?x-oss-process=image/format,png)

##### (1)RDD说明：RDD 弹性分布式数据集
##### (2)特性：
###### (a)依赖关系:rdd2依赖于rdd1
###### (b)算子
* Transformation	延时计算: flatMap,map,reduceByKey
* Action  触发计算:collect