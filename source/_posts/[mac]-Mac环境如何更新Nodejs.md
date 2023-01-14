---
title: '[mac]-Mac环境如何更新Nodejs'
date: 2019-05-30 15:00:00
tags: 
- Mac
- NodeJS
categories: Mac
---

#### 1.前提条件
* 安装npm

#### 2.解决办法:

##### (1)使用npm安装n模块,n模块是专门用来管理nodejs版本的

```shell
sudo npm install -g n
```

##### (2)升级nodejs

* 升级到最新版：

```shell
sudo n latest
```

* 升级到稳定版

```shell
sudo n stable
```
建议还是升级到稳定版
