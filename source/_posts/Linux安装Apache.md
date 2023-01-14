---
title: 'Linux安装Apache'
date: 2019-05-06 10:00:00
tags: 
- Linux
- Apache
- 安装部署
categories: Apache
---

### 一.安装Apache
#### 1.安装
```shell
yum install httpd
```

#### 2.启动：
```shell
service httpd start
```
![启动](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc5NjdkMTZhM2M1ZGE3YTIucG5n?x-oss-process=image/format,png)

#### 3.验证：
浏览器输入：
```shell
192.168.1.125:80
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY1YzY2ODE2MmM3ZjViYjMucG5n?x-oss-process=image/format,png)
