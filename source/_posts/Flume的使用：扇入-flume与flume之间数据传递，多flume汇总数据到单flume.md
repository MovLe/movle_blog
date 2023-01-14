---
title: 'Flume的使用：扇入-flume与flume之间数据传递，多flume汇总数据到单flume'
date: 2019-07-03 18:00:00
tags: 
- Flume
- Flume实战
categories: Flume
---

#### 1.图示
![扇入](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIwOWMyMmRlMmFhNDg0ZDYucG5n?x-oss-process=image/format,png)

#### 2.新建配置文件：
##### (1)flume-1.conf
```shell
# 1 agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 2 source
a1.sources.r1.type = netcat
a1.sources.r1.bind = hadoop2
a1.sources.r1.port = 55555

#3 sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = hadoop4
a1.sinks.k1.port = 4141

# 4 channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# 5 Bind
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```
##### (2)flume-2.conf
```shell
# 1 agent
a2.sources = r1
a2.sinks = k1
a2.channels = c1

# 2 source
a2.sources.r1.type = exec
a2.sources.r1.command = tail -F /opt/plus
a2.sources.r1.shell = /bin/bash -c

# 3 sink
a2.sinks.k1.type = avro
a2.sinks.k1.hostname = hadoop4
a2.sinks.k1.port = 4141

# 4 channel
a2.channels.c1.type = memory
a2.channels.c1.capacity = 1000
a2.channels.c1.transactionCapacity = 100

# 5. Bind
a2.sources.tail-file.channels = c1
a2.sinks.avro4.channel = c1
```

##### (3)flume-3.conf
```shell
# 1 agent
a3.sources = r1
a3.sinks = k1
a3.channels = c1

# 2 source
a3.sources.r1.type = avro
a3.sources.r1.bind = hadoop4
a3.sources.r1.port = 4141

# 3 sink
a3.sinks.k1.type = hdfs
a3.sinks.k1.hdfs.path = hdfs://hadoop2:9000/flume3/%H
#上传文件的前缀
a3.sinks.k1.hdfs.filePrefix = flume3-
#是否按照时间滚动文件夹
a3.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a3.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位
a3.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a3.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个Event才flush到HDFS一次
a3.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可支持压缩
a3.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件
a3.sinks.k1.hdfs.rollInterval = 600
#设置每个文件的滚动大小大概是128M
a3.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与Event数量无关
a3.sinks.k1.hdfs.rollCount = 0
#最小冗余数
a3.sinks.k1.hdfs.minBlockReplicas = 1

# 4 channel
a3.channels.c1.type = memory
a3.channels.c1.capacity = 1000
a3.channels.c1.transactionCapacity = 100

# 5 Bind
a3.sources.r1.channels = c1
a3.sinks.k1.channel = c1
```

#### 3.测试
##### (1)依次启动flume-3.conf,flume-2.conf,flume-1.conf
##### (2)改变文件，查看结果