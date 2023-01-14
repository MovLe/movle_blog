---
title: 'Yarn-HA配置'
date: 2019-01-02 13:50:00
tags: 
- Hadoop
- YARN
- 安装部署
categories: Hadoop
---
### 一.Yarn-HA配置
####  1.YARN-HA工作机制
##### (1).官方文档：
[官方文档](http://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html)


##### (2).YARN-HA工作机制

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRkMWVjZjhkOWRiOTkyMTAucG5n?x-oss-process=image/format,png)

#### 2.配置YARN-HA集群
##### (0).环境准备
(a)修改IP
(b)修改主机名及主机名和IP地址的映射
(c )关闭防火墙
(d)ssh免密登录
(e)安装JDK，配置环境变量等
(f)配置Zookeeper集群
(g)配置过mapred-site.xml(和以前配置一样)

##### (1).规划集群

|bigdata111|bigdata112|bigdata113|
|:-:|:-:|:-:|
|NameNode|NameNode||
|JournalNode|JournalNode|JournalNode|
|DataNode|DataNode|DataNode|
|ZK|ZK|ZK|
|ResourceManager|ResourceManager||
|NodeManager|NodeManager|NodeManager|

##### (2).具体配置
###### (a)yarn-site.xml
```xml
<configuration>

    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>

    <!--启用resourcemanager ha-->
    <property>
        <name>yarn.resourcemanager.ha.enabled</name>
        <value>true</value>
    </property>
 
    <!--声明两台resourcemanager的地址-->
    <property>
        <name>yarn.resourcemanager.cluster-id</name>
        <value>cluster-yarn1</value>
    </property>

    <property>
        <name>yarn.resourcemanager.ha.rm-ids</name>
        <value>rm1,rm2</value>
    </property>

    <property>
        <name>yarn.resourcemanager.hostname.rm1</name>
        <value>hadoop1</value>
    </property>

    <property>
        <name>yarn.resourcemanager.hostname.rm2</name>
        <value>hadoop2</value>
    </property>
 
    <!--指定zookeeper集群的地址--> 
    <property>
        <name>yarn.resourcemanager.zk-address</name>
        <value>hadoop1:2181,hadoop2:2181,hadoop3:2181</value>
    </property>

    <!--启用自动恢复--> 
    <property>
        <name>yarn.resourcemanager.recovery.enabled</name>
        <value>true</value>
    </property>
 
    <!--指定resourcemanager的状态信息存储在zookeeper集群--> 
    <property>
        <name>yarn.resourcemanager.store.class</name>     <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
</property>
</configuration>
```
###### (b)同步更新其他节点的配置信息
##### (3).启动hdfs 
(a)在各个JournalNode节点上，输入以下命令启动journalnode服务：
```shell
sbin/hadoop-daemon.sh start journalnode
```
(b)在[nn1]上，对其进行格式化，并启动：
```shell
bin/hdfs namenode -format
sbin/hadoop-daemon.sh start namenode
```
(c )在[nn2]上，同步nn1的元数据信息：
```shell
bin/hdfs namenode -bootstrapStandby
```
(d)启动[nn2]：
```shell
sbin/hadoop-daemon.sh start namenode
```
(e)启动所有datanode
```shell
sbin/hadoop-daemons.sh start datanode
```
(f)将[nn1]切换为Active
```shell
bin/hdfs haadmin -transitionToActive nn1
```
##### (4).启动yarn 
(a)在hadoop1中执行：
```shell
sbin/start-yarn.sh
```
(b)在hadoop2中执行：
```shell
sbin/yarn-daemon.sh start resourcemanager
```
(c)查看服务状态
```shell
bin/yarn rmadmin -getServiceState rm1
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY1YjllNzQ2YzUxMjQzZmIucG5n?x-oss-process=image/format,png)

(d)强制切换状态：

```shell
bin/yarn rmadmin -transitionToStandby rm2 --forcemanual
```

(e)关于sbin/start.all.sh
hadoop1:
```shell
sbin/start.all.sh
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJjNTk2YjE0NWZkMzRlZDEucG5n?x-oss-process=image/format,png)
### 二.HDFS Federation架构设计
#### 1.NameNode架构的局限性
##### (1)Namespace(命名空间)的限制
由于NameNode在内存中存储所有的元数据(metadata)，因此单个namenode所能存储的对象(文件+块)数目受到namenode所在JVM的heap size的限制。50G的heap能够存储20亿(200million)个对象，这20亿个对象支持4000个datanode，12PB的存储（假设文件平均大小为40MB）。随着数据的飞速增长，存储的需求也随之增长。单个datanode从4T增长到36T，集群的尺寸增长到8000个datanode。存储的需求从12PB增长到大于100PB。

##### (2)隔离问题
由于HDFS仅有一个namenode，无法隔离各个程序，因此HDFS上的一个实验程序就很有可能影响整个HDFS上运行的程序。

##### (3)性能的瓶颈
由于是单个namenode的HDFS架构，因此整个HDFS文件系统的吞吐量受限于单个namenode的吞吐量。

#### 2.HDFS Federation架构设计
能不能有多个NameNode

|NameNode|NameNode|NameNode|
|-|-|-|
|元数据|元数据|元数据|
|Log|machine|电商数据/话单数据|

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMxM2ZiYmM1Mjg0MDVlNjEucG5n?x-oss-process=image/format,png)
#### 3.HDFS Federation应用思考
不同应用可以使用不同NameNode进行数据管理
图片业务、爬虫业务、日志审计业务
Hadoop生态系统中，不同的框架使用不同的namenode进行管理namespace。（隔离性）