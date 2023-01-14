---
title: 'Flume拦截器-UUID拦截器'
date: 2019-07-03 21:00:00
tags: 
- Flume
- Flume拦截器
categories: Flume
---

#### 1.uuid.conf
```shell
a1.sources = r1
a1.sinks = k1
a1.channels = c1

a1.sources.r1.type = exec
a1.sources.r1.channels = c1
a1.sources.r1.command = tail -F /opt/plus
a1.sources.r1.interceptors = i1
#type的参数不能写成uuid，得写具体，否则找不到类
a1.sources.r1.interceptors.i1.type = org.apache.flume.sink.solr.morphline.UUIDInterceptor$Builder
#如果UUID头已经存在,它应该保存
a1.sources.r1.interceptors.i1.preserveExisting = true
a1.sources.r1.interceptors.i1.prefix = UUID_

#如果sink类型改为HDFS，那么在HDFS的文本中没有headers的信息数据
a1.sinks.k1.type = logger

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

#### 2.启动命令：
```shell
bin/flume-ng agent -c conf/ -f jobconf/uuid.conf -n a1 -Dflume.root.logger==INFO,console
```