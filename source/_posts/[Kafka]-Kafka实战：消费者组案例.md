---
title: '[Kafka]-Kafka实战-消费者组案例'
date: 2019-02-02 21:50:00
tags: Kafka
categories: Kafka
---

#### 1.需求：
测试同一个消费者组中的消费者，同一时刻只能有一个消费者消费。
#### 2.案例实操
(1)在hadoop2、hadoop3上修改/opt/module/kafka-2.11/config/consumer.properties配置文件中的group.id属性为任意组名。
```shell
vi consumer.properties

group.id=movle
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVmYTZhNTU3ZTY3ODU3NDcucG5n?x-oss-process=image/format,png)

(2)再hadoop3、hadoop4上分别启动消费者
```shell
./bin/kafka-console-consumer.sh --bootstrap-server hadoop2:9092 --topic topic0519 --consumer.config config/consumer.properties

./bin/kafka-console-consumer.sh --bootstrap-server hadoop2:9092 --topic topic0519 --consumer.config config/consumer.properties
```
(3)在hadoop2上启动生产者
```shell
bin/kafka-console-producer.sh --broker-list hadoop2:9092 --topic topic0519
>hello world
```
(4)查看bigdata11和bigdata12的接收者。
同一时刻只有一个消费者接收到消息。

![hadoo2生产者](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNiZGIyODcwZTJjNWVlZDkucG5n?x-oss-process=image/format,png)

![hadoop3消费者](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUzOGFmOWYxZGQ4Yjc4ODgucG5n?x-oss-process=image/format,png)

![hadoop4消费者](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ5NWVlYTk4ODg4NjIyN2YucG5n?x-oss-process=image/format,png)
