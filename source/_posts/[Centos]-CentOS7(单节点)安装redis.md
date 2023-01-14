---
title: '[Centos]-CentOS7(单节点)安装Redis'
date: 2018-12-07 12:00:00
tags: 
- Linux
- Redis
- 安装部署
- CentOS
categories: Redis
---

#### 1.下载安装包
#### 2.上传到linux
#### 3.解压：
```shell
cd /usr/local/tars

tar -zxvf redis-4.0.2.tar.gz -C /usr/local
```
#### 4.编译：
```shell
cd /usr/local/redis-4.0.2

make
```

![编译](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY2ZjVlZTJmMmY1YmUxMGEucG5n?x-oss-process=image/format,png)

#### 5.安装：
```shell
cd /usr/local/redis-4.0.2

 make PREFIX=/usr/local/redis install             //安装
```
![安装](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJlMDJlMDkyMjI1MDdhMjYucG5n?x-oss-process=image/format,png)

#### 6.拷贝配置文件：
```shell
cd /usr/local

cp ./redis-4.0.2/redis.conf ./redis
```
![拷贝](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRhMDNhM2ZiNmFiMTQwNTMucG5n?x-oss-process=image/format,png)

#### 7.修改配置文件
```shell
cd /usr/local/redis

vi redis.conf
```

修改内容如下：

```shell
daemonize yes        //是否后台启动

logfile "/usr/local/redis/redis.log"   //日志文件地址
```
#### 8.启动：

```shell
cd /usr/local/redis

./bin/redis-server ./redis.conf 
```

#### 9.查看：
```shell
ps -ef | grep redis
```

#### 10.启动客户端：
```shell
cd /usr/local/redis

./bin/redis-cli
```
![查看](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI0MDU4MmU2MzJkZjQyYTYucG5n?x-oss-process=image/format,png)

