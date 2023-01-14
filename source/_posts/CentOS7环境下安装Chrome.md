---
title: 'CentOS7环境下安装Chrome'
date: 2019-12-06 10:00:00
tags: 
- Linux
- Chrome
- 安装部署
- CentOS
categories: Linux
---

#### 1.修改yum源
在/etc/yum.repos.d/目录下新建文件google-chrome.repo，

```shell
cd /etc/yum.repos.d

vi google-chrome.repo
```
向其中添加如下内容:

```shell
[google-chrome]
name=google-chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
```

#### 2.安装
```shell
yum install google-chrome-stable --nogpgcheck
```
![1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc4OGVkNTYzYzUyNDUwNDEucG5n?x-oss-process=image/format,png)

![2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjYTUwZmU3ZDc0ZThhOWQucG5n?x-oss-process=image/format,png)
