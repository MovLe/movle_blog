---
title: 'Sqoop简单使用案例'
date: 2019-05-09 13:00:00
tags: Sqoop
categories: Sqoop
---

### 一.导入数据

在Sqoop中，“导入”概念指：从非大数据集群(RDBMS)向大数据集群(HDFS，HIVE，HBASE)中传输数据，叫做：导入，即使用import关键字。

#### 1.RDBMS到HDFS

##### (1).确定Mysql服务开启正常

##### (2).在Mysql中新建一张表并插入一些数据
```shell
$ mysql -uroot -p000000

mysql> create database company;

mysql> create table company.staff(id int(4) primary key not null auto_increment, name varchar(255), sex varchar(255));

mysql> insert into company.staff(name, sex) values('Thomas', 'Male');

mysql> insert into company.staff(name, sex) values('Catalina', 'FeMale');
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZiYTE2OWNlNjNlMDc1ZjcucG5n?x-oss-process=image/format,png)

##### (3).导入数据
###### (a)全部导入
```shell
bin/sqoop import \
--connect jdbc:mysql://hadoop1:3306/company \
--username root \
--password 000000 \
--table staff \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t"
```
![导入命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZkYTY4MmMxYmVkMDQ1MDkucG5n?x-oss-process=image/format,png)

![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE1Yjg4OWEzNmNlODEwZGYucG5n?x-oss-process=image/format,png)

###### (b)查询导入
```shell
bin/sqoop import \
--connect jdbc:mysql://hadoop1:3306/company \
--username root \
--password 000000 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--query 'select name,sex from staff where id <=3 and $CONDITIONS;'
```
![查询导入命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNiZTY5MDhiYTg0M2ZmZjMucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEzMmMyYWZjZDk1MjVlYTgucG5n?x-oss-process=image/format,png)

**提示**：must contain '$CONDITIONS' in WHERE clause.     
注：CONDITIONS 翻译‘条件’

**提示**：如果query后使用的是双引号，则$CONDITIONS前必须加转移符，防止shell识别为自己的变量。

###### (c )导入指定列
```shell
bin/sqoop import \
--connect jdbc:mysql://hadoop1:3306/company \
--username root \
--password 000000 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--columns id,name \
--table staff
```
![导入命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNjYjhmY2Y2ZjgzN2FkYWIucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE2MzBiM2Y0Yzg5NzZlOTgucG5n?x-oss-process=image/format,png)

**提示**：columns中如果涉及到多列，用逗号分隔，分隔时不要添加空格

###### (d)使用sqoop关键字筛选查询导入数据
```shell
bin/sqoop import \
--connect jdbc:mysql://hadoop1:3306/company \
--username root \
--password 000000 \
--target-dir /user/company \
--delete-target-dir \
--num-mappers 1 \
--fields-terminated-by "\t" \
--table staff \
--where "id=2"
```
![导入命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAyMTk0N2QxNzdhYzAyMWUucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcxMDg1MTk0NmRmNGI4ZDUucG5n?x-oss-process=image/format,png)


**提示**：在Sqoop中可以使用sqoop import -D property.name=property.value这样的方式加入执行任务的参数，多个参数用空格隔开。

#### 2.、RDBMS到Hive
```shell
$ bin/sqoop import \
--connect jdbc:mysql://hadoop1:3306/Movle \
--username root \
--password 000000 \
--table aca \
--delete-target-dir \
--num-mappers 1 \
--hive-import \
--fields-terminated-by "\t" \
--hive-overwrite \
--hive-table student_hive
```
![mysql数据库中表的内容](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQyNjk1ODQ1YmM4ZmUxNDMucG5n?x-oss-process=image/format,png)
![导入命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJhMzRkN2RlMjU1ZTUwZDYucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY0ODBjMTY4YTZkZjFkYmUucG5n?x-oss-process=image/format,png)


**提示**：该过程分为两步，第一步将数据导入到HDFS，第二步将导入到HDFS的数据迁移到Hive仓库

尖叫提示：从MYSQL到Hive，本质时从MYSQL => HDFS => load To Hive

### 二.导出数据

在Sqoop中，“导出”概念指：从大数据集群(HDFS，HIVE，HBASE)向非大数据集群(RDBMS)中传输数据，叫做：导出，即使用export关键字。

#### 1.HIVE/HDFS到RDBMS

##### (1)创建adc表
```shell
create table Movle.adc(id int,name VARCHAR(10));
```
![创建mysql 表adc](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWIzNjk3NzcyZjM1YmVlZTMucG5n?x-oss-process=image/format,png)
##### (2)导入
```shell
bin/sqoop export \
--connect jdbc:mysql://hadoop1:3306/Movle \
--username root \
--password 000000 \
--export-dir /user/hive/warehouse/student_hive \
--table adc \
--num-mappers 1 \
--input-fields-terminated-by "\t"
```



![导入命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdiYWJlNmNlZDc0YzhiZWUucG5n?x-oss-process=image/format,png)
![Hive表中的数据](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMwY2FiNDRjN2EyZjk0MjgucG5n?x-oss-process=image/format,png)
![导入后mysql中的数据](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJhNTc5Y2JjMGI1N2VmNDUucG5n?x-oss-process=image/format,png)

**提示**：Mysql中如果表不存在，不会自动创建,自行根据表结构创建

**提示**：重复往mysql的统一个表中导出数据，mysql的表不能设置主键和自增。

尖叫提示：如果数据导出mysql中是“？？”那么添加characterEncoding=utf-8

思考：数据是覆盖还是追加 答案：追加

### 三.脚本打包

使用opt格式的文件打包sqoop命令，然后执行

#### 1.创建一个.opt文件
```shell
$ touch job_HDFS2RDBMS.opt
```

#### 2.编写sqoop脚本
```shell
$ vi ./job_HDFS2RDBMS.opt
```
以下命令是从staff_hive中追加导入到mysql的aca表中
```shell
export
--connect
jdbc:mysql://hadoop1:3306/Movle
--username
root
--password
000000
--table
adc
--num-mappers
1
--export-dir
/user/hive/warehouse/student_hive
--input-fields-terminated-by
"\t"
```

![job_HDFS2RDBMS.opt](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVjZThmMDFlZmUzZDdlOGEucG5n?x-oss-process=image/format,png)

#### 3.执行该脚本
```shell
$ bin/sqoop --options-file job_HDFS2RDBMS.opt
```

![执行脚本](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk4MDU5YWFmMWFjMDJkYTAucG5n?x-oss-process=image/format,png)
![执行前mysql表adc](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ4OGVjYjJhMzUwMDJhMmIucG5n?x-oss-process=image/format,png)
![执行后mysql表adc](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAwZDM3MmY4YTY2ZDFmZDAucG5n?x-oss-process=image/format,png)