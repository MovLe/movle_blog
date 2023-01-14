---
title: 'Flume的使用：监听文件夹'
date: 2019-07-03 16:00:00
tags: 
- Flume
- Flume实战
categories: Flume
---


#### 1.新建配置文件flumejob_dir.conf
```shell
# 定义别名
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = spooldir
# 监控的文件夹
a1.sources.r1.spoolDir = /opt/testdir
# 上传成功后显示后缀名 
a1.sources.r1.fileSuffix = .COMPLETED
# 如论如何 加绝对路径的文件名 默认false
a1.sources.r1.fileHeader = true
#忽略所有以.tmp 结尾的文件（正在被写入），不上传
# ^以任何开头 出现无限次 以.tmp结尾的
a1.sources.r1.ignorePattern = ([^ ]*\.tmp)

# Describe the sink 下沉到hdfs
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://hadoop2:9000/flume/testdir/%Y%m%d/%H
#上传文件的前缀
a1.sinks.k1.hdfs.filePrefix = testdir-
#是否按照时间滚动文件夹
a1.sinks.k1.hdfs.round = true
#多少时间单位创建一个新的文件夹
a1.sinks.k1.hdfs.roundValue = 1
#重新定义时间单位
a1.sinks.k1.hdfs.roundUnit = hour
#是否使用本地时间戳
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#积攒多少个 Event 才 flush 到 HDFS 一次
a1.sinks.k1.hdfs.batchSize = 100
#设置文件类型，可支持压缩
a1.sinks.k1.hdfs.fileType = DataStream
#多久生成一个新的文件
a1.sinks.k1.hdfs.rollInterval = 600
#设置每个文件的滚动大小大概是 128M 
a1.sinks.k1.hdfs.rollSize = 134217700
#文件的滚动与 Event 数量无关
a1.sinks.k1.hdfs.rollCount = 0
#最小副本数
a1.sinks.k1.hdfs.minBlockReplicas = 1

# Use a channel which buffers events in memory 
a1.channels.c1.type = memory 
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1 
a1.sinks.k1.channel = c1
```

#### 2.启动

```shell
cd /opt/module/flume  
 
bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/flumejob_dir.conf
```
#### 3.在文件夹内进行操作，比如新建文件，修改文件，之后文件会有后缀名.COMPLETED
* 注意：所监控的文件夹内不允许有子文件夹

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI1ZTM0MjA4ODNiNWRmNjAucG5n?x-oss-process=image/format,png)