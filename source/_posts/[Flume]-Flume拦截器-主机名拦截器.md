---
title: '[Flume]-Flume拦截器-主机名拦截器'
date: 2019-07-03 20:00:00
tags: 
- Flume
- Flume拦截器
categories: Flume
---

#### 0.功能作用：
将时间戳放到event的header(Map<key,value>)
#### 1.Host.conf
```shell
#1.定义agent
a1.sources= r1
a1.sinks = k1
a1.channels = c1

#2.定义source
a1.sources.r1.type = exec
a1.sources.r1.channels = c1
a1.sources.r1.command = tail -F /opt/plus
#拦截器
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = host

#参数为true时用IP192.168.1.111，参数为false时用主机名，默认为true
a1.sources.r1.interceptors.i1.useIP = false
a1.sources.r1.interceptors.i1.hostHeader = agentHost

 #3.定义sinks
a1.sinks.k1.type=hdfs
a1.sinks.k1.hdfs.path = hdfs://hadoop2:9000/flumehost/%{agentHost}
a1.sinks.k1.hdfs.filePrefix = plus_%{agentHost}
#往生成的文件加后缀名.log
a1.sinks.k1.hdfs.fileSuffix = .log
a1.sinks.k1.hdfs.fileType = DataStream
a1.sinks.k1.hdfs.writeFormat = Text
a1.sinks.k1.hdfs.rollInterval = 10
a1.sinks.k1.hdfs.useLocalTimeStamp = true
 
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
 
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

#### 2.启动命令：
```shell
bin/flume-ng agent -c conf/ -f jobconf/host.conf -n a1 -Dflume.root.logger=INFO,console
```