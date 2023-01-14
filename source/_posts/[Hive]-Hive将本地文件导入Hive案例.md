---
title: '[Hive]-Hive将本地文件导入Hive案例'
date: 2019-07-15 16:00:00
tags: Hive
categories: Hive
---

#### 0.需求：
将本地/opt/module/datas/student.txt这个目录下的数据导入到hive的student(id int, name string)表中。

#### 1.数据准备：在/opt/module/datas/student.txt这个目录下准备数据
##### (1)在/opt/module/目录下创建datas
```shell
mkdir datas
```
##### (2)在/opt/module/datas/目录下创建student.txt文件并添加数据
```	shell
touch student.txt
vi student.txt
```
添加内容：
```shell
1001	zhangshan
1002	lishi
1003	zhaoliu
```
注意以tab键间隔。
#### 2.Hive实际操作
##### (1)启动hive
```shell
bin/hive
```
##### (2)显示数据库
```shell
show databases;
```
##### (3)使用default数据库
```shell
use default;
```
##### (4)显示default数据库中的表
```shell
show tables;
```
##### (5)删除已创建的student表
```shell
drop table student;
```
##### (6)创建student表, 并声明文件分隔符’\t’
```shell
create table student(id int, name string) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';
```
##### (7)加载/opt/module/datas/student.txt 文件到student数据库表中。
```shell
load data local inpath '/opt/module/datas/student.txt' into table student;
```
##### (8)Hive查询结果
```shell
select * from student;
```

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWIxOWRjMWI0YTNiM2Q5ZjcucG5n?x-oss-process=image/format,png)

**可能是编码的问题吧，留个坑**
在hdfs中就是正常的，所以应该是格式问题

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQyYjVjMmZmYzQ3YzQ0ZjYucG5n?x-oss-process=image/format,png)