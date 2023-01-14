---
title: 'Azkaban实战-多job工作流案例'
date: 2019-06-02 14:00:00
tags: 
- Azkaban
- Azkaban实战
categories: Azkaban
---

#### 0.数据源：

word.txt:
```txt
AAA
BBB
DDD
CCC
AAA
Movle
Kai Movle
BBB yue 
```
![word.txt](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE5ZTBkOTExYmIxYTI3M2MucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTczMmM4ZjNmM2YyZGQwODUucG5n?x-oss-process=image/format,png)

#### 1.创建有依赖关系的多个job描述
##### (1)第一个job：1.job
```shell
vi 1.job
```
添加内容：
```shell
type=command
command=/opt/module/hadoop-2.8.4/bin/hadoop fs -put /opt/module/datas/word.txt /
```
##### (2)第二个job：2.job依赖1.job
```shell
vi 2.job
```
添加内容：
```shell
type=command
command=/opt/module/hadoop-2.8.4/bin/hadoop jar /opt/module/hadoop-2.8.4/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.4.jar wordcount /word.txt /out
dependencies=1
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJiNzE4YWNhZTE1YTk4Y2MucG5n?x-oss-process=image/format,png)

#### 2.注意：将所有job资源文件打到一个zip包中

#### 3.在azkaban的web管理界面创建工程并上传zip包
![执行](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI5MGEyMWY3ZmJjMzc5YjUucG5n?x-oss-process=image/format,png)

#### 4.查看结果
![结果-查看对word.txt进行wordcount的结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI0ZGEyNDVmN2E5NmY3MmEucG5n?x-oss-process=image/format,png)
![2.job的运行结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIwN2ZlODRjMjVmOTQyMjAucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI5YTcwOGIxZmJhOGZkMzkucG5n?x-oss-process=image/format,png)

思考：

将student.txt文件上传到hdfs，根据所传文件创建外部表，再将表中查询到的结果写入到本地文件
