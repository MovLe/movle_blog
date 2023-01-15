---
title: '[Centos]-CentOS7免密登陆'
date: 2023-01-15 08:45:00
tags: 
- Linux
- 安装部署
- CentOS
categories: Linux
---

### 1.免密登录原理
![免密登录原理](免密登录原理.png)
### 2.生成公钥和私钥

```shell
ssh-keygen -t rsa # 然后三次回车，生成公钥和私钥
```

### 3.将公钥拷贝到要免密登录的目标机器上
```shell
ssh-copy-id bigdata101
ssh-copy-id bigdata102
ssh-copy-id bigdata103
```

