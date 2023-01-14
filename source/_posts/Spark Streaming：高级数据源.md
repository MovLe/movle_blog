---
title: 'Spark Streaming：高级数据源'
date: 2020-03-10 19:00:00
tags: 
- Spark
- Spark Streaming
categories: Spark
---


### 一.Spark Streaming接收Flume数据

#### 1.基于Flume的Push模式

&nbsp;&nbsp;&nbsp;&nbsp;Flume被用于在Flume agents之间推送数据.在这种方式下,Spark Streaming可以很方便的建立一个receiver,起到一个Avro agent的作用.Flume可以将数据推送到改receiver.

##### (1)第一步：Flume的配置文件
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRlNTIwM2ViNTFjMDg0YTUucG5n?x-oss-process=image/format,png)

##### (2)第二步：Spark Streaming程序

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkyODRiZGZkNWIxZjA3N2EucG5n?x-oss-process=image/format,png)

##### (3)第三步：注意除了需要使用Flume的lib的jar包以外，还需要以下jar包：
```shell
spark-streaming-flume_2.1.0.jar
```

##### (4)第四步：测试

* 启动Spark Streaming程序
* 启动Flume
* 拷贝日志文件到/root/training/logs目录
* 观察输出，采集到数据

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQxZDNjMmZjOGU0YzNjYzAucG5n?x-oss-process=image/format,png)


#### 2.基于Custom Sink的Pull模式
&nbsp;&nbsp;&nbsp;&nbsp;不同于Flume直接将数据推送到Spark Streaming中，第二种模式通过以下条件运行一个正常的Flume sink。Flume将数据推送到sink中，并且数据保持buffered状态。Spark Streaming使用一个可靠的Flume接收器和转换器从sink拉取数据。只要当数据被接收并且被Spark Streaming备份后，转换器才运行成功。

&nbsp;&nbsp;&nbsp;&nbsp;这样,与第一种模式相比,保证了很好的健壮性和容错能力。然而,这种模式需要为Flume配置一个正常的sink。

以下为配置步骤：

##### (1)第一步：Flume的配置文件

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVhYzFiYzk3MDI0MTliMTMucG5n?x-oss-process=image/format,png)

##### (2)第二步：Spark Streaming程序

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZjODhkMjAyNTVhMzIxNGEucG5n?x-oss-process=image/format,png)


##### (3)第三步：需要的jar包
* 将Spark的jar包拷贝到Flume的lib目录下
* 下面的这个jar包也需要拷贝到Flume的lib目录下，同时加入IDEA工程的classpath

```shell
spark-streaming-flume-sink_2.1.0.jar
```


##### (4)第四步：测试

* 启动Flume
* 在IDEA中启动FlumeLogPull
* 将测试数据拷贝到/root/training/logs
* 观察IDEA中的输出

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI5OTY2YjU2NTE0MGZmNzcucG5n?x-oss-process=image/format,png)



### 二.Spark Streaming接收Kafka数据
Apache Kafka是一种高吞吐量的分布式发布订阅消息系统。

![Kafka](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI3NTNmODdiN2VhZjM1ODUucG5n?x-oss-process=image/format,png)


#### 1.搭建ZooKeeper（Standalone）：
##### (1)配置/root/training/zookeeper-3.4.10/conf/zoo.cfg文件
```shell
dataDir=/root/training/zookeeper-3.4.10/tmp
server.1=spark81:2888:3888
```
##### (2)在/root/training/zookeeper-3.4.10/tmp目录下创建一个myid的空文件
```shell
echo 1 > /root/training/zookeeper-3.4.6/tmp/myid
```
#### 2.搭建Kafka环境(单机单broker)：
##### (1)修改server.properties文件

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWExZTJkMzQ5NzYzMzIxYzYucG5n?x-oss-process=image/format,png)


##### (2)启动Kafka
```shell
bin/kafka-server-start.sh config/server.properties &
```
 出现以下错误：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUxYWFjZjM2OTJmOTVjMmQucG5n?x-oss-process=image/format,png)

 需要修改bin/kafka-run-class.sh文件，将这个选项注释掉。

##### (3)测试Kafka
* 创建Topic
```shell
bin/kafka-topics.sh --create --zookeeper spark81:2181 -replication-factor 1 --partitions 3 --topic mydemo1
```
* 发送消息
```shell
bin/kafka-console-producer.sh --broker-list spark81:9092 --topic mydemo1
```
* 接收消息
```shell
bin/kafka-console-consumer.sh --zookeeper spark81:2181 --topic mydemo1
```

#### 3.搭建Spark Streaming和Kafka的集成开发环境

&nbsp;&nbsp;&nbsp;&nbsp;由于Spark Streaming和Kafka集成的时候，依赖的jar包比较多，而且还会产生冲突。强烈建议使用Maven的方式来搭建项目工程。
下面是依赖的pom.xml文件：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY0NDNmNjA2Y2Y4ZjRkY2MucG5n?x-oss-process=image/format,png)

#### 4.基于Receiver的方式

&nbsp;&nbsp;&nbsp;&nbsp;这个方法使用了Receivers来接收数据。Receivers的实现使用到Kafka高层次的消费者API。对于所有的Receivers，接收到的数据将会保存在Spark executors中，然后由Spark Streaming启动的Job来处理这些数据。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVmZDc1ODhkZWMzYWQ2NjcucG5n?x-oss-process=image/format,png)

##### (1)开发Spark Streaming的Kafka Receivers

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ2MDM1OWJmMTQ4YTM2NzkucG5n?x-oss-process=image/format,png)

##### (2)测试
* 启动Kafka消息的生产者
```shell
bin/kafka-console-producer.sh --broker-list spark81:9092 --topic mydemo1
```
* 在IDEA中启动任务，接收Kafka消息

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ1NjgyNmU5MzY5MDgwNGEucG5n?x-oss-process=image/format,png)


#### 5.直接读取方式

&nbsp;&nbsp;&nbsp;&nbsp;和基于Receiver接收数据不一样，这种方式定期地从Kafka的topic+partition中查询最新的偏移量，再根据定义的偏移量范围在每个batch里面处理数据。当作业需要处理的数据来临时，spark通过调用Kafka的简单消费者API读取一定范围的数据。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE4ZDQ4YzI5ODY1MjczYzkucG5n?x-oss-process=image/format,png)

##### (1)开发Spark Streaming的程序

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdmYjViNGQ2ZDQ3MGQ3MmEucG5n?x-oss-process=image/format,png)



##### (2)测试
* 启动Kafka消息的生产者
```shell
bin/kafka-console-producer.sh --broker-list spark81:9092 --topic mydemo1
```
* 在IDEA中启动任务，接收Kafka消息

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEyMzUyZTYzYTRlY2UyZGMucG5n?x-oss-process=image/format,png)
