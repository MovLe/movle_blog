---
title: 'Zookeeper之内部原理'
date: 2019-01-03 13:50:00
tags: ZooKeeper
categories: ZooKeeper
---

### 一.选举机制
**Server ID： myid(权重越大)**
**Zxid：数据ID(先一数据低进行选择)**

#### 1.半数机制（Paxos 协议）：
&nbsp;&nbsp;&nbsp;&nbsp;集群中半数以上机器存活，集群可用。所以zookeeper适合装在奇数台机器上。

#### 2.Zookeeper虽然在配置文件中并没有指定master和slave。
&nbsp;&nbsp;&nbsp;&nbsp;但是，zookeeper工作时，是有一个节点为leader，其他则为follower，Leader是通过内部的选举机制临时产生的。

#### 3.以一个简单的例子来说明整个选举的过程。
&nbsp;&nbsp;&nbsp;&nbsp;假设有五台服务器组成的zookeeper集群，它们的id从1-5，同时它们都是最新启动的，也就是没有历史数据，在存放数据量这一点上，都是一样的。假设这些服务器依序启动，来看看会发生什么。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZlOTAwMzA5YTYzZmMzZWQucG5n?x-oss-process=image/format,png)



* (1)服务器1启动，此时只有它一台服务器启动了，它发出去的报没有任何响应，所以它的选举状态一直是LOOKING状态。
* (2)服务器2启动，它与最开始启动的服务器1进行通信，互相交换自己的选举结果，由于两者都没有历史数据，所以id值较大的服务器2胜出，但是由于没有达到超过半数以上的服务器都同意选举它(这个例子中的半数以上是3)，所以服务器1、2还是继续保持LOOKING状态。
* (3)服务器3启动，根据前面的理论分析，服务器3成为服务器1、2、3中的老大，而与上面不同的是，此时有三台服务器选举了它，所以它成为了这次选举的leader。
* (4)服务器4启动，根据前面的分析，理论上服务器4应该是服务器1、2、3、4中最大的，但是由于前面已经有半数以上的服务器选举了服务器3，所以它只能接收当小弟的命了。
* (5)服务器5启动，同4一样当小弟。

### 二.节点类型
#### 1.Znode有两种类型：
* 短暂(ephemeral)：客户端和服务器端断开连接后，创建的节点自己删除
* 持久(persistent)：客户端和服务器端断开连接后，创建的节点不删除

#### 2.Znode有四种形式的目录节点(默认是persistent)

(1)持久化目录节点(PERSISTENT)(小写：persistent)

&nbsp;&nbsp;&nbsp;&nbsp;客户端与zookeeper断开连接后，该节点依旧存在。

(2)持久化顺序编号目录节点(PERSISTENT_SEQUENTIAL)(小写：persistent_sequential)

&nbsp;&nbsp;&nbsp;&nbsp;客户端与zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号。

(3)临时目录节点(EPHEMERAL)(ephemeral)

&nbsp;&nbsp;&nbsp;&nbsp;客户端与zookeeper断开连接后，该节点被删除。

(4)临时顺序编号目录节点(EPHEMERAL_SEQUENTIAL)(ephemeral_sequential)

&nbsp;&nbsp;&nbsp;&nbsp;客户端与zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBhOWI4MjMzZTI3OGZiNzMucG5n?x-oss-process=image/format,png)

#### 3.创建znode时设置顺序标识，znode名称后会附加一个值，顺序号是一个单调递增的计数器，由父节点维护

#### 4.在分布式系统中，顺序号可以被用于为所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序

### 三.stat结构体
#### 1.czxid- 引起这个znode创建的zxid，创建节点的事务的zxid

&nbsp;&nbsp;&nbsp;&nbsp;每次修改ZooKeeper状态都会收到一个zxid形式的时间戳，也就是ZooKeeper事务ID。
&nbsp;&nbsp;&nbsp;&nbsp;事务ID是ZooKeeper中所有修改总的次序。每个修改都有唯一的zxid，如果zxid1小于zxid2，那么zxid1在zxid2之前发生。

#### 2.ctime - znode被创建的毫秒数(从1970年开始)
#### 3.mzxid - znode最后更新的zxid
#### 4.mtime - znode最后修改的毫秒数(从1970年开始)
#### 5.pZxid-znode最后更新的子节点zxid
#### 6.cversion - znode子节点变化号，znode子节点修改次数
#### 7.dataversion - znode数据变化号
#### 8.aclVersion - znode访问控制列表的变化号
#### 9.ephemeralOwner- 如果是临时节点，这个是znode拥有者的session id。如果不是临时节点则是0。
#### 10.dataLength- znode的数据长度
#### 11.numChildren - znode子节点数量

### 四. 监听器原理
#### 1.监听原理详解：
(1) 首先要有一个main()线程

(2) 在main线程中创建ZK客户端，这是会创建两个线程，一个负责网络连接通信(connect),一个负责监听(listener)

(3) 通过connect线程将注册的监听事件发送给ZK

(4) 在ZK的注册监听器列表中将注册的监听事件添加到列表中

(5) ZK监听到有数据或路径发生变化时，就会将这个消息发送给listener线程

(6) Listener线程内部调用process()方法

#### 2.常见的监听
1.监听节点数据的变化
```shell
Get path [watch]
```
2.监听子节点增减的变化

```shell
Ls path [watch]
```

![监听器原理](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQzZWVjZTExYWYyNzIyY2IucG5n?x-oss-process=image/format,png)

### 五. 写数据流程

![写数据流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ1NDJjNmU2YzlmZTU4ZDAucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;读是局部性的，即client只需要从与它相连的server上读取数据即可；而client有写请求的话，与之相连的server会通知leader，然后leader会把写操作分发给所有server。所以定要比读慢很多。