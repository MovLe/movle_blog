---
title: 'CentOS7安装redis报错'
date: 2018-12-07 12:00:00
tags: 
- Linux
- Redis
- CentOS
- 填坑
categories: Redis
---

#### 1.错误信息：
```
/bin/sh: cc: 未找到命令
```
#### 2.原因是CentOS7虚拟机系统中缺少gcc，因此只要安装好gcc即可

#### 3.解决办法：
```shell
yum -y install gcc automake autoconf libtool make
```
#### 4.完毕