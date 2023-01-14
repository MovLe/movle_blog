---
title: 'HBase基本操作'
date: 2019-04-08 13:00:00
tags: HBase
categories: HBase
---

#### 1. 基本操作
##### (1).进入HBase客户端命令行
```shell
bin/hbase shell
```
##### (2).查看帮助命令
```shell
hbase(main)> help
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ5YTI1MDUzMTY0ODNiODUucG5n?x-oss-process=image/format,png)

##### (3). 查看当前数据库中有哪些表
```shell
hbase(main)> list
```
##### (4)查看当前数据库中有哪些命名空间
```shell
hbase(main)> list_namespace
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUzMjk3OGU2ZjgzM2JmMWQucG5n?x-oss-process=image/format,png)

(5)列出指定命名空间下的所有表
```shell
hbase> list_namespace_tables 'ns1'
```
#### 2.命名空间的操作
##### (1)列出所有命名空间
```shell
hbase> list_namespace
```
##### (2)新建命名空间
```shell
hbase> create_namespace 'ns1'
```
##### (3)删除命名空间
```shell
hbase> drop_namespace 'ns1'
```
该命名空间必须为空，否则会报错。

##### (4)修改命名空间
```shell
hbase> alter_namespace 'ns', {METHOD => 'set', 'PROPERTY_NAME' => 'PROPERTY_VALUE'}
```
#### 3. 表的操作

##### (1).创建表
###### (a)格式：
```shell
create '表名','列簇名'

create '命名空间:表名','列簇名'
```
###### (b)举例：
```shell
hbase(main)> create 'student','info'
hbase(main)> create 'iparkmerchant_order','smzf'
hbase(main)> create 'staff','info'
hbase(main)> create 'ns_ct:calllog'
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUzZTdmYmExYzYxZDg5MmEucG5n?x-oss-process=image/format,png)

##### (2).插入数据到表
###### (a)格式：
```shell
put '表名','rowkey','列簇:列','属性'
```
###### (b)举例：

```shell
hbase(main) > put 'student','1001','info:name','Thomas'
hbase(main) > put 'student','1001','info:sex','male'
hbase(main) > put 'student','1001','info:age','18'
hbase(main) > put 'student','1002','info:name','Janna'
hbase(main) > put 'student','1002','info:sex','female'
hbase(main) > put 'student','1002','info:age','20'
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdmYTIyNWYyNjJlYTdkMzEucG5n?x-oss-process=image/format,png)

数据插入后的数据模型

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTllOTA1MDE0ZTFjMDZlNzAucG5n?x-oss-process=image/format,png)

##### (3).扫描查看表数据
```shell
hbase(main) > scan 'student'
hbase(main) > scan 'student',{STARTROW => '1001', STOPROW  => '1001'}
hbase(main) > scan 'student',{STARTROW => '1001'}
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA4MWIyZWMyOTllZTZlZGQucG5n?x-oss-process=image/format,png)

注：这个是从哪一个rowkey开始扫描
##### (4).查看表结构
```shell
hbase(main)> desc 'student'
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI0NjFiNzA1MWU2NWFmNjQucG5n?x-oss-process=image/format,png)

##### (5).更新指定字段的数据
```shell
hbase(main) > put 'student','1001','info:name','Nick'
hbase(main) > put 'student','1001','info:age','100'
hbase(main) > put 'student','1001','info:isNull',''          //仅测试空值问题
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgwNDJkYTEyOTNhYzBmMGIucG5n?x-oss-process=image/format,png)

##### (6).查看“指定行”或“指定列族:列”的数据
```shell
hbase(main) > get 'student','1001'
hbase(main) > get 'student','1001','info:name'
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE4YTQyZjM2YzY4MTNiNjcucG5n?x-oss-process=image/format,png)

##### (7).删除数据
删除某rowkey的全部数据：
```shell
hbase(main) > deleteall 'student','1001'
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWViNTA3OGUwNzQ5YjlmNGUucG5n?x-oss-process=image/format,png)

##### (8).清空表数据
```shell
hbase(main) > truncate 'student'
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThmNTVhMTRlMmIwMzcwZTIucG5n?x-oss-process=image/format,png)

提示：清空表的操作顺序为先disable，然后再truncate。

(9). 删除表

首先需要先让该表为disable状态：
```shell
hbase(main) > disable 'student'
```
检查这个表是否被禁用
```shell
hbase(main) > is_enabled 'student'
hbase(main) > is_disabled 'student'
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU5YmJmNGIzMzhkOWNjZjIucG5n?x-oss-process=image/format,png)

恢复被禁用得表
```shell
hbase(main) > enable 'student'
```
disable后才能drop这个表：
```shell
hbase(main) > drop 'student'
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJhOWRiMWJjOWI0NjYwMDcucG5n?x-oss-process=image/format,png)

提示：如果直接drop表，会报错：Drop the named table. Table must first be disabled
ERROR: Table student is enabled. Disable it first.

##### (10).统计表数据行数

```shell
hbase(main) > count 'staff'
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBhMWM2ZDE3ZTU3ZDZiOTkucG5n?x-oss-process=image/format,png)
##### (11). 变更表信息

将info列族中的数据存放3个版本：
```shell
hbase(main) > alter 'student',{NAME=>'info',VERSIONS=>3}
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFiZWJlMTQ5NmVhMjk3YmIucG5n?x-oss-process=image/format,png)

查看student的最新的版本的数据
```shell
hbase(main) > get 'student','1001'
```
查看HBase中的多版本
```shell
hbase(main) > get 'student','1001',{COLUMN=>'info:name',VERSIONS=>10}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY3ZDk2MTYyYTM2NjViOGUucG5n?x-oss-process=image/format,png)

#### 3. 常用Shell操作

##### (1).status 例如：显示服务器状态

```shell
hbase> status 'bigdata111'
```
##### (2).exists 检查表是否存在，适用于表量特别多的情况
```shell
hbase> exists 'hbase_book'
```
##### (3).is_enabled/is_disabled 检查表是否启用或禁用
```shell
hbase> is_enabled 'hbase_book'
hbase> is_disabled 'hbase_book'
```
##### (4).alter 该命令可以改变表和列族的模式，例如：
为当前表增加列族：
```shell
hbase> alter 'hbase_book', NAME => 'CF2', VERSIONS => 2
```
为当前表删除列族：
```shell
hbase> alter 'hbase_book', 'delete' => 'CF2'
```
##### (5).disable禁用一张表
```shell
hbase> disable 'hbase_book'
hbase> drop 'hbase_book'
```
##### (6).delete

删除一行中一个单元格的值，例如：
```shell
hbase> delete 'hbase_book', 'rowKey', 'CF:C'
```
##### (7).truncate清空表数据，即禁用表-删除表-创建表
```shell
hbase> truncate 'hbase_book'
```
##### (8).create创建多个列族：
```shell
hbase> create 't1', {NAME => 'f1'}, {NAME => 'f2'}, {NAME => 'f3'}
```
