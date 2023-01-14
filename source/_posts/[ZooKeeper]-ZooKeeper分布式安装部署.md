---
title: '[ZooKeeper]-ZooKeeper分布式安装部署'
date: 2019-01-03 12:50:00
tags: 
- ZooKeeper
- 安装部署
categories: ZooKeeper
---
#### 1.集群规划

在hadoop1、hadoop2和hadoop3三个节点上部署Zookeeper。

#### 2.解压安装

(1)解压zookeeper安装包到/opt/module/目录下

```shell
tar -zxvf zookeeper-3.4.10.tar.gz -C /opt/module/
```
(2)在/opt/module/zookeeper-3.4.10/这个目录下创建zkData
```shell
mkdir -p zkData
```
(3)重命名/opt/module/zookeeper-3.4.10/conf这个目录下的zoo_sample.cfg为zoo.cfg
```shell
mv zoo_sample.cfg zoo.cfg
```

#### 3.配置zoo.cfg文件
(1)具体配置
```shell
vi /opt/module/zookeeper-3.4.10/conf/zoo.cfg
```
修改内容：
```shell
dataDir=/opt/module/zookeeper-3.4.10/zkData
```
增加如下配置
```shell
#######################cluster##########################

server.1=hadoop1:2888:3888

server.2=hadoop2:2888:3888

server.3=hadoop3:2888:3888
```

![zoo.cfg](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk4MGYxMWJkYzkyNjY5ODUucG5n?x-oss-process=image/format,png)


(2)配置参数解读

```shell
Server.A=B:C:D。
```
* A是一个数字，表示这个是第几号服务器；

* B是这个服务器的ip地址；

* C是这个服务器与集群中的Leader服务器交换信息的端口；

* D是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。

&nbsp;&nbsp;&nbsp;&nbsp;集群模式下配置一个文件myid，这个文件在dataDir目录下，这个文件里面有一个数据就是A的值，Zookeeper启动时读取此文件，拿到里面的数据与zoo.cfg里面的配置信息比较从而判断到底是哪个server。

#### 3.集群操作
(1)在/opt/module/zookeeper-3.4.10/zkData目录下创建一个myid的文件
```shell
touch myid
```
添加myid文件，注意一定要在linux里面创建，在notepad++里面很可能乱码

(2)编辑myid文件
```shell
vi myid
```
在文件中添加与server对应的编号：如1
![1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk0ZWU4YjYxMjEwMjI4Y2UucG5n?x-oss-process=image/format,png)
![myid](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWIzMTkwNGEyYzdlY2UxZGIucG5n?x-oss-process=image/format,png)




(3)拷贝配置好的zookeeper到其他机器上
并分别修改myid文件中内容为2、3,并修改环境变量，和hadoop1一样
```shell
scp -r /opt/module/zookeeper-3.4.10/ root@hadoop2:/opt/module/

scp -r /opt/module/zookeeper-3.4.10/ root@hadoop3:/opt/module/
```
![拷贝](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY4NWUzM2I1MzVlNDI1YWIucG5n?x-oss-process=image/format,png)
![2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMzZWI2MjJmNTBhOWU3NWMucG5n?x-oss-process=image/format,png)
![myid](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVmYjdkOWI2NDRjNDM2M2QucG5n?x-oss-process=image/format,png)
![3](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJhMWQzZTJkM2Y4NmFhMTEucG5n?x-oss-process=image/format,png)
![myid](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJkYmY3NDdlODE2ZTQ1NzQucG5n?x-oss-process=image/format,png)
(4)三台都修改环境变量

```shell
vi /etc/profile
```
修改内容：
```shell
export ZOOKEEPER_HOME=/opt/module/zookeeper-3.4.10
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

使环境变量生效：

```shell
source /etc/profile
```

![修改环境变量](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE3OTE0Y2FiMzgyN2U3YmIucG5n?x-oss-process=image/format,png)

(5)分别启动zookeeper
hadoop1:
```shell
bin/zkServer.sh start
```
hadoop2
```shell
bin/zkServer.sh start
```
hadoop3
```shell
bin/zkServer.sh start
```

(5)查看状态

```shell
bin/zkServer.sh status
```
![hadoop1查看状态](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkyNDEyYWFjZjE5YTFjOTgucG5n?x-oss-process=image/format,png)
```shell
bin/zkServer.sh status
```
![hadoop2查看状态](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI2MGEzMTQxODRkODZlZjIucG5n?x-oss-process=image/format,png)
```shell
bin/zkServer.sh status
```
![hadoop3查看状态](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFiMjU5MGVmNGYzMGI4NjQucG5n?x-oss-process=image/format,png)