---
title: 'Phoenix集成HBase'
date: 2019-04-10 11:00:00
tags: 
- HBase
- HBase实战
- Phoenix
categories: HBase
---

### 一. Phoenix介绍

&nbsp;&nbsp;&nbsp;&nbsp;可以把Phoenix理解为Hbase的查询引擎，phoenix，由saleforce.com开源的一个项目，后又捐给了Apache。它相当于一个Java中间件，帮助开发者，像使用jdbc访问关系型数据库一些，访问NoSql数据库HBase。

&nbsp;&nbsp;&nbsp;&nbsp;phoenix，操作的表及数据，存储在hbase上。phoenix只是需要和Hbase进行表关联起来。然后再用工具进行一些读或写操作。

&nbsp;&nbsp;&nbsp;&nbsp;其实，可以把Phoenix只看成一种代替HBase的语法的一个工具。虽然可以用java可以用jdbc来连接phoenix，然后操作HBase，但是在生产环境中，不可以用在OLTP中。在线事务处理的环境中，需要低延迟，而Phoenix在查询HBase时，虽然做了一些优化，但**延迟还是不小**。所以依然是用在OLAT中，再将结果返回存储下来。

### 二.安装
#### 1.phoenix安装包解压缩，更换目录
```shell
tar -zxvf apache-phoenix-4.14.1-HBase-1.2-bin.tar.gz -C /opt/module

mv apache-phoenix-4.14.1-HBase-1.2-bin phoenix-4.14.1
```
#### 2.修改环境变量
```shell
vi /etc/profile

#在最后两行加上如下phoenix配置
export PHOENIX_HOME=/opt/module/phoenix-4.14.1

export PATH=$PATH:$PHOENIX_HOME/bin
```
#### 3.使环境变量配置生效
```shell
source /etc/profile
```
#### 4.将主节点的phoenix包传到从节点
```shell
scp -r phoenix-4.14.1 root@bigdata13:/opt/module

scp -r phoenix-4.14.1 root@bigdata12:/opt/module
```
#### 5.拷贝hbase-site.xml（注）三台都要
```shell
cp hbase-site.xml /opt/module/phoenix-4.14.1/bin/
```
将如下两个jar包，目录在/opt/module/phoenix-4.14.1下，拷贝到hbase的lib目录，目录在/opt/module/hbase-1.3.1/lib/
（注）三台都要
```shell
phoenix-4.10.0-HBase-1.2-server.jar

phoenix-core-4.10.0-HBase-1.2.jar
```
#### 6.启动Phoenix

配置好之后重启下hbase。
```shell
bin/sqlline.py bigdata11:2181
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUxZGFkOGFiMDc0MThmNGYucG5n?x-oss-process=image/format,png)


### 三.基本命令

#### 1.展示表
```shell
!table
```
#### 2.创建表
```shell
create table test(id integer not null primary key,name varchar);

create table "Andy"(
id integer not null primary key,
name varchar);
```
#### 3.删除表
```shell
drop table test;
```
#### 4.插入数据
```shell
upsert into test values(1,'Andy');

upsert into users(name) values('toms');
```
#### 5.查询数据
```shell
phoenix > select * from test;
```
```shell
hbase > scan 'test'
```
#### 6.退出phoenix
```shell
!q
```
#### 7.删除数据
```shell
delete from "Andy" where id=4;
```
#### 8.sum函数的使用
```shell
select sum(id) from "Andy";
```
#### 9.增加一列
```shell
alter table "Andy" add address varchar;
```
#### 10.删除一列
```shell
alter table "Andy" drop column address;
```
其他语法详见：[http://phoenix.apache.org/language/index.html](http://phoenix.apache.org/language/index.html)

### 四.表映射

#### 1.hbase中创建表
```shell
create 'teacher','info','contact'
```
#### 2.插入数据
```shell
put 'teacher','1001','info:name','Jack'

put 'teacher','1001','info:age','28'

put 'teacher','1001','info:gender','male'

put 'teacher','1001','contact:address','shanghai'

put 'teacher','1001','contact:phone','13458646987'

put 'teacher','1002','info:name','Jim'

put 'teacher','1002','info:age','30'

put 'teacher','1002','info:gender','male'

put 'teacher','1002','contact:address','tianjian'

put 'teacher','1002','contact:phone','13512436987'
```
#### 3.在Phoenix创建映射表
```shell
create view "teacher"(

"ROW" varchar primary key,

"contact"."address" varchar,

"contact"."phone" varchar,

"info"."age"  varchar,

"info"."gender" varchar,

"info"."name" varchar

);
```
#### 4.在Phoenix查找数据
```shell
select * from "teacher";
```