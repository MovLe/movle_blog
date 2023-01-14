---
title: 'HBase-MapReduce案例：统计表中数据，使用MapReduce将本地数据导入Hbase'
date: 2019-04-09 12:00:00
tags: 
- HBase
- HBase实战
categories: HBase
---

### 一.HBase的MapReduce任务过程
#### 1.查看HBase的MapReduce任务所需的依赖：
```shell
cd /opt/module/hbase-1.3.1

bin/hbase mapredcp
```

#### 2.执行环境变量的导入
```shell
export HBASE_HOME=/opt/module/hbase-1.3.1
export HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase mapredcp`
```
#### 3.运行官方的MapReduce任务

### 二.案例一：统计default:student表中有多少行数据
```shell
cd /opt/module/hbase-1.3.1

/opt/module/hadoop-2.8.4/bin/yan jar lib/hbase-server-1.3.1.jar rowcounter default:student
```
![命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjM2U1YjljNjkzYTMyYzcucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ5OTMxOWQyMWM5OThiM2QucG5n?x-oss-process=image/format,png)

### 三.案例二：使用MapReduce将本地数据导入到HBase
##### (1) 在本地创建一个tsv格式的文件：fruit.tsv，自己建表用\t分割数据 

```txt
1001	Apple	Red
1002	Pear	Yellow
1003	Pineapple	Yellow
```
尖叫提示：上面的这个数据不要从word中直接复制，有格式错误
##### (2) 创建HBase表 
```shell
hbase(main):001:0> create 'fruit','info'
```
##### (3) 在HDFS中创建input_fruit文件夹并上传fruit.tsv文件
```shell
hdfs dfs -mkdir /input_fruit/
hdfs dfs -put fruit.tsv /input_fruit/
```
##### (4) 执行MapReduce到HBase的fruit表中
```shell
/opt/module/hadoop-2.8.4/bin/yarn jar lib/hbase-server-1.3.1.jar importtsv \
-Dimporttsv.columns=HBASE_ROW_KEY,info:name,info:color fruit \
hdfs://hadoop2:9000/input_fruit
```
![执行](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIxMDlhZTc5MGNjOTlmMzkucG5n?x-oss-process=image/format,png)

##### (5) 使用scan命令查看导入后的结果
```shell
hbase(main):001:0> scan 'fruit' 
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk0OGNiNTkwY2Y2NWJhNDAucG5n?x-oss-process=image/format,png)
