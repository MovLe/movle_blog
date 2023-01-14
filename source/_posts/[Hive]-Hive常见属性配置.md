---
title: '[Hive]-Hive常见属性配置'
date: 2019-07-15 14:00:00
tags: Hive
categories: Hive
---

#### 1.Hive数据仓库位置配置

##### (1)Default数据仓库的最原始位置是在hdfs上的：/user/hive/warehouse路径下

##### (2)在仓库目录下，没有对默认的数据库default创建文件夹。如果某张表属于default数据库，直接在数据仓库目录下创建一个文件夹。

##### (3)修改default数据仓库原始位置(将hive-default.xml.template如下配置信息拷贝到hive-site.xml文件中)

```xml
<property>
<name>hive.metastore.warehouse.dir</name>
<value>/user/hive/warehouse</value>
<description>location of default database for the warehouse</description>
</property>
```
配置同组用户有执行权限
```shell
bin/hdfs dfs -chmod g+w /user/hive/warehouse
```
#### 2.查询后信息显示配置
##### (1)在hive-site.xml文件中添加如下配置信息，就可以实现显示当前数据库，以及查询表的头信息配置。
```xml
<property>
	<name>hive.cli.print.header</name>
	<value>true</value>
</property>

<property>
	<name>hive.cli.print.current.db</name>
	<value>true</value>
</property>
```
##### (2)重新启动hive，对比配置前后差异
(a)配置前
(b)配置后

#### 3 Hive运行日志信息配置
##### (1)Hive的log默认存放在/tmp/itstar/hive.log目录下(当前用户名下)
##### (2)修改hive的log存放日志到/opt/module/hive/logs
###### (a)修改/opt/module/hive/conf/hive-log4j.properties.template文件名称为hive-log4j.properties
```shell
pwd

mv hive-log4j.properties.template hive-log4j.properties
```
###### (b)在hive-log4j.properties文件中修改log存放位置
```shell
hive.log.dir=/opt/module/hive/logs
```
