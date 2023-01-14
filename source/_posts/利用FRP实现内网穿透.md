---
title: '利用FRP实现内网穿透'
date: 2020-01-09 11:00:00
tags: 
- FRP
- 内网穿透
- 项目
categories: 有用项目
---

>情况：
自己有两台电脑，一台16G内存的Dell，和一台8G内存的MacBook Pro，因为自己学的是大数据开发，所以需要搭建大数据集群，因此在MacBook Pro开发的话内存太小了，既要用虚拟机搭建大数据集群，又要用IDEA开发的话，内存是肯定不够的，所以之前是在Dell上面创建虚拟机，搭建大数据集群，然后再在MacBook Pro上开发，因为虚拟机使用的桥接法上网，Macbook Pro和Dell又在同一个局域网里，所以还可以，但是如果需要离开，区别的地方的话，自己就需要带两台电脑了，太沉了，所以就打算用内网穿透，让MacBook 在外网，也可以访问Dell里的虚拟机

### 一.介绍
#### 1.FRP介绍：

frp 是一个可用于内网穿透的高性能的反向代理应用，支持 tcp, udp 协议，为 http 和 https 应用协议提供了额外的能力，且尝试性支持了点对点穿透。

#### 2.FRP项目地址：
https://github.com/fatedier/frp

#### 3.环境：
##### (1)带外网IP的云主机-腾讯云：49.xxx.xxx.xx
##### (2)内网的CentOS7(Windows搭建的虚拟机)：192.168.0.161

### 二.搭建过程
##### 1.在云主机上搭建FRP服务端：
##### (1)下载对应的FTP包：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNmYTRmNzA3N2NjNzYwMGEucG5n?x-oss-process=image/format,png)


![拷贝链接](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEzNjM1M2NlOGQ1YWUzN2QucG5n?x-oss-process=image/format,png)


下载
```shell
cd /mnt

wget https://github.com/fatedier/frp/releases/download/v0.31.1/frp_0.31.1_linux_386.tar.gz
```
##### (2).解压：
```shell
tar -zxvf frp_0.31.1_linux_386.tar.gz
```
##### (3).重命名：

```shell
mv frp_0.31.1_linux_386 frp 
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM4MzZiMjI3NmVjNzA4YzMucG5n?x-oss-process=image/format,png)

##### (4).修改配置文件(服务端)
```shell
cd ftp

vi frps.ini 
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUzOGFkNTliY2I3YjMwYWIucG5n?x-oss-process=image/format,png)

修改内容：
```shell
#frps.ini
[common]
bind_port = 7000 
```
![frpc.ini](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ4OTE2MzNiMDRkOTU4MmEucG5n?x-oss-process=image/format,png)

##### (5)关闭防火墙，或者打开6000端口：

##### (6)运行

```shell
./frps -c ./frps.ini
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY2NGNiYzVjNmY1M2JhOGYucG5n?x-oss-process=image/format,png)

#### 2.在内网CentOS7虚拟机配置FRP客户端

##### (1)下载：
```shell
cd /mnt

wget https://github.com/fatedier/frp/releases/download/v0.31.1/frp_0.31.1_linux_386.tar.gz
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM2ODZiYjEyMGIyNjIyYmQucG5n?x-oss-process=image/format,png)

##### (2)解压，重命名：
```shell
tar -zxvf frp_0.31.1_linux_386.tar.gz

mv frp_0.31.1_linux_386 frp
```
##### (3)修改客户端配置文件frpc.ini

```shell
cd /mnt/frp

vi frpc.ini
```
修改内容：
```shell
# frpc.ini
[common]
server_addr = 49.x.x.x
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
```
* server_addr填写的是云服务器IP
* remote_port 填写的是远程访问的端口号

##### (4)启动frpc
```shell
./frpc -c ./frpc.ini
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg0NmQ1ZmI2YWJjZTA2Y2EucG5n?x-oss-process=image/format,png)



#### 3.测试：
![1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY5YzA0ZjZkYmZmMDczY2EucG5n?x-oss-process=image/format,png)
![2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg3MDA5OGViZWZlMTdlOTYucG5n?x-oss-process=image/format,png)
