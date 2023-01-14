---
title: 'HBase与Hive的集成'
date: 2019-04-10 13:00:00
tags: 
- HBase
- HBase实战
- Hive
categories: HBase
---

### 一.HBase与Hive的对比
||Hive|Hbase|
|:-:|:-:|:-:|
|特点|类SQL 数据仓库|NoSQL (Key-value)|
|适用场景|离线数据分析和清洗|适合在线业务|
|延迟|延迟高|延迟低|
|存储位置|存储在HDFS|存储在HDFS|

### 二.HBase与Hive集成使用
#### 1.环境准备
因为后续会在操作Hive的同时对HBase也会产生影响，所以Hive需要持有操作HBase的Jar，那么接下来拷贝Hive所依赖的Jar包(或者使用软连接的形式),记得还有把zookeeper的jar包考入到hive的lib目录下。

##### (1)在/etc/profile文件中添加环境变量
```shell
#环境变量/etc/profile
export HBASE_HOME=/opt/module/hbase-1.3.1
export HIVE_HOME=/opt/module/hive-1.2.1
```
##### (2)进入hive的lib目录
```shell
cd /opt/module/hive-1.2.1/lib/
```
##### (3)进行软连接
```shell
ln -s $HBASE_HOME/lib/hbase-common-1.3.1.jar  $HIVE_HOME/lib/hbase-common-1.3.1.jar
ln -s $HBASE_HOME/lib/hbase-server-1.3.1.jar $HIVE_HOME/lib/hbase-server-1.3.1.jar
ln -s $HBASE_HOME/lib/hbase-client-1.3.1.jar $HIVE_HOME/lib/hbase-client-1.3.1.jar
ln -s $HBASE_HOME/lib/hbase-protocol-1.3.1.jar $HIVE_HOME/lib/hbase-protocol-1.3.1.jar
ln -s $HBASE_HOME/lib/hbase-it-1.3.1.jar $HIVE_HOME/lib/hbase-it-1.3.1.jar
ln -s $HBASE_HOME/lib/htrace-core-3.1.0-incubating.jar $HIVE_HOME/lib/htrace-core-3.1.0-incubating.jar
ln -s $HBASE_HOME/lib/hbase-hadoop2-compat-1.3.1.jar $HIVE_HOME/lib/hbase-hadoop2-compat-1.3.1.jar
ln -s $HBASE_HOME/lib/hbase-hadoop-compat-1.3.1.jar $HIVE_HOME/lib/hbase-hadoop-compat-1.3.1.jar
```
![软连接](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgyNGIxOGMzYTA0MTExMWYucG5n?x-oss-process=image/format,png)
![软连接](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVlNzFmMDEwNDNlNmY4ZjgucG5n?x-oss-process=image/format,png)

##### (4)同时在hive-site.xml中修改zookeeper的属性，如下：
```xml
<property>
    <name>hive.zookeeper.quorum</name>
    <value>hadoop2,hadoop3,hadoop4</value>
    <description>The list of ZooKeeper servers to talk to. This is only needed for read/write locks.</description>
</property>
<property>
    <name>hive.zookeeper.client.port</name>
    <value>2181</value>
    <description>The port of ZooKeeper servers to talk to. This is only needed for read/write locks.</description>
</property>
```
![hive-site.xml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRjYTExMWI0OTBlZjVmZDQucG5n?x-oss-process=image/format,png)

### 三.案例
#### 1.案例一
目标：建立Hive表，关联HBase表，插入数据到Hive表的同时能够影响HBase表。
分步实现：
##### (1) 在Hive中创建表同时关联HBase
```shell
CREATE TABLE hive_hbase_emp_table1(
empno int,
ename string,
job string,
mgr int,
hiredate string,
sal double,
comm double,
deptno int)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,info:ename,info:job,info:mgr,info:hiredate,info:sal,info:comm,info:deptno")
TBLPROPERTIES ("hbase.table.name" = "hbase_emp_table1");
```
提示：完成之后，可以分别进入Hive和HBase查看，都生成了对应的表

原有的hive-hbase-handler-1.2.1.jar可能有问题，换一个
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJlNDUxMWVlNjk2MjQ3ODUucG5n?x-oss-process=image/format,png)
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTliY2Y2MWM4NTIzZmY0NzYucG5n?x-oss-process=image/format,png)

##### (2) 在Hive中创建临时中间表，用于load文件中的数据

提示：不能将数据直接load进Hive所关联HBase的那张表中
```shell
CREATE TABLE emp(
empno int,
ename string,
job string,
mgr int,
hiredate string,
sal double,
comm double,
deptno int)
row format delimited fields terminated by '\t';
```
##### (3) 向Hive中间表中load数据
```shell
hive> load data local inpath '/opt/TestFolder/emp.txt' into table emp;
```
##### (4) 通过insert命令将中间表中的数据导入到Hive关联HBase的那张表中
```shell
hive> insert into table hive_hbase_emp_table1 select * from emp;
```
##### (5) 查看Hive以及关联的HBase表中是否已经成功的同步插入了数据
Hive：
```shell
hive> select * from hive_hbase_emp_table1;
```
![hive_hbase_emp_table1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFkOWU1N2M3NjcxODhmNTkucG5n?x-oss-process=image/format,png)

HBase：
```shell
hbase> scan 'hbase_emp_table1'
```
![hbase_emp_table1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJmYjk4MWUwMTk4N2I3NWEucG5n?x-oss-process=image/format,png)

#### 2.案例二
目标：在HBase中已经存储了某一张表hbase_emp_table，然后在Hive中创建一个外部表来关联HBase中的hbase_emp_table这张表，使之可以借助Hive来分析HBase这张表中的数据。
注：该案例2紧跟案例1的脚步，所以完成此案例前，请先完成案例1。
分步实现：
##### (1) 在Hive中创建外部表
```shell
CREATE EXTERNAL TABLE relevance_hbase_emp(
empno int,
ename string,
job string,
mgr int,
hiredate string,
sal double,
comm double,
deptno int)
STORED BY 
'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = 
":key,info:ename,info:job,info:mgr,info:hiredate,info:sal,info:comm,info:deptno") 
TBLPROPERTIES ("hbase.table.name" = "hbase_emp_table1");
```
##### (2) 关联后就可以使用Hive函数进行一些分析操作了
```shell
hive (default)> select * from relevance_hbase_emp;
```
![外部表](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRhZWRjNGIzZGVkOGMxMjEucG5n?x-oss-process=image/format,png)

