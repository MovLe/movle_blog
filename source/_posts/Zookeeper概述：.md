---
title: 'Zookeeper概述'
date: 2019-01-03 10:50:00
tags: ZooKeeper
categories: ZooKeeper
---

### 一.概述：
&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper是一个开源的分布式的，为分布式应用提供协调服务的Apache项目。Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。

### 二.特点：
(1)Zookeeper：一个领导者（leader），多个跟随者（follower）组成的集群。

(2)Leader负责进行投票的发起和决议，更新系统状态。

(3)Follower用于接收客户请求并向客户端返回结果，在选举Leader过程中参与投票。

(4)集群中奇数台服务器只要有半数以上节点存活，Zookeeper集群就能正常服务。

(5)全局数据一致：每个server保存一份相同的数据副本，client无论连接到哪个server，数据都是一致的。

(6)更新请求顺序进行，来自同一个client的更新请求按其发送顺序依次执行。

(7)数据更新原子性，一次数据更新要么成功，要么失败。

(8)实时性，在一定时间范围内，client能读到最新数据。

### 三.数据结构：
&nbsp;&nbsp;&nbsp;&nbsp;ZooKeeper数据模型的结构与Unix文件系统很类似，整体上可以看作是一棵树，每个节点称做一个ZNode。每一个ZNode默认能够存储1MB的元数据，每个ZNode都可以通过其路径唯一标识

![ZooKeeper数据结构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTljNDQ2YjdmYzg1ODNjNmEucG5n?x-oss-process=image/format,png)

### 四.应用场景：

#### 1.统一命名服务

![统一命名服务](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWViYTBjOTc1OTFmY2UyYjcucG5n?x-oss-process=image/format,png)

#### 2.统一配置管理
##### (1).分布式环境下，配置文件管理和同步是一个常见问题
(a)一个集群中，所有节点的配置信息是一致的，比如hadoop集群

(b)对配置文件修改后，希望能够快速同步到各个节点上

##### (2).配置管理可交由ZK实现
(a)可配置信息写入ZK上的一个Znode

(b)各个节点监听这个ZNode

(c )一旦Znode中的数据被修改，ZK将通知各个节点

![配置管理](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdmZDRmMTUzZTQwNmE5MTgucG5n?x-oss-process=image/format,png)

#### 3.统一集群管理
集群管理结构图如下所示。
##### (1).分布式环境中，实时掌握每个节点的状态是必要的。
(a)可根据节点实时做出一些调整

##### (2).可交由Zk实现
(a)可将节点信息写入ZK上的一个ZNode

(b)监听这个Znode可获取它的实时状态变化

##### (3).典型应用
(a)HBase中Master状态监控与选举

![集群管理](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA3ZDY3N2Q0M2FkZWQwYTEucG5n?x-oss-process=image/format,png)

#### 4.服务器节点动态上下线

![服务器动态上下线](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA5NTJlNDZmOWVkMDdhNGYucG5n?x-oss-process=image/format,png)

#### 5.软负载均衡

![软负载均衡](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJkZTI0NWVhODEwMTAxMzgucG5n?x-oss-process=image/format,png)