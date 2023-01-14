---
title: '[Hive]-Hive DML数据操作'
date: 2019-07-15 18:00:00
tags: Hive
categories: Hive
---

### 一. 数据导入
#### 1.向表中装载数据（Load）
##### (1).语法
```shell
hive>load data [local] inpath '/opt/module/datas/student.txt' [overwrite] into table student [partition (partcol1=val1,…)];
```

(a)load data:表示加载数据
(b)local:表示从本地加载数据到hive表；否则从HDFS加载数据到hive表
(c )inpath:表示加载数据的路径
(d)overwrite:表示覆盖表中已有数据，否则表示追加
(e)into table:表示加载到哪张表
(f)student:表示具体的表名
(g)partition:表示上传到指定分区

##### (2).实操案例
###### (a)创建一张表
```shell
hive (default)> create table student (id string, name string) row format delimited fields terminated by '\t';
```
###### (b)加载本地文件到hive
```shell
hive (default)> load data local inpath '/opt/module/datas/student.txt' into table default.student;
```
###### (c)加载HDFS文件到hive中

上传文件到HDFS
```shell
hive (default)> dfs -put /opt/module/datas/student.txt /user/movle/hive;
```

加载HDFS上数据

```shell
hive (default)>load data inpath '/user/movle/hive/student.txt' into table default.student;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM1ZTVjODkyOTIyNGVhMmMucG5n?x-oss-process=image/format,png)

###### (d)加载数据覆盖表中已有的数据
上传文件到HDFS
```shell
hive (default)> dfs -put /opt/module/datas/student.txt /user/movle/hive/;
```
加载数据覆盖表中已有的数据
```shell
hive (default)>load data inpath '/user/movle/hive/student.txt' overwrite into table default.student;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ4ZjkxM2U4ZDJkMWIzODIucG5n?x-oss-process=image/format,png)

注：load hdfs的数据相当于mv文件到另一个目录中，原目录文件消失
#### 2. 通过查询语句向表中插入数据(Insert)
##### (1)创建一张分区表
```shell
hive (default)> create table student6(id int, name string) partitioned by (month string) row format delimited fields terminated by '\t';
```
##### (2)基本插入数据
```shell
hive (default)> insert into table  student6 partition(month='201909') values(1,'wangwu');
```
##### (3)基本模式插入(根据单张表查询结果)
```shell
hive (default)> insert overwrite table student6 partition(month='201908')
             select id, name from student6 where month='201909';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRiMTY0MGZjMjU0Mzk1NDYucG5n?x-oss-process=image/format,png)

##### (4)多插入模式(根据多张表查询结果)
```shell
hive (default)> from student6
              insert overwrite table student6 partition(month='201907')
              select id, name where month='201909'
              insert overwrite table student6 partition(month='201906')
              select id, name where month='201909';
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY3YTY5ODA2NjU2ZDBjMTYucG5n?x-oss-process=image/format,png)

##### 3.查询语句中创建表并加载数据(As Select)
详见5章创建表。
根据查询结果创建表（查询的结果会添加到新创建的表中）
```shell
create table if not exists student7
as select id, name from student;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRjZDNhMDAwZDYxYzFlZDQucG5n?x-oss-process=image/format,png)

#### 4.创建表时通过Location指定加载数据路径
##### (1).创建表，并指定在hdfs上的位置
```shell
hive (default)> create table if not exists student5(
              id int, name string
              )
              row format delimited fields terminated by '\t'
              location '/user/hive/warehouse/student5';
```
##### (2)上传数据到hdfs上
```shell
hive (default)> dfs -put /opt/module/datas/student.txt  /user/hive/warehouse/student5;
```
##### (3)查询数据
```shell
hive (default)> select * from student5;
```
#### 5.Import数据到指定Hive表中
注意：先用export导出后，再将数据导入。同在HDFS上是Copy级操作
先导出：
```shell
hive (default)> export table default.student6 to '/user/hive/warehouse/export/student';
```

再导入：
```shell
hive (default)> import table student2 partition(month='201909') from '/user/hive/warehouse/export/student';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY5NmQxZDliZWVkYWQ5NzAucG5n?x-oss-process=image/format,png)
### 二. 数据导出
#### 1. Insert导出
##### (1).将查询的结果导出到本地,数据之间无间隔
```shell
hive (default)> insert overwrite local directory '/opt/module/datas/export/student'
            select * from student;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkzYmQ5ZTMwNTNiZGQyMWUucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU1ZTk5M2M2ZWRjYTRlMzEucG5n?x-oss-process=image/format,png)

##### (2)将查询的结果格式化导出到本地,数据之间"\t"间隔
```shell
hive (default)> insert overwrite local directory '/opt/module/datas/export/student1'
             ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'             
             select * from student;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY3ZTY3NzA0YTlmNjM3NDQucG5n?x-oss-process=image/format,png)

##### (3)将查询的结果导出到HDFS上(没有local)
```shell
hive (default)> insert overwrite directory '/user/movle/student2'
             ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
             select * from student;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZlY2VhZmNmNWU2YjllOGYucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdjMTMwMTBiNjg5MzEzMjgucG5n?x-oss-process=image/format,png)

注：虽然同是HDFS，但不是copy操作

#### 2.Hadoop命令导出到本地
```shell
hive (default)> dfs -get /user/hive/warehouse/student6/month=201909/000000_0  /opt/module/datas/export/student3.txt;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJjMWQyNWYxNTYxNGYzODYucG5n?x-oss-process=image/format,png)

#### 3.Hive Shell 命令导出
基本语法：(hive -f/-e 执行语句或者脚本 > file)

```shell
bin/hive -e 'select * from default.student;' > /opt/module/datas/export/student4.txt;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMwYTI0Nzk0Yzc1YjdjOTAucG5n?x-oss-process=image/format,png)

#### 4. Export导出到HDFS上
```shell
hive (default)> export table default.student to '/user/hive/warehouse/export/student2';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc2MTAwOWU4YmVmYzg5NDkucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdmODY5NzFkZDgxNzI2MjAucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU4NzAyOWFkYzUxZmM4M2YucG5n?x-oss-process=image/format,png)


#### 5.Sqoop导出

### 三.清除表中数据(Truncate)
注意：Truncate只能删除管理表，不能删除外部表中数据
```shell
hive (default)> truncate table student;
```