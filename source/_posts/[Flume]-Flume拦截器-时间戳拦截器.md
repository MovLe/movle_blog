---
title: '[Flume]-Flume拦截器-时间戳拦截器'
date: 2019-07-03 19:00:00
tags: 
- Flume
- Flume拦截器
categories: Flume
---

#### 0.功能作用：
将时间戳放到event的header(Map<key,value>)

#### 1.Timestamp.conf
```shell
#1.定义agent名， source、channel、sink的名称
a4.sources = r1
a4.channels = c1
a4.sinks = k1

#2.具体定义source
a4.sources.r1.type = spooldir
a4.sources.r1.spoolDir = /opt/module/flume-1.8.0/upload

#定义拦截器，为文件最后添加时间戳
a4.sources.r1.interceptors = timestamp
a4.sources.r1.interceptors.timestamp.type = org.apache.flume.interceptor.TimestampInterceptor$Builder

#具体定义channel
a4.channels.c1.type = memory
a4.channels.c1.capacity = 10000
a4.channels.c1.transactionCapacity = 100


#具体定义sink
a4.sinks.k1.type = hdfs
a4.sinks.k1.hdfs.path = hdfs://hadoop2:9000/flume-interceptors/%H
a4.sinks.k1.hdfs.filePrefix = events-
a4.sinks.k1.hdfs.fileType = DataStream

#不按照条数生成文件
a4.sinks.k1.hdfs.rollCount = 0
#HDFS上的文件达到128M时生成一个文件
a4.sinks.k1.hdfs.rollSize = 134217728
#HDFS上的文件达到60秒生成一个文件
a4.sinks.k1.hdfs.rollInterval = 60

#组装source、channel、sink
a4.sources.r1.channels = c1
a4.sinks.k1.channel = c1
```

#### 2.启动命令：
```shell
bin/flume-ng agent -c conf/ -f jobconf/uuid.conf -n a4 -Dflume.root.logger==INFO,console
```