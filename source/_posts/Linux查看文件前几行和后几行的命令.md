---
title: 'Linux查看文件前几行和后几行的命令'
date: 2018-12-06 09:49:00
tags: Linux
categories: Linux
---

#### 1.可以使用head(查看前几行)、tail(查看末尾几行)两个命令
例如：
查看/opt/a.txt的前20行内容：
```shell
head -n 20 /opt/a.txt
```
查看/opt/a.txt的最后5行内容，应该是：
```shell
tail  -n 5 /opt/a.txt
```
#### 2.可以同时查看可以将前10行和后5行的显示信息，并通过输出重定向的方法保存到一个文档

例如：
将内容输出到/opt/test文件中
```shell
head -n 10 /opt/a.txt >>/opt/test

tail  -n 5 /opt/a.txt >>/opt/test
```
查看的话只需要打开test文件即可。
```shell
cat /opt/test
```