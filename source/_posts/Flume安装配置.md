---
title: 'Flume安装配置'
date: 2019-07-03 12:00:00
tags: 
- Flume
- 安装部署
categories: Flume
---

#### 1.下载压缩包
#### 2.上传到linux:/opt/software

#### 3.解压

```shell
cd /opt/software

tar -zxvf apache-flume-1.6.0-bin.tar.gz -C /opt/moudule
```
#### 4.重命名

```shell
cd /opt/module/flume/conf

mv flume-env.sh.template flume-env.sh
```
#### 5.修改配置

```shell
vi flume-env.sh
```
修改内容如下：

```shell
export JAVA_HOME=/opt/module/jdk1.8.0_144
```
![flume-env.sh](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTExMGIxMDVjM2Y3OTliOWYucG5n?x-oss-process=image/format,png)