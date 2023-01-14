---
title: '[Linux]-Linux安装rz,sz'
date: 2019-08-09 09:49:00
tags: Linux
categories: Linux
---
###### 0.概念：
* rz命令（Receive ZMODEM），使用ZMODEM协议，将本地文件批量上传到远程Linux/Unix服务器，注意不能上传文件夹
* sz fileName：运行该命令会弹出一个选择窗口，选择好保存路径后，将选定的文件发送到本机选定的目录

###### 1.由于在VM安装linux虚拟机时是最小安装，因此很多功能都没有自动安装，此时只能手动安装
###### 2.对于经常使用Linux系统的人员来说，少不了将本地的文件上传到服务器或者从服务器上下载文件到本地，rz / sz命令很方便的帮我们实现了这个功能，但是很多Linux系统初始并没有这两个命令。

###### 3.安装步骤
(1)搜索

```
yum search lrzsz
```

![搜索](/images/1.png)

(2)安装

```
yum install lrzsz.x86_64
```

![安装](/images/2.png)

(3)成功！
