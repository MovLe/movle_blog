---
title: 'CentOS7(单节点)安装elasticsearch'
date: 2020-01-11 15:00:00
tags: 
- Elasticsearch
- 安装部署
- CentOS
categories: Elasticsearch
---

#### 1.下载安装包
#### 2.上传到linux
#### 3.解压：
```shell
cd /usr/local/tars

tar -zxvf elasticsearch-5.6.2.tar.gz -C /usr/local
```

#### 4.新建目录

```shell
cd /usr/local/elasticsearch-5.6.2

mkdir data                    
mkdir logs     
```

#### 5.修改配置文件：

```shell
cd /usr/local/elasticsearch-5.6.2/config

vi elasticsearch.yml
```

修改内容如下：

```shell
cluster.name: my-application

node.name: hadoop1

path.data: /usr/local/elasticsearch-5.6.2/data

path.logs: /usr/local/elasticsearch-5.6.2/logs

bootstrap.memory_lock: false
bootstrap.system_call_filter: false

network.host: hadoop1

discovery.zen.ping.unicast.hosts: ["hadoop1"]
```
#### 6.修改limits.conf 文件

```shell
vi /etc/security/limits.conf

//在文件末尾添加如下内容

* soft nofile 655360
* hard nofile 131072
* soft nproc 4096
* hard nproc 4096
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU3MjllYjlhYzhjNWUwN2EucG5n?x-oss-process=image/format,png)

#### 7.修改limit.d下的文件

```shell
vi /etc/security/limits.d/20-nproc.conf

//修改内容如下
*    soft    nproc    1024
//改为
*    soft    nproc    4096
```
![20-nproc.conf](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc3MWQxYjljOGVlMTVhY2IuanBn?x-oss-process=image/format,png)

#### 8.修改sysctl.conf文件

```shell
vi /etc/sysctl.conf

//添加内容
vm.max_map_count=655360

```
![sysct;.conf](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRmYjE0NzhiNmM3MjNkODcuanBn?x-oss-process=image/format,png)

#### 9.新建用户，elasticsearch不能以root用户启动

```shell
useradd hduser

cd /user/local

chown -R hduser ./elasticsearch-5.6.2/
chgrp -R hduser ./elasticsearch-5.6.2/
```
#### 10.切换到hduser用户，启动elasticsearch
```shell
su hduser

cd /usr/local/elasticsearch-5.6.2

./bin/elasticsearch -d                              //后台启动
```
