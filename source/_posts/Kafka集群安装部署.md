---
title: 'Kafka集群安装部署'
date: 2019-02-02 14:50:00
tags: 
- Kafka
- 安装部署
categories: Kafka
---
### 一. 环境准备

#### 1.集群规划

|hadoop2|hadoop3|hadoop4|
|:-:|:-:|:-:|
|zk|zk|zk|
|kafka|kafka|kafka|

#### 2.jar包下载

[http://kafka.apache.org/downloads.html](http://kafka.apache.org/downloads.html)

#### 3.虚拟机准备

(1).准备3台虚拟机

(2).配置ip地址

(3).配置主机名称

(4).3台主机分别关闭防火墙

#### 4.安装jdk

#### 5.安装Zookeeper

在hadoop2、hadoop3和hadoop4三个节点上部署Zookeeper
见之前的文章

### 二. Kafka集群部署 

#### 1.解压安装包
```shell
tar -zxvf kafka_2.11-0.11.0.2.tgz -C /opt/module/
```
#### 2.修改解压后的文件名称

```shell
mv kafka_2.11-0.11.0.2/ kafka-2.11
```
#### 3.在/opt/module/kafka目录下创建logs文件夹

```shell
cd /opt/module/kafka-2.11

mkdir logs
```
#### 4.修改配置文件
```shell
cd config/

vi server.properties
```
输入以下内容：
```xml
#broker的全局唯一编号，不能重复

broker.id=0

#是否允许删除topic

delete.topic.enable=true

#处理网络请求的线程数量

num.network.threads=3

#用来处理磁盘IO的线程数量

num.io.threads=8

#发送套接字的缓冲区大小

socket.send.buffer.bytes=102400

#接收套接字的缓冲区大小

socket.receive.buffer.bytes=102400

#请求套接字的最大缓冲区大小

socket.request.max.bytes=104857600

#kafka运行日志存放的路径

log.dirs=/opt/module/kafka-2.11/logs

#topic在当前broker上的分区个数

num.partitions=1

#用来恢复和清理data下数据的线程数量

num.recovery.threads.per.data.dir=1

#segment文件保留的最长时间，超时将被删除

log.retention.hours=168

#配置连接Zookeeper集群地址

zookeeper.connect=hadoop2:2181,hadoop3:2181,hadoop4:2181
```
![1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQwODFiZWIzOWU0NmNjYWYucG5n?x-oss-process=image/format,png)
![2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU0MDNmYjQ3YTkyMzI2ZWUucG5n?x-oss-process=image/format,png)
![3](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY2OTRmY2NhNjcwOWQ2OTgucG5n?x-oss-process=image/format,png)

#### 5.配置环境变量
```shell
vi /etc/profile
```

增加内容：
```shell
#KAFKA_HOME
export KAFKA_HOME=/opt/module/kafka-2.11

export PATH=$PATH:$KAFKA_HOME/bin
```
使环境变量生效：
```shell
source /etc/profile
```
#### 6.分发安装包
```shell
cd /opt/module

scp -r /kafka root@hadoop3:/opt/module/
scp -r /kafka root@hadoop4:/opt/module/
```
#### 7.分别在hadoop3和hadoop4上修改配置文件
```shell
vi /opt/module/kafka-2.11/config/server.properties
```
将broker.id改了
```shell
//bigdata12
broker.id=1

//bigdata13
broker.id=2
```
注：broker.id不得重复

#### 8.启动集群

依次在hadoop2、hadoop3、hadoop4节点上启动kafka(加上& ,是在后台启动)
```shell
//hadoop2
bin/kafka-server-start.sh config/server.properties &

//hadoop3
bin/kafka-server-start.sh config/server.properties &

//hadoop4
bin/kafka-server-start.sh config/server.properties &
```
#### 9.关闭集群
```shell
//hadoop2
bin/kafka-server-stop.sh stop

//hadoop3
bin/kafka-server-stop.sh stop

//hadoop4
bin/kafka-server-stop.sh stop
```