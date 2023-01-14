---
title: 'Sqoop集成HBase：Mysql TO HBase'
date: 2019-04-10 15:00:00
tags: 
- HBase
- HBase实战
- Sqoop
categories: HBase
---


### 一.Sqoop集成HBase
#### 1.利用Sqoop在HBase和RDBMS中进行数据的转储。
#### 2.相关参数：
|参数|描述|
|:-:|:-:|
|--column-family <family\>|设置导入的目标列族|
|--hbase-create-table|是否自动创建不存在的HBase表(这就意味着，不需要手动提前在HBase中先建立表)|
|--hbase-row-key <col\>|mysql中哪一列的值作为HBase的rowkey，如果rowkey是个组合键，则以逗号分隔(注：避免rowkey的重复)|
|--hbase-table <table-name\>|指定数据将要导入到HBase中的哪张表中|
|--hbase-bulkload|是否允许bulk形式的导入|


### 二. 案例

#### 1.目标：将RDBMS中的数据抽取到HBase中

#### 2.分步实现：
##### (1) 配置sqoop-env.sh，添加如下内容：
```shell
export HBASE_HOME=/opt/module/hbase-1.3.1
```
##### (2) 在Mysql中新建一个数据库db_library，一张表book

进入mysql
```shell
mysql -uroot -p000000
```
建库建表
```shell
CREATE DATABASE db_library;

CREATE TABLE db_library.book(
id int(4) PRIMARY KEY NOT NULL AUTO_INCREMENT, 
name VARCHAR(255) NOT NULL, 
price VARCHAR(255) NOT NULL);
```
##### (3) 向表中插入一些数据
```shell
INSERT INTO db_library.book (name, price) VALUES('Lie Sporting', '30');  
INSERT INTO db_library.book (name, price) VALUES('Pride & Prejudice', '70');  
INSERT INTO db_library.book (name, price) VALUES('Fall of Giants', '50');
```
![mysql](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQzOTg5OWQ5Mjg2NTZjZWUucG5n?x-oss-process=image/format,png)

##### (4) 执行Sqoop导入数据的操作
手动创建HBase表
```shell
hbase> create 'hbase_book','info'
```
##### (5) 在HBase中scan这张表得到如下内容
```shell
hbase> scan 'hbase_book'
```
思考：尝试使用复合键作为导入数据时的rowkey。
##### (6)利用sqoop导入
```shell
bin/sqoop import \
--connect jdbc:mysql://hadoop2:3306/db_library \
--username root \
--password 000000 \
--table book \
--columns "id,name,price" \
--column-family "info" \
--hbase-create-table \
--hbase-row-key "id" \
--hbase-table "hbase_book" \
--num-mappers 1 \
--split-by id
```
![sqoop导入](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAwMzgzMGFmMzQyMzRjZmIucG5n?x-oss-process=image/format,png)

提示：sqoop1.4.6只支持HBase1.0.1之前的版本的自动创建HBase表的功能
##### (7)结果：
```shell
hbase> scan 'hbase_book'
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVmZWQyZTY0ZTE2Nzc5MjgucG5n?x-oss-process=image/format,png)
