---
title: '[ZooKeeper]-ZooKeeper本地模式安装'
date: 2019-01-03 11:50:00
tags: 
- ZooKeeper
- 安装部署
categories: ZooKeeper
---
#### 1.安装前准备：
(1)安装jdk
(2)上传zookeeper到linux系统下
(3)解压到指定目录
```shell
tar -zxvf zookeeper-3.4.10.tar.gz -C /opt/module/
```
![解压](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU0M2M4YzAzNTNkNDBmMTcucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY2MzBmN2FmZDA5ZTg4MTIucG5n?x-oss-process=image/format,png)


(4)配置环境变量

输入命令：
```shell
vi /etc/profile
```
添加内容：
```shell
export ZOOKEEPER_HOME=/opt/module/zookeeper-3.4.10
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```
![环境变量](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgwZTAwY2YxYmMyMGY1NWUucG5n?x-oss-process=image/format,png)
#### 2.配置修改
(1)将/opt/module/zookeeper-3.4.10/conf这个路径下的zoo_sample.cfg修改为zoo.cfg；
```shell
mv zoo_sample.cfg  zoo.cfg
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU0ZjljM2IxY2Q5M2I1OTEucG5n?x-oss-process=image/format,png)

(2)进入zoo.cfg文件：
```shell
vim zoo.cfg
```
(3)修改dataDir路径为
```shell
dataDir=/opt/module/zookeeper-3.4.10/zkData
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc3ZDhjZDI1MzkzNzVlNmQucG5n?x-oss-process=image/format,png)

(4)在/opt/module/zookeeper-3.4.10/这个目录上创建zkData文件夹

```		shell	
cd /opt/module/zookeeper-3.4.10 

mkdir zkData
```
![创建文件夹](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI5MGE5ZmNkOTlkNDk3MGYucG5n?x-oss-process=image/format,png)

#### 3.操作zookeeper
##### (1)启动zookeeper
```shell
bin/zkServer.sh start
```
##### (2)查看进程是否启动
```shell
jps
```

##### (3)查看状态：
```shell
bin/zkServer.sh status
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg2ZTAwZTRkMGQyODhkN2UucG5n?x-oss-process=image/format,png)

##### (4)启动客户端：
```shell
bin/zkCli.sh
```
![启动客户端](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFkOTBkZWI3YjQ0ZTQ4NWUucG5n?x-oss-process=image/format,png)

##### (5)退出客户端：
```shell
quit
```

![退出客户端](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ2MGE3MWIyMGIyMjY2MmIucG5n?x-oss-process=image/format,png)

##### (6)停止zookeeper
```shell
bin/zkServer.sh stop
```

![停止zookeeper](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlmODA1MDljMmFkYWExZTUucG5n?x-oss-process=image/format,png)