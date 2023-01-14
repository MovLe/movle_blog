---
title: 'Flume拦截器-查询替换拦截器'
date: 2019-07-03 22:00:00
tags: 
- Flume
- Flume拦截器
categories: Flume
---

#### 1.search.conf
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
a1.sources.r1.interceptors.i1.type = search_replace

#遇到数字改成itstar，A123会替换为Aitstar
a1.sources.r1.interceptors.i1.searchPattern = [0-9]+
a1.sources.r1.interceptors.i1.replaceString = ***
a1.sources.r1.interceptors.i1.charset = UTF-8

#3 sink
a1.sinks.k1.type = logger

#4 Chanel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

#5 bind
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

#### 2.启动命令：
```shell
bin/flume-ng agent -c conf/ -f jobconf/search.conf -n a1 -Dflume.root.logger=INFO,console
```