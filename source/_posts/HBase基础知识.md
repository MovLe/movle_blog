---
title: 'HBase基础知识'
date: 2019-04-08 11:00:00
tags: HBase
categories: HBase
---


### 一.HBase简介
&nbsp;&nbsp;&nbsp;&nbsp;HBase是一个分布式的、面向列的开源数据库，它是一个适合于非结构化数据存储的数据库。另一个不同的是HBase基于列的而不是基于行的模式。

大：上亿行、百万列

面向列：面向列（簇）的存储和权限控制，列（簇）独立检索

稀疏：对于为空(null)的列，并不占用存储空间，因此，表的设计的非常的稀疏

### 二. HBase的角色
#### 1. HMaster

##### (1).功能：
(a) 监控RegionServer
(b) 处理RegionServer故障转移
(c ) 处理元数据的变更
(d) 处理region的分配或移除
(e) 在空闲时间进行数据的负载均衡
(f) 通过Zookeeper发布自己的位置给客户端

#### 2.HRegionServer
##### (1).功能：
(a) 负责存储HBase的实际数据
(b) 处理分配给它的Region
(c ) 刷新缓存到HDFS
(d) 维护HLog
(e) 执行压缩
(f) 负责处理Region分片

##### (2).组件：
###### (a). Write-Ahead logs
&nbsp;&nbsp;&nbsp;&nbsp;HBase的修改记录，当对HBase读写数据的时候，数据不是直接写进磁盘，它会在内存中保留一段时间（时间以及数据量阈值,可以设定）。但把数据保存在内存中可能有更高的概率引起数据丢失，为了解决这个问题，数据会先写在一个叫做Write-Ahead logfile的文件中，然后再写入内存中。所以在系统出现故障的时候，数据可以通过这个日志文件重建。

###### (b). HFile
&nbsp;&nbsp;&nbsp;&nbsp;这是在磁盘上保存原始数据的实际的物理文件，是实际的存储文件。

###### (c ). Store
&nbsp;&nbsp;&nbsp;&nbsp;HFile存储在Store中，一个Store对应HBase表中的一个列簇。

###### (d). MemStore
&nbsp;&nbsp;&nbsp;&nbsp;顾名思义，就是内存存储，位于内存中，用来保存当前的数据操作，所以当数据保存在WAL中之后，RegsionServer会在内存中存储键值对。

###### (e). Region
&nbsp;&nbsp;&nbsp;&nbsp;Hbase表的分片，HBase表会根据RowKey值被切分成不同的region存储在RegionServer中，在一个RegionServer中可以有多个不同的region。

### 三.HBase的架构
&nbsp;&nbsp;&nbsp;&nbsp;一个RegionServer可以包含多个HRegion，每个RegionServer维护一个HLog，和多个HFiles以及其对应的MemStore。RegionServer运行于DataNode上，数量可以与DatNode数量一致，请参考如下架构图：

![HBase架构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA0OGI4N2M1NzBlNTM2YTAucG5n?x-oss-process=image/format,png)
![Hbase架构2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE0YmExNDhmMjdlYTZmNjYucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU5YWUyZDE0ODRjMmI0ODkucG5n?x-oss-process=image/format,png)
### 四. HBase数据模型

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ1MDliYmVkNDUyM2M2NjQucG5n?x-oss-process=image/format,png)
				
确定一个单元格的位置（cell），需要如下四个
rowkey + Colume Family + Colume + timestamp(版本version)，数据有版本的概念，即一个单元格可能有多个值，但是只有最新得一个对外显示。

* HBase中有两张特殊的Table，-ROOT-和.META.
* .META.：记录了用户表的Region信息，**.META.可以有多个region**
* -ROOT-：记录了.META.表的Region信息，**-ROOT-只有一个region**
* Zookeeper中记录了-ROOT-表的location
* Client访问用户数据之前需要首先访问zookeeper，然后访问-ROOT-表，接着访问.META.表，最后才能找到用户数据的位置去访问，中间需要多次网络操作，不过client端会做cache缓存，注意：**在0.96版本后，Hbase移除了-ROOT-表**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI2OTUyZTNkMDJiYTYyMTIucG5n?x-oss-process=image/format,png)

**Row Key**: 行键，Table的主键，Table中的记录默认按照Row Key升序排序

**Timestamp**:时间戳，每次数据操作对应的时间戳，可以看作是数据的version number

**Column Family**：列簇，Table在水平方向有一个或者多个Column Family组成，一个Column Family中可以由**任意多个Column**组成，即Column Family支持动态扩展，无需预先定义Column的数量以及类型，所有Column均以二进制格式存储，用户需要自行进行类型转换。

**Table & Region:** 当Table随着记录数不断增加而变大后，会逐渐分裂成多份splits，成为regions，一个region由[startkey,endkey)表示，不同的region会被Master分配给相应的RegionServer进行管理：.

### 五.HMaster

&nbsp;&nbsp;&nbsp;&nbsp;HMaster没有单点问题，HBase中可以启动多个HMaster，通过Zookeeper的Master Election机制保证总有一个Master运行，HMaster在功能上主要负责Table和Region的管理工作：

(1). 管理用户对Table的增、删、改、查操作

(2). 管理HRegionServer的负载均衡，调整Region分布

(3). 在Region Split后，负责新Region的分配

(4). 在HRegionServer停机后，负责失效HRegionServer 上的Regions迁移

### 六. HRegionServer

&nbsp;&nbsp;&nbsp;&nbsp;HRegionServer内部管理了一系列HRegion对象，每个HRegion对应了Table中的一个Region，HRegion中由多个HStore组成。**每个HStore对应了Table中的一个Column Family**的存储，可以看出每个Column Family其实就是一个集中的存储单元，因此最好将具备共同IO特性的column放在一个Column Family中，这样最高效。

#### 1. MemStore & StoreFiles

&nbsp;&nbsp;&nbsp;&nbsp;HStore存储是HBase存储的核心了，其中由两部分组成，一部分是**MemStore**，一部分是**StoreFiles**。MemStore是Sorted Memory Buffer，用户写入的数据**首先**会放入MemStore，当MemStore满了以后会Flush成一个StoreFile（底层实现是HFile），当StoreFile文件数量增长到一定阈值，会触发Compact合并操作，将多个StoreFiles合并成一个StoreFile，合并过程中会进行版本合并和数据删除，因此可以看出HBase其实只有增加数据，所有的更新和删除操作都是在后续的compact过程中进行的，这使得用户的写操作只要进入内存中就可以立即返回，保证了HBase I/O的高性能。当StoreFiles Compact后，会逐步形成越来越大的StoreFile，当单个StoreFile大小超过一定阈值后，会触发Split操作，同时把当前Region Split成2个Region，父Region会下线，新Split出的2个孩子Region会被HMaster分配到相应的HRegionServer上，使得原先1个Region的压力得以分流到2个Region上。

#### 2. HLog

&nbsp;&nbsp;&nbsp;&nbsp;每个HRegionServer中都有一个HLog对象，HLog是一个实现Write Ahead Log的类，在每次用户操作写入MemStore的同时，也会写一份数据到HLog文件中，HLog文件定期会滚动出新的，并删除旧的文件（已持久化到StoreFile中的数据）。当HRegionServer意外终止后，HMaster会通过Zookeeper感知到，HMaster首先会处理遗留的 HLog文件，将其中不同Region的Log数据进行拆分，分别放到相应region的目录下，然后再将失效的region重新分配，领取 到这些region的HRegionServer在Load Region的过程中，会发现有历史HLog需要处理，因此会Replay HLog中的数据到MemStore中，然后flush到StoreFiles，完成数据恢复。

#### 3.文件类型

&nbsp;&nbsp;&nbsp;&nbsp;HBase中的所有数据文件都存储在Hadoop HDFS文件系统上，主要包括上述提出的两种文件类型：

##### (1).HFile 
&nbsp;&nbsp;&nbsp;&nbsp;HBase中KeyValue数据的存储格式，HFile是Hadoop的二进制格式文件，实际上StoreFile就是对HFile做了轻量级包装，即StoreFile底层就是HFile

##### (2).HLog File
&nbsp;&nbsp;&nbsp;&nbsp;HBase中WAL（Write Ahead Log） 的存储格式，物理上是Hadoop的Sequence File

#### 4. Zookeeper中hbase的节点的存储信息：

* rs：regionserver节点信息

* table-lock：hbase的除meta以外的所有表

* Table：hbase的所有的表



