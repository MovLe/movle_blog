---
title: '[Centos]-CentOS7 安装NodeJS-使用二进制文件安装'
date: 2020-02-15 14:37:00
tags: 
- NodeJS
- 安装部署
- CentOS
categories: Linux
---

###### 1.下载安装包

[下载地址](https://nodejs.org/en/download/)

###### 2.解压文件
```
tar xvf node-v12.19.0-linux-x64.tar.xz -C /opt/module/
```

###### 3.创建软连接
```
ln -s /opt/module/node-v12.19.0-linux-x64/bin/node /usr/local/bin/node

ln -s /opt/module/node-v12.19.0-linux-x64/bin/npm /usr/local/bin/npm
```
###### 4.查看版本
```
node -v

npm -v
```