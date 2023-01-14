---
title: 'Spark-安装配置：伪分布式安装和全分布式安装'
date: 2020-03-10 12:00:00
tags: 
- Spark
- 安装部署
categories: Spark
---

#### 0.准备工作：安装JDK，配置主机名，免密登陆

### 一.伪分布模式搭建：
#### 1.解压
```shell
tar -zxvf spark-2.1.0-bin-hadoop2.7.tgz -C /opt/module
```
#### 2.修改配置文件：
##### (1)修改spark-env.sh

重命名(拷贝)：
```shell
cd /opt/module/spark-2.1.0-bin-hadoop2.7/conf

cp spark-env.sh.template spark-env.sh
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZlOGE4NzNjMDNjMDc1YzEucG5n?x-oss-process=image/format,png)

修改：
```shell
vi spark-env.sh
```
修改内容：
```shell
export JAVA_HOME=/opt/module/jdk1.8.0_144 
export SPARK_MASTER_HOST=hadoop1
export SPARK_MASTER_PORT=7077

//下面的可以不写，默认
export SPARK_WORKER_CORES=1
export SPARK_WORKER_MEMORY=1024m
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJmM2JjMzE2ZDQxYzJlZGEucG5n?x-oss-process=image/format,png)

##### (2)修改slaves

```shell
cp slaves.template slaves 
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk2ZjdhMTgyMTJjN2ZhZTgucG5n?x-oss-process=image/format,png)

修改：
```shell
cd /opt/module/spark-2.1.0-bin-hadoop2.7/conf

vi slaves
```
修改内容：
```shell
hadoop1
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRhOWExYjAxNWVlMjA0YjEucG5n?x-oss-process=image/format,png)


### 二.全分布模式安装：

#### 1.解压
```shell
tar -zxvf spark-2.1.0-bin-hadoop2.7.tgz -C /opt/module
```
#### 2.修改配置文件：
##### (1)修改spark-env.sh

重命名(拷贝)：
```shell
cd /opt/module/spark-2.1.0-bin-hadoop2.7/conf

cp spark-env.sh.template spark-env.sh
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZlOGE4NzNjMDNjMDc1YzEucG5n?x-oss-process=image/format,png)

修改：
```shell
vi spark-env.sh
```
修改内容：
```shell
export JAVA_HOME=/opt/module/jdk1.8.0_144 
export SPARK_MASTER_HOST=hadoop1
export SPARK_MASTER_PORT=7077

//下面的可以不写，默认
export SPARK_WORKER_CORES=1
export SPARK_WORKER_MEMORY=1024m
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJmM2JjMzE2ZDQxYzJlZGEucG5n?x-oss-process=image/format,png)

##### (2)修改slaves

```shell
cp slaves.template slaves 
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk2ZjdhMTgyMTJjN2ZhZTgucG5n?x-oss-process=image/format,png)

修改：
```shell
vi slaves
```
修改内容：

```shell
hadoop2
hadoop3
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEwYmM5NzYwODM3YWEyNDEucG5n?x-oss-process=image/format,png)

#### 3.发送到另外两台服务器：
```shell
scp -r spark-2.1.0-bin-hadoop2.7/ root@hadoop2:/opt/module/

scp -r spark-2.1.0-bin-hadoop2.7/ root@hadoop3:/opt/module/
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUzNzFjZTY5MTY0NDk2MzAucG5n?x-oss-process=image/format,png)


#### 4.启动：
hadoop1:
```shell
cd /opt/module/spark-2.1.0-bin-hadoop2.7

sbin/start-all.sh
```

![hadoop1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE3YzM4YWMyNmMzZjBlNjcucG5n?x-oss-process=image/format,png)
![hadoop2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI3ZGU3Yjc3YjZlZWUwZWYucG5n?x-oss-process=image/format,png)
![hadoop3](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFlMDM0OTA1NDc5MTEzOTIucG5n?x-oss-process=image/format,png)

#### 5.验证：

浏览器输入hadoop1IP地址：192.168.1.121:8080

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdiMjRiZGZmYTFhYzNhOTUucG5n?x-oss-process=image/format,png)