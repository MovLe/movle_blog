---
title: '[Kafka]-Kafka命令行操作'
date: 2019-02-02 15:50:00
tags: Kafka
categories: Kafka
---

#### 1.查看当前服务器中的所有topic
```shell
bin/kafka-topics.sh --zookeeper bigdata13:2181 --list
```
#### 2.创建topic
```shell
bin/kafka-topics.sh --zookeeper bigdata13:2181 --create --replication-factor 3 --partitions 1 --topic first
```
选项说明：
```shell
--topic 定义topic名

--replication-factor  定义副本数

--partitions  定义分区数
```
#### 3.删除topic
```shell
bin/kafka-topics.sh --zookeeper bigdata11:2181 --delete --topic first
```
需要server.properties中设置delete.topic.enable=true否则只是标记删除或者直接重启。

#### 4.发送消息
```shell
bin/kafka-console-producer.sh --broker-list bigdata11:9092 --topic first

>hello world

>itstar  itstar
```
#### 5.消费消息
```shell
bin/kafka-console-consumer.sh --zookeeper bigdata11:2181 --from-beginning --topic first
```
--from-beginning：会把first主题中以往所有的数据都读取出来。根据业务场景选择是否增加该配置。

#### 6.查看某个Topic的详情
```shell
bin/kafka-topics.sh --zookeeper bigdata11:2181 --describe --topic first 
```
