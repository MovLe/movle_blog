---
title: 'HDFS的其他功能'
date: 2019-01-01 17:49:00
tags: Hadoop
categories: Hadoop
---

### 一.集群间数据拷贝
#### 1.scp实现两个远程主机之间的文件复制
```shell
scp -r hello.txt [root@bigdata111:/user/itstar/hello.txt](mailto:root@bigdata111:/user/itstar/hello.txt)          // 推 push
scp -r [root@bigdata112:/user/itstar/hello.txt  hello.txt](mailto:root@hadoop103:/user/atguigu/hello.txt%20%20hello.txt)        // 拉 pull
scp -r root@bigdata112:/opt/module/hadoop-2.8.4/LICENSE.txt root@bigdata113:/opt/module/hadoop-2.8.4/LICENSE.txt   //是通过本地主机中转实现两个远程主机的文件复制；如果在两个远程主机之间ssh没有配置的情况下可以使用该方式。
```
#### 2.采用discp命令实现两个hadoop集群之间的递归数据复制(注:不用设置其他，直接写IP)
```shell
bin/hadoop distcp hdfs://192.168.1.51:9000/LICENSE.txt hdfs://192.168.1.111:9000/HAHA
```
### 二.Hadoop存档
#### 1.理论概述
&nbsp;&nbsp;&nbsp;&nbsp;每个文件均按块存储，每个块的元数据存储在namenode的内存中，因此hadoop存储小文件会非常低效。因为大量的小文件会耗尽namenode中的大部分内存。但注意，存储小文件所需要的磁盘容量和存储这些文件原始内容所需要的磁盘空间相比也不会增多。例如，一个1MB的文件以大小为128MB的块存储，使用的是1MB的磁盘空间，而不是128MB。
&nbsp;&nbsp;&nbsp;&nbsp;Hadoop存档文件或HAR文件，是一个更高效的文件存档工具，它将文件存入HDFS块，在减少namenode内存使用的同时，允许对文件进行透明的访问。具体说来，Hadoop存档文件可以用作MapReduce的输入。

### 三.快照管理
快照相当于对目录做一个备份。并不会立即复制所有文件，而是指向同一个文件。当写入发生时，才会产生新文件。
#### 1.基本语法
```shell
hdfs dfsadmin -allowSnapshot 路径   （功能描述：开启指定目录的快照功能）
hdfs dfsadmin -disallowSnapshot 路径 （功能描述：禁用指定目录的快照功能，默认是禁用）
hdfs dfs -createSnapshot 路径        （功能描述：对目录创建快照）
hdfs dfs -createSnapshot 路径 名称   （功能描述：指定名称创建快照）
hdfs dfs -renameSnapshot 路径 旧名称 新名称 （功能描述：重命名快照）
hdfs lsSnapshottableDir         （功能描述：列出当前用户所有已快照目录）
hdfs snapshotDiff 路径1 路径2 （功能描述：比较两个快照目录的不同之处）
hdfs dfs -deleteSnapshot <path> <snapshotName>  （功能描述：删除快照）
```
### 四. 回收站
#### 1.默认回收站
* 默认值fs.trash.interval=0，0表示禁用回收站，可以设置删除文件的存活时间。
* 默认值fs.trash.checkpoint.interval=0，检查回收站的间隔时间。
* 要求fs.trash.checkpoint.interval<=fs.trash.interval。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY4NDExNGM2Yzg5MGE0NGEucG5n?x-oss-process=image/format,png)

#### 2.启用回收站
修改core-site.xml，配置垃圾回收时间为1分钟。
```xml
<property>
    <name>fs.trash.interval</name>
    <value>1</value>
</property>
```
#### 3.查看回收站
回收站在集群中的；路径：/user/itstar/.Trash/….

#### 4.修改访问垃圾回收站用户名称
进入垃圾回收站用户名称，默认是dr.who，修改为itstar用户
[core-site.xml]
```xml
<property>
  <name>hadoop.http.staticuser.user</name>
  <value>itstar</value>
</property>
```
#### 5.通过程序删除的文件不会经过回收站，需要调用moveToTrash()才进入回收站
&nbsp;&nbsp;&nbsp;&nbsp;Trash trash = New Trash(conf);
&nbsp;&nbsp;&nbsp;&nbsp;trash.moveToTrash(path);
#### 6.恢复回收站数据
```shell
hadoop fs -mv /user/itstar/.Trash/Current/user/itstar/input    /user/itstar/input
```
#### 7.清空回收站
```shell
hdfs dfs -expunge
```