---
title: '[Flume]-Flume的使用：监听本地linux文件并采集到hdfs'
date: 2019-07-03 15:00:00
tags: 
- Flume
- Flume实战
categories: Flume
---

#### 1.新建配置文件flumejob_hdfs.conf,然后上传

```shell
# Name the components on this agent agent别名设置
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source  设置数据源监听本地文件配置
# exec 执行一个命令的方式去查看文件 tail -F 实时查看
a1.sources.r1.type = exec
# 要执行的脚本command tail -F 默认10行 man tail  查看帮助
a1.sources.r1.command = tail -F /opt/movle
# 执行这个command使用的是哪个脚本 -c 指定使用什么命令
# whereis bash
# bash: /usr/bin/bash /usr/man/man1/bash.1.gz 
a1.sources.r1.shell = /bin/bash -c

# Describe the sink 
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://hadoop2:9000/flume/%Y%m%d/%H
#上传文件的前缀
a1.sinks.k1.hdfs.filePrefix = logs-
#是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹  秒 （默认30s）
a1.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位（每小时滚动一个文件夹）
a1.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个 Event 才 flush 到 HDFS 一次
a1.sinks.k1.hdfs.batchSize = 1000
#设置文件类型，可支持压缩
a1.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件 秒
a1.sinks.k1.hdfs.rollInterval = 600
#设置每个文件的滚动大小 字节（最好128M）
a1.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与 Event 数量无关
a1.sinks.k1.hdfs.rollCount = 0
#最小冗余数(备份数 生成滚动功能则生效roll hadoop本身有此功能 无需配置) 1份 不冗余
a1.sinks.k1.hdfs.minBlockReplicas = 1

# Use a channel which buffers events in memory 
a1.channels.c1.type = memory 
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 1000

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

#### 2.启动

```shell
bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/flumejob_hdfs.conf
```

#### 3.验证

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRiOTYzY2ExMTNmMDdiM2EucG5n?x-oss-process=image/format,png)

