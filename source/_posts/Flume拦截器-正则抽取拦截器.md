---
title: 'Flume拦截器-正则抽取拦截器'
date: 2019-07-03 22:03:00
tags: 
- Flume
- Flume拦截器
categories: Flume
---

#### 1.extractor.conf
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
a1.sources.r1.interceptors.i1.type = regex_extractor
# hostname is bigdata111 ip is 192.168.20.111
a1.sources.r1.interceptors.i1.regex = hostname is (.*?) ip is (.*)
a1.sources.r1.interceptors.i1.serializers = s1 s2
#hostname（自定义）= (.*?)->bigdata111 
a1.sources.r1.interceptors.i1.serializers.s1.name = hostname
#ip（自定义） = (.*)->192.168.20.111
a1.sources.r1.interceptors.i1.serializers.s2.name = ip

a1.sinks.k1.type = logger

a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```

#### 2.启动命令：
```shell
bin/flume-ng agent -c conf/ -f jobconf/extractor.conf -n a1 -Dflume.root.logger=INFO,console
```