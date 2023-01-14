---
title: '[Hive]-Hive参数配置方式'
date: 2019-07-15 15:00:00
tags: Hive
categories: Hive
---

#### 1.查看当前所有的配置信息
```shell
set;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcxYjk1MzhhMzViZjU4NWQucG5n?x-oss-process=image/format,png)

#### 2.参数的配置三种方式
##### (1)配置文件方式

* 默认配置文件：hive-default.xml 
* 用户自定义配置文件：hive-site.xml

&nbsp;&nbsp;&nbsp;&nbsp;注意：用户自定义配置会覆盖默认配置。另外，Hive也会读入Hadoop的配置，因为Hive是作为Hadoop的客户端启动的，Hive的配置会覆盖Hadoop的配置。配置文件的设定对本机启动的所有Hive进程都有效。

##### (2)命令行参数方式
启动Hive时，可以在命令行添加-hiveconf param=value来设定参数。
例如：
```shell
bin/hive -hiveconf mapred.reduce.tasks=10;
```
注意：仅对本次hive启动有效
查看参数设置：
```shell
hive (default)> set mapred.reduce.tasks;
```
显示为：mapred.reduce.tasks=10
默认mapred.reduce.tasks=-1

##### (3)参数声明方式
可以在HQL中使用SET关键字设定参数
例如：
```shell
hive (default)> set mapred.reduce.tasks=10;
```
注意：仅对本次hive启动有效。
查看参数设置
```shell
hive (default)> set mapred.reduce.tasks;
```
上述三种设定方式的优先级依次递增。即配置文件<命令行参数<参数声明。注意某些系统级的参数，例如log4j相关的设定，必须用前两种方式设定，因为那些参数的读取在会话建立以前已经完成了。