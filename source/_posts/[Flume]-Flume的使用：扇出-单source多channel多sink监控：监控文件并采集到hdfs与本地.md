---
title: '[Flume]-Flume的使用：扇出-单source多channel多sink监控：监控文件并采集到hdfs与本地'
date: 2019-07-03 17:00:00
tags: 
- Flume
- Flume实战
categories: Flume
---

#### 0.图解过程：
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdjN2IxOWVkMGU4MjI5MWEucG5n?x-oss-process=image/format,png)
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY4N2I2ZGU4NWE3OTI5MzIucG5n?x-oss-process=image/format,png)

#### 1.修改多配置文件,flumejob_1.conf,flumejob_2.conf,flumejob_3.conf
##### (1)flumejob_1.conf
```shell
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1 c2
# 将数据流复制给多个channel
a1.sources.r1.selector.type = replicating

# 2.source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /opt/plus
a1.sources.r1.shell = /bin/bash -c

# 3.sink1
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop3
a1.sinks.k1.port = 4141

# sink2
a1.sinks.k2.type = avro
a1.sinks.k2.hostname = hadoop4
a1.sinks.k2.port = 4141

# 4.channel—1
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# 4.channel—2
a1.channels.c2.type = memory
a1.channels.c2.capacity = 1000
a1.channels.c2.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1 c2
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c2
```
##### (2)flumejob_2.conf
```shell
# 1 agent
a2.sources = r1
a2.sinks = k1
a2.channels = c1

# 2 source
a2.sources.r1.type = avro
a2.sources.r1.bind = hadoop3
a2.sources.r1.port = 4141

# 3 sink
a2.sinks.k1.type = hdfs
a2.sinks.k1.hdfs.path = hdfs://hadoop2:9000/flume2/%H
#上传文件的前缀
a2.sinks.k1.hdfs.filePrefix = flume2-
#是否按照时间滚动文件夹
a2.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a2.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位
a2.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a2.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个Event才flush到HDFS一次
a2.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可支持压缩
a2.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件
a2.sinks.k1.hdfs.rollInterval = 600
#设置每个文件的滚动大小大概是128M
a2.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与Event数量无关
a2.sinks.k1.hdfs.rollCount = 0
#最小副本数
a2.sinks.k1.hdfs.minBlockReplicas = 1


# 4 channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 100

#5 Bind 
a2.sources.r1.channels = c1
a2.sinks.k1.channel = c1
```
##### (3)flumejob_3.conf
```shell
#1 agent
a3.sources = r1
a3.sinks = k1
a3.channels = c1

# 2 source
a3.sources.r1.type = avro
a3.sources.r1.bind = hadoop4
a3.sources.r1.port = 4141

#3 sink
a3.sinks.k1.type = file_roll
#备注：此处的文件夹需要先创建好
a3.sinks.k1.sink.directory = /opt/flume3

# 4 channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 1000
a3.channels.c1.transactionCapacity = 100

# 5 Bind
a3.sources.r1.channels = c1
a3.sinks.k1.channel = c1
```
#### 2.启动：先启动1，再启动2，3
由于flumejob_3.conf是采集到本地，故本地linux必须存在/root/flume2目录
```shell
bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/flumejob_1.conf

bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/flumejob_2.conf

bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/flumejob_3.conf
```
#### 3.验证：
修改文件，进行操作，并在本地/root/flume2或hdfs下/flume2目录下查看