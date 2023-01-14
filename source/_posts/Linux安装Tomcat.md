---
title: 'Linux安装Tomcat'
date: 2019-05-06 09:49:00
tags: 
- Linux
- Tomcat
- 安装部署
categories: Tomcat
---

### 一.安装Tomcat

#### 1.解压：
```shell
tar -zxvf apache-tomcat-8.5.23.tar.gz -C /opt/module/
```
![解压](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThmYmI4YTkwZmUzNWE1ZDEucG5n?x-oss-process=image/format,png)

#### 2.启动：

```shell
bin/startup.sh
```

![启动](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAxMDFhNjJiNDk2MTg4MTgucG5n?x-oss-process=image/format,png)

#### 3.验证：

```shell
192.168.1.125:8080
```
![验证](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBlY2ZlYzM0YTM4NGUxMTAucG5n?x-oss-process=image/format,png)

