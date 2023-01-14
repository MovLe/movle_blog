---
title: '[Flume]-Flume的使用：监听端口'
date: 2019-07-03 16:02:00
tags: 
- Flume
- Flume实战
categories: Flume
---


#### 1.新建配置文件flumejob_telnet.conf

```shell
#smple.conf: A single-node Flume configuration

# Name the components on this agent 定义变量方便调用 加s可以有多个此角色
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source 描述source角色 进行内容定制
# 此配置属于tcp source 必须是netcat类型
a1.sources.r1.type = netcat 
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Describe the sink 输出日志文件
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory（file） 使用内存 总大小1000 每次传输100
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel 一个source可以绑定多个channel 
# 一个sinks可以只能绑定一个channel  使用的是图二的模型
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
```
#### 2.上传到/opt/module/flume/conf

#### 3.启动命令：

```shell
bin/flume-ng agent --conf conf/ --name a1 --conf-file conf/flumejob_telnet.conf -Dflume.root.logger=INFO,console

bin/flume-ng agent     //使用ng启动agent
--conf conf/           //指定配置所在文件夹
--name a1              //指定agent别买
--conf-file conf/flumejob_telnet.conf   //指定配置文件 
-Dflume.root.logger=INFO,console       //指定日志级别
```

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNjNzM2MDdkZmFjY2MzZjcucG5n?x-oss-process=image/format,png)


#### 4.测试
##### (1).下载telnet：往端口内发送数据(netcat也可以)

```shell
yum install nc 

yum search telnet 

yum install telnet.x86_64
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJjNmJiZmY1NDM5ZWJlYjEucG5n?x-oss-process=image/format,png)



##### (2).开启telnet工具，输入信息

```shell
telnet localhost 444444   //开启

11
22
33
are you ok       //输入信息
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ0NzQ4MDQyZTVlOTI2ZTcucG5n?x-oss-process=image/format,png)


##### (3).查看监控
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI5NTljYjI5ZTViZjZhNmEucG5n?x-oss-process=image/format,png)