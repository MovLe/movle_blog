---
title: 'Elasticsearch集群安装部署'
date: 2020-01-12 13:00:00
tags: 
- Elasticsearch
- 安装部署
categories: Elasticsearch
---

#### 1.前提：安装好jdk
#### 2.下载安装包：
#### 3.上传到linux
#### 4.解压

#### 5.新建data，logs文件夹

```shell
cd /opt/module/elasticsearch-5.6.2

mkdir data

mkdir logs
```

![新建目录](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZkYTg5MmMwNWQ5ZDU1NGMucG5n?x-oss-process=image/format,png)

#### 6.修改配置文件(root用户)三台机器都需要改

##### (1).修改elasticsearch.yml文件

```shell
vi /opt/module/elasticsearch-5.6.2/conf/elasticsearch.yml

```
修改内容如下：

```yml
# ---------------------------------- Cluster -------------------------------------
cluster.name: my-application
# ------------------------------------ Node --------------------------------------
node.name: JBS1
# ----------------------------------- Paths ---------------------------------------
path.data: /opt/module/elasticsearch-5.6.2/data
path.logs: /opt/module/elasticsearch-5.6.2/logs
# ----------------------------------- Memory -----------------------------------
bootstrap.memory_lock: false
bootstrap.system_call_filter: false
# ---------------------------------- Network ------------------------------------
network.host: 192.168.127.121 
# --------------------------------- Discovery ------------------------------------
discovery.zen.ping.unicast.hosts: ["bigdata121"]

http.cors.enabled: true
http.cors.allow-origin: "*"
node.master: true
node.data: true
```

说明：
* cluster.name ：如果要配置集群需要两个节点上的 elasticsearch 配置的 cluster.name 相同，都启动可以自动组成集群，这里如果不改 cluster.name 则默认是 cluster.name=my-application。
* nodename 随意取但是集群内的各节点不能相同
* node.master:设置为主节点
* node.data:


![elasticsearch.yml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY3YWY5MjZjYWZjZDg2MjUucG5n?x-oss-process=image/format,png)

![elasticsearch.yml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU1YjUyMjRkNDM4NGQyZGYucG5n?x-oss-process=image/format,png)


##### (2).修改limits.conf文件

```conf
vi /etc/security/limits.conf

//在文件末尾添加如下内容

* soft nofile 65536
* hard nofile 131072
* soft nproc 4096
* hard nproc 4096
```

![limits.conf](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlhY2YyNmRhOGYxMWZhODkucG5n?x-oss-process=image/format,png)


##### (3).修改limits.d文件

```conf
vi /etc/security/limits.d/20-nproc.conf

//修改内容如下
//*    soft    nproc    1024
//改为
*    soft    nproc    4096
```

![20-nproc.conf](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU5NDg4ZjE3MWYxMjYyYTAucG5n?x-oss-process=image/format,png)

##### (4).修改配置sysctl.conf

```shell
vi /etc/sysctl.conf

//添加内容
vm.max_map_count=655360
```

![sysctl.conf](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTExODZkOGZjODM3ZTY0NmYucG5n?x-oss-process=image/format,png)

#### 5.新建linux用户:es不能以root用户启动

```shell
useradd kai

passwd kai
输入密码


cd /opt/module/elasticsearch-5.6.2       //进入的elasticsearch目录

chown -R kai:users *      //修改权限

su kai        //切换到kai用户，启动es
```
![新建用户](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU4ZDI1NjVmM2Q1OTMyNzEucG5n?x-oss-process=image/format,png)

#### 6.将elasticsearch发送到bigdata122，bigdata123

```shell
cd /opt/module
scp -r elasticsearch-5.6.2/ bigdata122:/opt/module

scp -r elasticsearch-5.6.2/ bigdata123:/opt/module
```

#### 7.再在bigdata122，bigdata123中修改elasticsearch.yml

```yml
# ------------------------------------ Node --------------------------------------
node.name: JBS2
# ---------------------------------- Network ------------------------------------
network.host: 192.168.127.122     //bigdata123改为：192.168.127.123 

node.master: false
node.data: true
```
其实和单节点安装差不多，只是elasticsearch.yml文件有细微不同

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRkMWY4MGMyOGEzMTBiNjcucG5n?x-oss-process=image/format,png)

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIzYjJkNjkxOGQ5MGUxYWYucG5n?x-oss-process=image/format,png)

#### 8.再在bigdata122，bigdata123中修改linux配置文件，和bigdata121一样，以及新建用户
#### 9.重启bigdata121,bigdata122,bigdata123,因为修改linux配置文件后需要重启后才生效
#### 10.启动ES集群：
##### (1)切换用户：
```shell
su kai
```
##### (2)先启动bigdata121，再启动bigdata122,bigdata123
bigdata121
```shell
cd /opt/module/elasticsearch-6.1.1/

bin/elasticsearch
```
bigdata122
```shell
cd /opt/module/elasticsearch-6.1.1/

bin/elasticsearch
```
bigdata123
```shell
cd /opt/module/elasticsearch-6.1.1/

bin/elasticsearch
```
#### 11.集群安装参照：
【链接】手把手教你搭建一个Elasticsearch集群
https://cloud.tencent.com/developer/article/1189282