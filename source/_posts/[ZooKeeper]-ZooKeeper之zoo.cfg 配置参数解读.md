---
title: '[ZooKeeper]-ZooKeeper之zoo.cfg配置参数解读'
date: 2019-01-03 13:50:00
tags: ZooKeeper
categories: ZooKeeper
---

#### 1.解读zoo.cfg文件中参数含义
![参数解读](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlhZWRlNjA4NTQ0MDc0ODIucG5n?x-oss-process=image/format,png)

##### (1)tickTime=2000：通信心跳数，Zookeeper服务器心跳时间，单位毫秒
&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper使用的基本时间，服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳，时间单位为毫秒。
它用于心跳机制，并且设置最小的session超时时间为两倍心跳时间。(session的最小超时时间是2*tickTime)

##### (2)initLimit=10：Leader和Follower初始通信时限
&nbsp;&nbsp;&nbsp;&nbsp;集群中的follower跟随者服务器与leader领导者服务器之间初始连接时能容忍的最多心跳数（tickTime的数量），用它来限定集群中的Zookeeper服务器连接到Leader的时限。
投票选举新leader的初始化时间
&nbsp;&nbsp;&nbsp;&nbsp;Follower在启动过程中，会从Leader同步所有最新数据，然后确定自己能够对外服务的起始状态。
&nbsp;&nbsp;&nbsp;&nbsp;Leader允许Follower在initLimit时间内完成这个工作。

##### (3)syncLimit=5：Leader和Follower同步通信时限

&nbsp;&nbsp;&nbsp;&nbsp;集群中Leader与Follower之间的最大响应时间单位，假如响应超过syncLimit * tickTime，Leader认为Follwer死掉，从服务器列表中删除Follwer。
&nbsp;&nbsp;&nbsp;&nbsp;在运行过程中，Leader负责与ZK集群中所有机器进行通信，例如通过一些心跳检测机制，来检测机器的存活状态。
&nbsp;&nbsp;&nbsp;&nbsp;如果L发出心跳包在syncLimit之后，还没有从F那收到响应，那么就认为这个F已经不在线了。

##### (4)dataDir：数据文件目录+数据持久化路径
&nbsp;&nbsp;&nbsp;&nbsp;保存内存数据库快照信息的位置，如果没有其他说明，更新的事务日志也保存到数据库。

##### (5)clientPort=2181：客户端连接端口
&nbsp;&nbsp;&nbsp;&nbsp;监听客户端连接的端口
