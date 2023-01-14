---
title: '[HBase]-HBase读写数据流程'
date: 2019-04-08 14:00:00
tags: HBase
categories: HBase
---


### 一、读写流程
#### 1.HBase读数据流程

![读流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVmZDI2NDI3NTNiNDg0MjcucG5n?x-oss-process=image/format,png)

(1).HRegionServer保存着.META.的这样一张表以及表数据，要访问表数据，首先Client先去访问zookeeper，从zookeeper里面找到.META.表所在的位置信息，即找到这个.META.表在哪个HRegionServer上保存着。

(2) 接着Client通过刚才获取到的HRegionServer的IP来访问.META.表所在的HRegionServer，从而读取到.META.，进而获取到.META.表中存放的元数据。

(3)Client通过元数据中存储的信息，访问对应的HRegionServer，然后扫描(scan)所在HRegionServer的Memstore和Storefile来查询数据。

(4) 最后HRegionServer把查询到的数据响应给Client。

#### 2.HBase写数据流程
![写流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk3ZTBjMTA3NDVhYjk1NTQucG5n?x-oss-process=image/format,png)


(1) Client也是先访问zookeeper，找到-ROOT-表，进而找到.META.表，并获取.META.表信息。

(2) 确定当前将要写入的数据所对应的RegionServer服务器和Region。

(3) Client向该RegionServer服务器发起写入数据请求，然后RegionServer收到请求并响应。

(4) Client先把数据写入到HLog，以防止数据丢失。

(5) 然后将数据写入到Memstore。

(6) 如果Hlog和Memstore均写入成功，则这条数据写入成功。在此过程中，如果Memstore达到阈值，会把Memstore中的数据flush到StoreFile中。

(7) 当Storefile越来越多，会触发Compact合并操作，把过多的Storefile合并成一个大的Storefile。当Storefile越来越大，Region也会越来越大，达到阈值后，会触发Split操作，将Region一分为二。

提示：因为内存空间是有限的，所以说溢写过程必定伴随着大量的小文件产生。

### 二. 退役(decommissioning)
&nbsp;&nbsp;&nbsp;&nbsp;顾名思义，就是从当前HBase集群中删除某个RegionServer，这个过程分为如下几个过程：
&nbsp;&nbsp;&nbsp;&nbsp;在0.90.2之前，我们只能通过在要卸载的节点上执行
#### 1.第一种方案
##### (1)停止负载平衡器
```shell
hbase> balance_switch false
```
##### (2) 在退役节点上停止RegionServer
```shell
hbase-daemon.sh stop regionserver
```
##### (3) RegionServer一旦停止，会关闭维护的所有region

##### (4) Zookeeper上的该RegionServer节点消失

##### (5) Master节点检测到该RegionServer下线，开启平衡器
```shell
hbase> balance_switch true
```
##### (6) 下线的RegionServer的region服务得到重新分配

&nbsp;&nbsp;&nbsp;&nbsp;这种方法很大的一个缺点是该节点上的Region会离线很长时间。因为假如该RegionServer上有大量Region的话，因为Region的关闭是顺序执行的，第一个关闭的Region得等到和最后一个Region关闭并Assigned后一起上线。这是一个相当漫长的时间。每个Region Assigned需要4s，也就是说光Assigned就至少需要2个小时。该关闭方法比较传统，需要花费一定的时间，而且会造成部分region短暂的不可用。

#### 2.另一种方案：
##### (1)新方法
&nbsp;&nbsp;&nbsp;&nbsp;自0.90.2之后，HBase添加了一个新的方法，即“graceful_stop”,只需要在HBase Master节点执行
```shell
$ bin/graceful_stop.sh <RegionServer-hostname>
```
&nbsp;&nbsp;&nbsp;&nbsp;该命令会自动关闭Load Balancer，然后Assigned Region，之后会将该节点关闭。除此之外，你还可以查看remove的过程，已经assigned了多少个Region，还剩多少个Region，每个Region 的Assigned耗时

##### (2)开启负载平衡器
```shell
hbase> balance_switch false
```

### 三.Hbase集群出问题如何重置(学习的时候)
#### 1.将zookeeper中的hbase结点删除
#### 2.删除hdfs上的hbase目录
#### 3.重新启动hbase
