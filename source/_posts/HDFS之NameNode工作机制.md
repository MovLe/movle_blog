---
title: 'HDFS之NameNode工作机制'
date: 2019-01-01 15:49:00
tags: Hadoop
categories: Hadoop
---
### 一.NameNode&Secondary NameNode工作机制
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdlNmM2NTBjNDZjYTIyMjIucG5n?x-oss-process=image/format,png)

#### 1.第一阶段：namenode启动
(a)第一次启动namenode格式化后，创建fsimage和edits文件。如果不是第一次启动，直接加载编辑日志(edits)和镜像文件(fsimage)到内存

(b)客户端对元数据进行增删改的请求

(c)namenode记录操作日志，更新滚动日志

(d)namenode在内存中对数据进行增删改查

#### 2.第二阶段：Secondary NameNode工作

(a)Secondary NameNode询问namenode是否需要checkpoint。直接带回namenode是否检查结果。

(b)Secondary NameNode请求执行checkpoint。

(c)namenode滚动正在写的edits日志

(d)将滚动前的编辑日志和镜像文件拷贝到Secondary NameNode

(e)Secondary NameNode加载编辑日志和镜像文件到内存，并合并。

(f)生成新的镜像文件fsimage.chkpoint

(g)拷贝fsimage.chkpoint到namenode

(h)namenode将fsimage.chkpoint重新命名成fsimage

#### 3.chkpoint检查时间参数设置
(1)通常情况下，SecondaryNameNode每隔一小时执行一次。

[hdfs-default.xml]
```xml
<property>
  <name>dfs.namenode.checkpoint.period</name>
  <value>3600</value>
</property>
```
(2)一分钟检查一次操作次数，当操作次数达到1百万时，SecondaryNameNode执行一次。
```xml
<property>
  <name>dfs.namenode.checkpoint.txns</name>
  <value>1000000</value>
  <description>操作动作次数</description>
</property>

<property>
  <name>dfs.namenode.checkpoint.check.period</name>
  <value>60</value>
  <description> 1分钟检查一次操作次数</description>
</property>
```

### 二.镜像文件和编辑日志文件

#### 1.概念
&nbsp;&nbsp;&nbsp;&nbsp;namenode被格式化之后，将在/opt/module/hadoop-2.8.4/data/dfs/name/current目录中产生如下文件,注只能在NameNode所在的节点才能找到此文件
&nbsp;&nbsp;&nbsp;&nbsp;可以执行find . -name edits* 来查找文件

```shell
edits_0000000000000000000
fsimage_0000000000000000000.md5
seen_txid
VERSION
```
(1)Fsimage文件：HDFS文件系统元数据的一个永久性的检查点，其中包含HDFS文件系统的所有目录和文件idnode的序列化信息。 

(2)Edits文件：存放HDFS文件系统的所有更新操作的路径，文件系统客户端执行的所有写操作首先会被记录到edits文件中。 

(3)seen_txid文件保存的是一个数字，就是最后一个edits_的数字

(4)每次Namenode启动的时候都会将fsimage文件读入内存，并从00001开始到seen_txid中记录的数字依次执行每个edits里面的更新操作，保证内存中的元数据信息是最新的、同步的，可以看成Namenode启动的时候就将fsimage和edits文件进行了合并。


#### 2.oiv查看fsimage文件
(1)查看oiv和oev命令
```shell
hdfs
```
![hdfs](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlmM2IxN2VjMTJlYzA2MDMucG5n?x-oss-process=image/format,png)

(2)基本语法
```shell
hdfs oiv -p 文件类型 -i镜像文件 -o 转换后文件输出路径
```
(3)案例实操
```shell
pwd
/opt/module/hadoop-2.8.4/data/dfs/name/current

hdfs oiv -p XML -i fsimage_0000000000000000316 -o /opt/fsimage.xml

cat /opt/module/hadoop-2.8.4/fsimage.xml
```
将显示的xml文件内容拷贝到IDEA中创建的xml文件中，并格式化。

#### 3.oev查看edits文件
(1)基本语法
```shell
hdfs oev -p 文件类型 -i编辑日志 -o 转换后文件输出路径

-p	–processor <arg>   指定转换类型: binary (二进制格式), xml (默认，XML格式),stats
-i	–inputFile <arg>     输入edits文件，如果是xml后缀，表示XML格式，其他表示二进制
-o 	–outputFile <arg> 输出文件，如果存在，则会覆盖
```

### 三.滚动编辑日志
&nbsp;&nbsp;&nbsp;&nbsp;正常情况HDFS文件系统有更新操作时，就会滚动编辑日志。也可以用命令强制滚动编辑日志。
(1)滚动编辑日志（前提必须启动集群）
```shell
hdfs dfsadmin -rollEdits
```
举例：原文件名edits_inprogress_0000000000000000010
执行以下命令后
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU3ZGM0OTZhNzgxMzRkMzIucG5n?x-oss-process=image/format,png)


(2)镜像文件什么时候产生
Namenode启动时加载镜像文件和编辑日志

### 四.namenode版本号
#### 1.查看namenode版本号
在/opt/module/hadoop-2.8.4/data/dfs/name/current这个目录下查看VERSION
```shell
namespaceID=1778616660
clusterID=CID-bc165781-d10a-46b2-9b6f-3beb1d988fe0
cTime=1552918200296
storageType=NAME_NODE
blockpoolID=BP-274621862-192.168.1.111-1552918200296
layoutVersion=-63
```
#### 2.namenode版本号具体解释

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY5OTNjYTEyNGI5Yjg0MjgucG5n?x-oss-process=image/format,png)


```shell
(1)namespaceID在HDFS上，会有多个Namenode，所以不同Namenode的namespaceID是不同的，分别管理一组blockpoolID。

(2)clusterID集群id，全局唯一

(3)cTime属性标记了namenode存储系统的创建时间，对于刚刚格式化的存储系统，这个属性为0；但是在文件系统升级之后，该值会更新到新的时间戳。

(4)storageType属性说明该存储目录包含的是namenode的数据结构。

(5)blockpoolID：一个block pool id标识一个block pool，并且是跨集群的全局唯一。当一个新的Namespace被创建的时候(format过程的一部分)会创建并持久化一个唯一ID。在创建过程构建全局唯一的BlockPoolID比人为的配置更可靠一些。NN将BlockPoolID持久化到磁盘中，在后续的启动过程中，会再次load并使用。

(6)layoutVersion是一个负整数。通常只有HDFS增加新特性时才会更新这个版本号。

(7)storageID (存储ID)：是DataNode的ID,不唯一
```

### 五.SecondaryNameNode目录结构
&nbsp;&nbsp;&nbsp;&nbsp;Secondary NameNode用来监控HDFS状态的辅助后台程序，每隔一段时间获取HDFS元数据的快照。
&nbsp;&nbsp;&nbsp;&nbsp;在/opt/module/hadoop-2.8.4/data/dfs/namesecondary/current这个目录中查看SecondaryNameNode目录结构

```shell
edits_0000000000000000001-0000000000000000002
fsimage_0000000000000000002
fsimage_0000000000000000002.md5
VERSION
```
&nbsp;&nbsp;&nbsp;&nbsp;SecondaryNameNode的namesecondary/current目录和主namenode的current目录的布局相同。
&nbsp;&nbsp;&nbsp;&nbsp;好处：在主namenode发生故障时（假设没有及时备份数据），可以从SecondaryNameNode恢复数据。

```shell
方法一：将SecondaryNameNode中数据拷贝到namenode存储数据的目录；
方法二：使用-importCheckpoint选项启动namenode守护进程，从而将SecondaryNameNode中数据拷贝到namenode目录中。
```

### 六.集群安全模式操作
#### 1.概述
&nbsp;&nbsp;&nbsp;&nbsp;Namenode启动时，首先将映像文件（fsimage）载入内存，并执行编辑日志（edits）中的各项操作。一旦在内存中成功建立文件系统元数据的映像，则创建一个新的fsimage文件和一个空的编辑日志。此时，namenode开始监听datanode请求。但是此刻，namenode运行在安全模式，即namenode的文件系统对于客户端来说是只读的。
&nbsp;&nbsp;&nbsp;&nbsp;系统中的数据块的位置并不是由namenode维护的，而是以块列表的形式存储在datanode中。在系统的正常操作期间，namenode会在内存中保留所有块位置的映射信息。在安全模式下，各个datanode会向namenode发送最新的块列表信息，namenode了解到足够多的块位置信息之后，即可高效运行文件系统。
&nbsp;&nbsp;&nbsp;&nbsp;如果满足“最小副本条件”，namenode会在30秒钟之后就退出安全模式。所谓的最小副本条件指的是在整个文件系统中99.9%的块满足最小副本级别（默认值：dfs.replication.min=1）。在启动一个刚刚格式化的HDFS集群时，因为系统中还没有任何块，所以namenode不会进入安全模式。
#### 2.基本语法
集群处于安全模式，不能执行重要操作（写操作）。集群启动完成后，自动退出安全模式。

```shell
bin/hdfs dfsadmin -safemode get		（功能描述：查看安全模式状态）
bin/hdfs dfsadmin -safemode enter  	（功能描述：进入安全模式状态）
bin/hdfs dfsadmin -safemode leave	（功能描述：离开安全模式状态）
bin/hdfs dfsadmin -safemode wait	（功能描述：等待安全模式状态）
```

### 七.Namenode多目录配置
#### 1.namenode的本地目录可以配置成多个，且每个目录存放内容相同，增加了可靠性。

#### 2.具体配置如下：
hdfs-site.xml
```xml
<property>
<name>dfs.namenode.name.dir</name>
<value>file:///${hadoop.tmp.dir}/dfs/name1,file:///${hadoop.tmp.dir}/dfs/name2</value>
</property>
```
(1)步骤：
```shell
a.停止集群 删除data 和 logs  rm -rf data/* logs/*

b.hdfs namenode -format

c.start-dfs.sh

d.去展示
```



(2)**具体解释：**

**格式化做了哪些事情？**

在NameNode节点上，有两个最重要的路径，分别被用来存储元数据信息和操作日志，而这两个路径来自于配置文件，它们对应的属性分别是dfs.name.dir和dfs.name.edits.dir，同时，它们默认的路径均是/tmp/hadoop/dfs/name。格式化时，NameNode会清空两个目录下的所有文件，之后，格式化会在目录dfs.name.dir下创建文件

**hadoop.tmp.dir** 这个配置，会让dfs.name.dir和dfs.name.edits.dir会让两个目录的文件生成在一个目录里

(3)**思考2**：非NN上如果生成了name1和name2，那么他和NN上生成得有没有差别？

答：有区别、NN节点上会产生新得edits_XXX，非NN不会fsimage会更新，而非NN不会，只会产生一个仅初始化得到得fsimage，不会生成edits,更不会发生日志滚动。