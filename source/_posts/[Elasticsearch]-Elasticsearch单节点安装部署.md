---
title: '[Elasticsearch]-Elasticsearch单节点安装部署'
date: 2020-01-12 12:00:00
tags: 
- Elasticsearch
- 安装部署
categories: Elasticsearch
---


### 一.安装
#### 1.先前条件：
* 安装java8
* 下载es安装包

#### 2.上传到linux：/opt/software
#### 3.解压

```shell
cd /opt/software

tar -zxvf elasticsearch-5.6.2.tar.gz -C /opt/module
```
#### 4.新建data，logs文件夹

```shell
cd /opt/module/elasticsearch-5.6.2

mkdir data

mkdir logs
```

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI2YzM2NTM2NmFkNzIyYWYucG5n?x-oss-process=image/format,png)

#### 5.修改配置文件(root用户)

##### (1).修改elasticsearch.yml文件
```shell
vi /opt/module/elasticsearch-5.6.2/conf/elasticsearch.yml
```
修改内容如下：

```shell
# ---------------------------------- Cluster -------------------------------------
cluster.name: my-application
# ------------------------------------ Node --------------------------------------
node.name: JBS
# ----------------------------------- Paths ---------------------------------------
path.data: /opt/module/elasticsearch-5.6.1/data
path.logs: /opt/module/elasticsearch-5.6.1/logs
# ----------------------------------- Memory -----------------------------------
bootstrap.memory_lock: false
bootstrap.system_call_filter: false
# ---------------------------------- Network ------------------------------------
network.host: 192.168.127.121 
# --------------------------------- Discovery ------------------------------------
discovery.zen.ping.unicast.hosts: ["bigdata121"]
```
![elasticsearch.yml](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU2MjI1MDY3MTI1YWU0NDQucG5n?x-oss-process=image/format,png)

说明：
* cluster.name ：如果要配置集群需要两个节点上的 elasticsearch 配置的 cluster.name 相同，都启动可以自动组成集群，这里如果不改 cluster.name 则默认是 cluster.name=my-application。
* nodename 随意取但是集群内的各节点不能相同

##### (2).修改limits.conf文件

```txt
vi /etc/security/limits.conf

//在文件末尾添加如下内容

* soft nofile 655360
* hard nofile 131072
* soft nproc 4096
* hard nproc 4096
```

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM5NjFiNTRlY2ExMzM3ZGMucG5n?x-oss-process=image/format,png)


##### (3).修改limits.d文件

```txt
vi /etc/security/limits.d/20-nproc.conf

//修改内容如下
*    soft    nproc    1024
//改为
*    soft    nproc    4096
```

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA4YmIwMzgyMTU4Njg2MDIucG5n?x-oss-process=image/format,png)

##### (4).修改配置sysctl.conf

```shell
vi /etc/sysctl.conf

//添加内容
vm.max_map_count=655360
```

![sysctl.conf](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThmZmQ4MWI5MjE4NzRkOTIucG5n?x-oss-process=image/format,png)

#### 5.新建linux用户:es不能以root用户启动

```shell
useradd kai

passwd kai
输入密码

cd /opt/module/elasticsearch-5.6.2       //进入的elasticsearch目录

chown -R kai:users *      //修改权限

su kai        //切换到kai用户，启动es
```

![新建用户](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA4OTU5ZTM2Y2I4MmI0OGEucG5n?x-oss-process=image/format,png)

#### 6.重启linux,因为修改linux配置文件后需要重启后才生效
### 二.启动：

```shell
su kai      //切换到kai用户
cd /opt/module/elasticsearch-5.6.2

bin/elasticsearch       //启动
```

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUwOGI0MzI4YzlhMjI2ZDAucG5n?x-oss-process=image/format,png)


注意：can not run elasticsearch as root

### 三.验证：

```shell
curl 'http://192.168.127.121:9200'

curl -XGET '192.168.127.121:9200/_cat/health?v&pretty'
```

![验证](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJmNWI3OTYzZTE4Mzk1ODQucG5n?x-oss-process=image/format,png)


当看到status是green时，证明启动成功

* Green - 一切运行正常(集群功能齐全)
* Yellow - 所有数据是可以获取的，但是一些复制品还没有被分配(集群功能齐全)
* Red - 一些数据因为一些原因获取不到(集群部分功能不可用)

### 四.当启动es，报错：

```shell
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]

[2]: max number of threads [1024] for user [hduser] is too low, increase to at least [4096]

[3]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

[4]: system call filters failed to install; check the logs and fix your configuration or disable system 
```

#### 1.配置linux系统环境：
#### 2.切换到 root 用户，编辑 limits.conf 添加类似如下内容

```shell
vi /etc/security/limits.conf
```

新增内容如下：

```shell
* soft nofile 65536
* hard nofile 131072
* soft nproc 4096
* hard nproc 4096
```
#### 3.进入 limits.d 目录下修改配置文件。

```shell
vi /etc/security/limits.d/90-nproc.conf
```
把 *          soft    nproc     1024 改成4096
#### 5.修改配置 sysctl.conf

```shell
vi /etc/sysctl.conf 
```
添加内容如下：


```shell
vm.max_map_count=655360
```
执行命令：

```shell
sysctl -p
```
#### 6.重新登录kai用户，重新启动es,如果还有报错，则需重启虚拟机
* 查看集群状态命令：

```shell
curl -XGET 'http://192.168.127.121:9200/_cat/health?v&pretty'
```
* 查看所有数据命令：

```shell
curl -XGET 'ip地址:9200/index名称/_search?pretty' -H 'Content-Type: application/json' -d'

{

  "query": { "match_all": {} }

}'
```