---
title: 'Hive基本操作'
date: 2019-07-15 13:00:00
tags: Hive
categories: Hive
---

### 一.Hive基本操作
#### 1.启动hive
```shell
bin/hive
```
#### 2.查看数据库
```	shell
show databases;
```
#### 3.打开默认数据库
```shell
use default;
```
#### 4.显示default数据库中的表
```shell
show tables;
```
#### 5.创建一张表
```shell
create table student(id int, name string) ;
```
#### 6.显示数据库中有几张表
```shell
show tables;
```
#### 7.查看表的结构
```shell
desc student;
```
#### 8.向表中插入数据
```shell
insert into student values(1001,"ss1");
```
#### 9.查询表中数据
```shell
select * from student;
```
#### 10.退出hive
```	shell
quit;
```
### 二.hive常用交互命令
#### 1.“-e”不进入hive的交互窗口执行sql语句
```shell
bin/hive -e "select id from student;"
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYwZTJiNzBhOGYxZDQ1OTMucG5n?x-oss-process=image/format,png)
#### 2.“-f”执行脚本中sql语句
##### (1)在/opt/module/datas目录下创建hivef.sql文件
```shell
touch hive.sql
```	
文件中写入正确的sql语句
```shell
select * from student;
```	

##### (2)执行文件中的sql语句
```shell
bin/hive -f /opt/module/datas/hive.sql
```

##### (3)执行文件中的sql语句并将结果写入文件中(注：可能含有其他的表信息，如表头)
```shell
bin/hive -f /opt/module/datas/hive.sql  > /opt/module/datas/hive_result.txt
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ3ZDc5NDlhOTc3NjczMzgucG5n?x-oss-process=image/format,png)


### 三.Hive其他命令操作
#### 1.退出hive窗口：	
```shell
exit;
quit;
```
在新版的oracle中没区别了，在以前的版本是有的：
exit:先隐性提交数据，再退出；
quit:不提交数据，退出；

#### 2.在hive cli命令窗口中如何查看hdfs文件系统
```shell
dfs -ls /;
```
#### 3.在hive cli命令窗口中如何查看linux本地系统
```shell
! ls /opt/module/datas;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVjNDU2MjE5ZTRhODE0MjIucG5n?x-oss-process=image/format,png)
#### 4.查看在hive中输入的所有历史命令
##### (1)进入到当前用户的根目录/root或/home/itstar
##### (2)查看. hivehistory文件
```shell
cat .hivehistory
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTExYjlkMjUyMWVmYjMyMDkucG5n?x-oss-process=image/format,png)
