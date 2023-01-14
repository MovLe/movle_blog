---
title: 'HDFS之DateNode工作机制'
date: 2019-01-01 16:49:00
tags: Hadoop
categories: Hadoop
---
### 一.NameNode & DataNode工作机制
![Datanode工作机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI5YjZhZTllYzExZWFlNzEucG5n?x-oss-process=image/format,png)

1.一个数据块在datanode上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳。

2.DataNode启动后向namenode注册，通过后，周期性（1小时）的向namenode上报所有的块信息。

3.心跳是每3秒一次，心跳返回结果带有namenode给该datanode的命令如复制块数据到另一台机器，或删除某个数据块。如果超过10分钟没有收到某个datanode的心跳，则认为该节点不可用。

4.集群运行中可以安全加入和退出一些机器

### 二.数据完整性
1.当DataNode读取block的时候，它会计算checksum校验和

2.如果计算后的checksum，与block创建时值不一样，说明block已经损坏。

3.client读取其他DataNode上的block.

4.datanode在其文件创建后周期验证checksum校验和

### 三. 掉线时限参数设置
&nbsp;&nbsp;&nbsp;&nbsp;datanode进程死亡或者网络故障造成datanode无法与namenode通信，namenode不会立即把该节点判定为死亡，要经过一段时间，这段时间暂称作超时时长。HDFS默认的超时时长为10分钟+30秒。如果定义超时时间为timeout，则超时时长的计算公式为：
```math
timeout  = 2 * dfs.namenode.heartbeat.recheck-interval + 10 * dfs.heartbeat.interval。
```
&nbsp;&nbsp;&nbsp;&nbsp;而默认的dfs.namenode.heartbeat.recheck-interval 大小为5分钟，dfs.heartbeat.interval默认为3秒。
&nbsp;&nbsp;&nbsp;&nbsp;需要注意的是hdfs-site.xml 配置文件中的heartbeat.recheck.interval的单位为毫秒，dfs.heartbeat.interval的单位为秒。
```xml
<property>
    <name>dfs.namenode.heartbeat.recheck-interval</name>
    <value>300000</value>
</property>
<property>
    <name> dfs.heartbeat.interval </name>
    <value>3</value>
</property>
```

### 四.DataNode的目录结构
&nbsp;&nbsp;&nbsp;&nbsp;和namenode不同的是，datanode的存储目录是初始阶段自动创建的，不需要额外格式化。
#### 1.在/opt/module/hadoop-2.8.4/data/dfs/data/current这个目录下查看版本号
```shell
cat VERSION 

storageID=DS-1b998a1d-71a3-43d5-82dc-c0ff3294921b
clusterID=CID-1f2bf8d1-5ad2-4202-af1c-6713ab381175
cTime=0
datanodeUuid=970b2daf-63b8-4e17-a514-d81741392165
storageType=DATA_NODE
layoutVersion=-56
```
#### 2.具体解释

```shell
storageID：存储id号
clusterID集群id，全局唯一
cTime属性标记了datanode存储系统的创建时间，对于刚刚格式化的存储系统，这个属性为0；但是在文件系统升级之后，该值会更新到新的时间戳。
datanodeUuid：datanode的唯一识别码
storageType：存储类型
layoutVersion是一个负整数。通常只有HDFS增加新特性时才会更新这个版本号。
```
#### 3.在/opt/module/hadoop-2.8.4/data/dfs/data/current/BP-97847618-192.168.10.102-1493726072779/current这个目录下查看该数据块的版本号
```shell
cat VERSION 

#Mon May 08 16:30:19 CST 2017
namespaceID=1933630176
cTime=0
blockpoolID=BP-97847618-192.168.10.102-1493726072779
layoutVersion=-56
```
#### 4.具体解释
```shell
namespaceID：是datanode首次访问namenode的时候从namenode处获取的storageID对每个datanode来说是唯一的（但对于单个datanode中所有存储目录来说则是相同的），namenode可用这个属性来区分不同datanode。

cTime属性标记了datanode存储系统的创建时间，对于刚刚格式化的存储系统，这个属性为0；但是在文件系统升级之后，该值会更新到新的时间戳。

blockpoolID：一个block pool id标识一个block pool，并且是跨集群的全局唯一。当一个新的Namespace被创建的时候(format过程的一部分)会创建并持久化一个唯一ID。在创建过程构建全局唯一的BlockPoolID比人为的配置更可靠一些。NN将BlockPoolID持久化到磁盘中，在后续的启动过程中，会再次load并使用。

layoutVersion是一个负整数。通常只有HDFS增加新特性时才会更新这个版本号。
```

### 五.Datanode多目录配置
#### 1.datanode也可以配置成多个目录，每个目录存储的数据不一样。即：数据不是副本。
#### 2.具体配置如下：
hdfs-site.xml

```xml
<property>
<name>dfs.datanode.data.dir</name>  
<value>file:///${hadoop.tmp.dir}/dfs/data1,file:///${hadoop.tmp.dir}/dfs/data2</value>
</property>
```