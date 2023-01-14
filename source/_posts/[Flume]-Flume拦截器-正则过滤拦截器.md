---
title: '[Flume]-Flume拦截器-正则过滤拦截器'
date: 2019-07-03 22:01:00
tags: 
- Flume
- Flume拦截器
categories: Flume
---
#### 1.filter.conf
```shell
#1 agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

#2 source
a1.sources.r1.type = exec
a1.sources.r1.channels = c1
a1.sources.r1.command = tail -F /opt/plus
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = regex_filter
a1.sources.r1.interceptors.i1.regex = ^A.*
#如果excludeEvents设为false,表示过滤掉不是以A开头的events。如果excludeEvents设为true，则表示过滤掉以A开头的events。
a1.sources.r1.interceptors.i1.excludeEvents = true

a1.sinks.k1.type = logger

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

#### 2.启动命令：
```shell
bin/flume-ng agent -c conf/ -f jobconf/filter.conf -n a1 -Dflume.root.logger=INFO,console
```