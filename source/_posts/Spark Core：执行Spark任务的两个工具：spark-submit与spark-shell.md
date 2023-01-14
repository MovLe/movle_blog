---
title: 'Spark Core：执行Spark任务的两个工具：spark-submit与spark-shell'
date: 2020-03-10 14:00:00
tags: 
- Spark
- SparkCore
categories: Spark
---
#### 1.spark-submit:用于提交Spark任务
##### (1)举例：spark 自带的实例程序。
/opt/module/spark-2.1.0-bin-hadoop2.7/examples/jars中有Spark自带的实例程序。

蒙特卡洛求PI（圆周率）
```shell
cd /opt/module/spark-2.1.0-bin-hadoop2.7

bin/spark-submit --master spark://hadoop1:7077 --class org.apache.spark.examples.SparkPi /opt/module/spark-2.1.0-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.1.0.jar 500
```
![启动命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM4OWFmMGEwZjAyZTllZmEucG5n?x-oss-process=image/format,png)
![执行结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFlMDIzYzBlNGRhN2RjNjUucG5n?x-oss-process=image/format,png)

#### 2.Spark-shell
##### (1)概念：相当于REPL工具，命令行工具，作为一个独立的Application运行
		
##### (2)两种运行模式：
###### (a)本地模式：不需要连接到Spark集群，在本地直接运行，用于测试

启动：
```shell
//后面不写任何参数，代表本地模式
bin/spark-shell  
```

![本地模式](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE2YzdjODk5OTc0MzE1ZWUucG5n?x-oss-process=image/format,png)
local代表本地模式
			
###### (b)集群模式
命令：
```shell
bin/spark-shell --master spark://hadoop1:7077
```
![集群模式](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEzYzBiNzUwYjk0ODRkZWQucG5n?x-oss-process=image/format,png)

特殊说明：
* Spark session(spark) : Spark2.0以后提供的，利用session可以访问所有spark组件(core sql..)
* spark context(sc) 两个对象，可以直接使用