---
title: 'Hive DDL数据定义'
date: 2019-07-15 17:00:00
tags: Hive
categories: Hive
---


### 一.创建数据库
#### 1.创建一个数据库，数据库在HDFS上的默认存储路径是/user/hive/warehouse/*.db。

```shell
hive (default)> create database db_hive;
```
**可能出现的报错：**
```shell
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:For direct MetaStore DB connections, we don't support retries at the client level.)
```
**解决办法**：[https://blog.csdn.net/mo_ing/article/details/81219533](https://blog.csdn.net/mo_ing/article/details/81219533)

个人解决版本：使用mysql-connector-java-5.1.27.jar，而不是mysql-connector-java-5.1.18.jar

#### 2.避免要创建的数据库已经存在错误，增加if not exists判断。（标准写法）
```shell
hive> create database db_hive;
```
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. Database db_hive already exists

标准写法：
```shell
hive (default)> create database if not exists db_hive;
```

#### 3.创建一个数据库，指定数据库在HDFS上存放的位置
```shell
hive (default)> create database db_hive2 location '/db_hive2.db';
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBiNGUzNTY2NjU2MTA3MWMucG5n?x-oss-process=image/format,png)

### 二.修改数据库

&nbsp;&nbsp;&nbsp;&nbsp;用户可以使用ALTER DATABASE命令为某个数据库的DBPROPERTIES设置键-值对属性值，来描述这个数据库的属性信息。数据库的其他元数据信息都是不可更改的，包括数据库名和数据库所在的目录位置。

```shell
hive (default)> alter database db_hive set dbproperties('createtime'='20190506');
```
在mysql中查看修改结果
```shell
hive> desc database extended db_hive;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgxY2E4NDU2MjczM2YyYjcucG5n?x-oss-process=image/format,png)


### 三.查询数据库

#### 1.显示数据库

##### (1)显示数据库
```shell
hive> show databases;
```
##### (2)过滤显示查询的数据库
```shell
hive> show databases like 'db_hive*';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ4YTEzMzkxZDQ5OThhZTEucG5n?x-oss-process=image/format,png)


#### 2. 查看数据库详情

##### (1)显示数据库信息
```shell
hive> desc database db_hive;
```

##### (2)显示数据库详细信息，extended
```shell
hive> desc database extended db_hive;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM1NDI4N2ExN2E3MjE3MGUucG5n?x-oss-process=image/format,png)


#### 3.切换当前数据库
```shell
hive (default)> use db_hive;
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMxOWI2ZDkzMTQ1NTdiMGYucG5n?x-oss-process=image/format,png)

### 四.删除数据库

#### 1.删除空数据库

```shell
hive>drop database db_hive2;
```
#### 2.如果删除的数据库不存在，最好采用 if exists判断数据库是否存在
```shell
hive> drop database db_hive2;
```
正确做法：
```shell
hive> drop database if exists db_hive2;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBlMmVmMTgyMGQ4NTFjOTkucG5n?x-oss-process=image/format,png)

#### 3.如果数据库不为空，可以采用cascade命令，强制删除
```shell
hive> drop database db_hive;
```
正确做法：

```shell
hive> drop database db_hive cascade;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThlYTEwOTk5OTJmMjNiMzkucG5n?x-oss-process=image/format,png)

### 五.创建表

#### 1.建表语法
```shell
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name 
```

```shell
[(col_name data_type [COMMENT col_comment], ...)] 

[COMMENT table_comment] 

[PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] 

[CLUSTERED BY (col_name, col_name, ...) 

[SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] 

[ROW FORMAT row_format] 

[STORED AS file_format] 

[LOCATION hdfs_path]
```

#### 2..字段解释说明：
##### (1)CREATE TABLE 创建一个指定名字的表。
如果相同名字的表已经存在，则抛出异常；用户可以用 IF NOT EXISTS 选项来忽略这个异常。

##### (2)EXTERNAL关键字可以让用户创建一个外部表
在建表的同时指定一个指向实际数据的路径(LOCATION)，Hive创建内部表时，会将数据移动到数据仓库指向的路径；若创建外部表，仅记录数据所在的路径，不对数据的位置做任何改变。在删除表的时候，内部表的元数据和数据会被一起删除，而外部表只删除元数据，不删除数据。

##### (3)COMMENT：为表和列添加注释。

##### (4)PARTITIONED BY   创建分区表

##### (5)CLUSTERED BY  创建分桶表

##### (6)SORTED BY    不常用

##### (7)ROW FORMAT 

DELIMITED [FIELDS TERMINATED BY char] [COLLECTION ITEMS TERMINATED BY char] [MAP KEYS TERMINATED BY char] [LINES TERMINATED BY char] | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)]

&nbsp;&nbsp;&nbsp;&nbsp;用户在建表的时候可以自定义SerDe或者使用自带的SerDe。如果没有指定ROW FORMAT 或者ROW FORMAT DELIMITED，将会使用自带的SerDe。在建表的时候，用户还需要为表指定列，用户在指定表的列的同时也会指定自定义的SerDe，Hive通过SerDe确定表的具体的列的数据。

##### (8)STORED AS指定存储文件类型

&nbsp;&nbsp;&nbsp;&nbsp;常用的存储文件类型：SEQUENCEFILE(二进制序列文件)、TEXTFILE(文本)、RCFILE(列式存储格式文件)
&nbsp;&nbsp;&nbsp;&nbsp;如果文件数据是纯文本，可以使用STORED AS TEXTFILE。如果数据需要压缩，使用 STORED AS SEQUENCEFILE。

##### (9)LOCATION ：指定表在HDFS上的存储位置。

##### (10)LIKE允许用户复制现有的表结构，但是不复制数据。

### 六.管理表
#### 1.理论
&nbsp;&nbsp;&nbsp;&nbsp;默认创建的表都是所谓的管理表，有时也被称为内部表。因为这种表，Hive会（或多或少地）控制着数据的生命周期。Hive默认情况下会将这些表的数据存储在由配置项hive.metastore.warehouse.dir(例如，/user/hive/warehouse)所定义的目录的子目录下。   当我们删除一个管理表时，Hive也会删除这个表中数据。管理表不适合和其他工具共享数据。

#### 2.案例实操

##### (1)普通创建表
```shell
create table if not exists student2(

id int, name string

)

row format delimited fields terminated by '\t'

stored as textfile

location '/user/hive/warehouse/student2';
```
##### (2)根据查询结果创建表（查询的结果会添加到新创建的表中）

```shell
create table if not exists student3
as select id from student;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI0NTA4Mzc0MWMwMzU3OTQucG5n?x-oss-process=image/format,png)


##### (3)根据已经存在的表结构创建表
```shell
create table if not exists student4 like student;
```

##### (4)查询表的类型
```shell
hive (default)> desc formatted student4;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ5ZDQ2NjQ3MGIxZWZlNTkucG5n?x-oss-process=image/format,png)


### 七. 外部表

#### 1.理论

&nbsp;&nbsp;&nbsp;&nbsp;因为表是外部表，所以Hive并非认为其完全拥有这份数据。删除该表并不会删除掉这份数据，不过描述表的元数据信息会被删除掉。

#### 2.管理表和外部表的使用场景：

&nbsp;&nbsp;&nbsp;&nbsp;每天将收集到的网站日志定期流入HDFS文本文件。在外部表（原始日志表）的基础上做大量的统计分析，用到的中间表、结果表使用内部表存储，数据通过SELECT+INSERT进入内部表。

#### 3.案例实操

分别创建部门和员工外部表，并向表中导入数据。

##### (1)原始数据

##### (2)建表语句

创建部门表
```shell
create external table if not exists default.dept(
deptno int,
dname string,
loc int
)
row format delimited fields terminated by '\t';
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU5YWY5OTI0MjIwZGViZmYucG5n?x-oss-process=image/format,png)
创建员工表

```shell
create external table if not exists default.emp(
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
##### (3)查看创建的表

```shell
hive (default)> show tables;
```

![查看创建的表](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNiMTI0NzZjZWEzYWZmNWMucG5n?x-oss-process=image/format,png)

##### (4)向外部表中导入数据
导入数据
```shell
hive (default)> load data local inpath '/opt/module/datas/dept.txt' into table default.dept;

hive (default)> load data local inpath '/opt/module/datas/emp.txt' into table default.emp;
```
查询结果
```shell
hive (default)> select * from emp;

hive (default)> select * from dept;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRkZjliMWFhOTQ1YjQ1NDMucG5n?x-oss-process=image/format,png)

##### (5)查看表格式化数据

```shell
hive (default)> desc formatted dept;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgzNGYzNDM4NzYxZmFiN2EucG5n?x-oss-process=image/format,png)

### 八.分区表

&nbsp;&nbsp;&nbsp;&nbsp;分区表实际上就是对应一个HDFS文件系统上的独立的文件夹，该文件夹下是该分区所有的数据文件。Hive中的分区就是分目录，把一个大的数据集根据业务需要分割成小的数据集。在查询时通过WHERE子句中的表达式选择查询所需要的指定的分区，这样的查询效率会提高很多。
#### 1.分区表基本操作

##### (1)引入分区表（需要根据日期对日志进行管理）
```shell
/user/hive/warehouse/log_partition/20190702/20190702.log

/user/hive/warehouse/log_partition/20190703/20190703.log

/user/hive/warehouse/log_partition/20190704/20190704.log
```
##### (2)创建分区表语法
```shell
hive (default)> create table dept_partition(
               deptno int, dname string, loc string
               )
               partitioned by (month string)
               row format delimited fields terminated by '\t';
```
##### (3)加载数据到分区表中
```shell
hive (default)> load data local inpath '/opt/module/datas/dept.txt' into table default.dept_partition partition(month='201909');

hive (default)> load data local inpath '/opt/module/datas/dept.txt' into table default.dept_partition partition(month='201908');

hive (default)> load data local inpath '/opt/module/datas/dept.txt' into table default.dept_partition partition(month='201907');
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIwNWRjM2RkZDg0ODk3NWIucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJjNGFmM2RiZThhYTgxMzcucG5n?x-oss-process=image/format,png)

##### (4)查询分区表中数据
单分区查询
```shell
hive (default)> select * from dept_partition where month='201909';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBiZTg5MGUyM2U2NWQxMzUucG5n?x-oss-process=image/format,png)

多分区联合查询
```shell
hive (default)> select * from dept_partition where month='201909'
              union
              select * from dept_partition where month='201908'
              union
              select * from dept_partition where month='201907';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBjODAxYmRkYTMyZDlkYjgucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI5MWRmZGY0ZjJhNWYyM2MucG5n?x-oss-process=image/format,png)

##### (5)增加分区

创建单个分区

```shell
hive (default)> alter table dept_partition add partition(month='201906') ;
```
同时创建多个分区
```shell
hive (default)>  alter table dept_partition add partition(month='201905') partition(month='201904');
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQzNDQ2M2NiNGRlM2EyZWYucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU1MTZkNDRjMzI0YTVhZjMucG5n?x-oss-process=image/format,png)


注：增加多个分区之间用空格" "隔开，删除多个分区用","隔开

##### (6)删除分区

删除单个分区
```shell
hive (default)> alter table dept_partition drop partition (month='201904');
```
同时删除多个分区
```shell
hive (default)> alter table dept_partition drop partition (month='201905'), partition (month='201906');
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIyNjI0ZTc2MDg3MTBlMDMucG5n?x-oss-process=image/format,png)

##### (7)查看分区表有多少分区
```shell
hive> show partitions dept_partition;
```
##### (8)查看分区表结构
```shell
hive> desc formatted dept_partition;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQxZmZmZWRhNjFkMWRjN2EucG5n?x-oss-process=image/format,png)


#### 2.分区表注意事项

##### (1).创建二级分区表
```shell
hive (default)> create table dept_partition2(
               deptno int, dname string, loc string
               )
               partitioned by (month string, day string)
               row format delimited fields terminated by '\t';
```
##### (2).正常的加载数据

###### (a)加载数据到二级分区表中
```shell
hive (default)> load data local inpath '/opt/module/datas/dept.txt' into table default.dept_partition2 partition(month='201909', day='13');
```
###### (b)查询分区数据
```shell
hive (default)> select * from dept_partition2 where month='201909' and day='13';
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFiYzAwMmY0MmE1ZmJlZGEucG5n?x-oss-process=image/format,png)

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI5MTdlZjI3NTY3NzM5OGMucG5n?x-oss-process=image/format,png)

#### 3.把数据直接上传到分区目录上，让分区表和数据产生关联的两种方式

##### (1)方式一：上传数据后修复
上传数据
```shell
hive (default)> dfs -mkdir -p /user/hive/warehouse/dept_partition2/month=201909/day=12;

hive (default)> dfs -put /opt/module/datas/dept.txt /user/hive/warehouse/dept_partition2/month=201909/day=12;
```

查询数据（查询不到刚上传的数据）
```shell
hive (default)> select * from dept_partition2 where month='201909' and day='12';
```
执行修复命令
```shell
hive>msck repair table dept_partition2;
```
再次查询数据
```shell
hive (default)> select * from dept_partition2 where month='201909' and day='12';
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMwNzIzNGJhZmYzMGMxZDIucG5n?x-oss-process=image/format,png)

##### (2)方式二：上传数据后添加分区
上传数据
```shell
hive (default)> dfs -mkdir -p /user/hive/warehouse/dept_partition2/month=201909/day=11;

hive (default)> dfs -put /opt/module/datas/dept.txt /user/hive/warehouse/dept_partition2/month=201909/day=11;
```
执行添加分区
```shell
hive (default)> alter table dept_partition2 add partition(month='201909', day='11');
```
查询数据
```shell
hive (default)> select * from dept_partition2 where month='201909' and day='11';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUyNDhjNWVhOTY1ZGRlNmEucG5n?x-oss-process=image/format,png)

##### (3)方式三：上传数据后load数据到分区

创建目录
```shell
hive (default)> dfs -mkdir -p /user/hive/warehouse/dept_partition2/month=201909/day=10;
```
上传数据
```shell
hive (default)> load data local inpath '/opt/module/datas/dept.txt' into table dept_partition2 partition(month='201909',day='10');
```
查询数据
```shell
hive (default)> select * from dept_partition2 where month='201909' and day='10';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc3MjE3MjlmMzY3NzQyNGQucG5n?x-oss-process=image/format,png)

### 九.修改表

#### 1.重命名表

##### (1).语法
```shell
ALTER TABLE table_name RENAME TO new_table_name
```
##### (2).实操案例
```shell
hive (default)> alter table dept_partition2 rename to dept_partition4;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU4MmQ2MGJjZmFjZGE3N2YucG5n?x-oss-process=image/format,png)

#### 2. 增加和删除表分区

详见1.6.1分区表基本操作。同上

#### 3. 增加/修改/替换列信息

##### (1).语法

更新列
```shell
ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name column_type [COMMENT col_comment] [FIRST|AFTER column_name]
```

增加和替换列
```shell
ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type [COMMENT col_comment], ...) 
```
注：ADD是代表新增一字段，字段位置在所有列后面(partition列前)，REPLACE则是表示替换表中所有字段。

##### (2).实操案例
###### (a)查询表结构
```shell
hive> desc dept_partition;
```
###### (b)添加列
```shell
hive (default)> alter table dept_partition add columns(deptdesc string);
```
###### (c )查询表结构
```shell
hive>desc dept_partition;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjMzZhZWI2NmE4YTUyNjMucG5n?x-oss-process=image/format,png)

###### (d)更新列
```shell
hive (default)> alter table dept_partition change column deptdesc desc int;
```
###### (e)查询表结构
```shell
hive>desc dept_partition;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThiODRiNjc1ODEwN2U5OTQucG5n?x-oss-process=image/format,png)

###### (f)替换列
```shell
hive (default)> alter table dept_partition replace columns(deptno string, dname string, loc string);
```
###### (g)查询表结构
```shell
hive>desc dept_partition;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBhMDEzYzJmMWQzNDgwYTUucG5n?x-oss-process=image/format,png)

### 十.删除表
```shell
hive (default)> drop table dept_partition;
```