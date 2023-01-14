---
title: 'Spark HA的实现'
date: 2020-03-10 13:00:00
tags: Spark
categories: Spark
---
![分布式的HA](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY5ZmJjMTRhZmM0NTk0NjAucG5n?x-oss-process=image/format,png)

#### 1.基于文件系统的单点恢复(注意：不能用于生产，主要用于开发和测试)
&nbsp;&nbsp;&nbsp;&nbsp;主要用于开发或测试环境。当spark提供目录保存spark Application和worker的注册信息，并将他们的恢复状态写入该目录中，这时，一旦Master发生故障，就可以通过重新启动Master进程(sbin/start-master.sh)，恢复已运行的spark Application和worker的注册信息。

&nbsp;&nbsp;&nbsp;&nbsp;基于文件系统的单点恢复，主要是在spark-env.sh里对SPARK_DAEMON_JAVA_OPTS设置

|配置参数|参考值|
|:-:|:-:|
|spark.deploy.recoveryMode|	设置为FILESYSTEM开启单点恢复功能，默认值：NONE|
|spark.deploy.recoveryDirectory|Spark 保存恢复状态的目录|

##### (1)在spark-env.sh中添加
```shell
export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=FILESYSTEM -Dspark.deploy.recoveryDirectory=/opt/module/spark-2.1.0-bin-hadoop2.7/recovery"
```

##### (2)测试：
a.在hadoop1上启动Spark集群
b.在hadoop2上启动spark shell
```shell
MASTER=spark://hadoop1:7077 spark-shell
```
c.在hadoop1上停止master
```shell
stop-master.sh
```	
d.观察hadoop2上的输出:
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM3ODllZTBkNzI4MWNlN2MucG5n?x-oss-process=image/format,png)

e.在hadoop1上重启master
```shell
start-master.sh
```

#### 2.基于Zookeeper的Standby Masters
&nbsp;&nbsp;&nbsp;&nbsp;ZooKeeper提供了一个Leader Election机制，利用这个机制可以保证虽然集群存在多个Master，但是只有一个是Active的，其他的都是Standby。当Active的Master出现故障时，另外的一个Standby Master会被选举出来。由于集群的信息，包括Worker， Driver和Application的信息都已经持久化到ZooKeeper，因此在切换的过程中只会影响新Job的提交，对于正在进行的Job没有任何的影响。加入ZooKeeper的集群整体架构如下图所示。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY0YTk3ZmMwNzhkZTdhOWUucG5n?x-oss-process=image/format,png)


|配置参数|参考值|
|:-:|:-:|
|spark.deploy.recoveryMode|设置为ZOOKEEPER开启单点恢复功能，默认值：NONE|
|spark.deploy.zookeeper.url|ZooKeeper集群的地址|
|spark.deploy.zookeeper.dir|	Spark信息在ZK中的保存目录，默认：/spark|

##### (1)做法：
(a)在每个节点中的spark-env.sh文件中添加以下代码：
```shell
export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=hadoop1:2181,hadoop2:2181,hadoop3:2181 -Dspark.deploy.zookeeper.dir=/spark"
```
(b)另外：每个节点上，需要将以下两行注释掉。
```shell
# export SPARK_MASTER_HOST=hadoop1
# export SPARK_MASTER_PORT=7077
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRjM2IxNTk3ZTI0YWI1NzQucG5n?x-oss-process=image/format,png)

(c )start-all启动spark集群
从任意Worker节点，再启动一个Master

(d)ZooKeeper中保存的信息

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM5ZTAzYmRiMzI1MmRlMGMucG5n?x-oss-process=image/format,png)