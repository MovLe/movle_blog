---
title: 'Mac报错Error listen EADDRINUSE address already in use 4000'
date: 2019-05-30 15:00:00
tags: 
- Mac
- 填坑
categories: Mac
---

#### 1.错误信息：
```shell
Error: listen EADDRINUSE: address already in use :::4000
```
#### 2.问题描述：
在hexo博客本地启动的时候，之前启动过，所以4000端口被占用

#### 3.解决方法：
* 在控制台输入

```shell
sudo lsof -i:端口号
```
查看被占用进程的pid，
* 再输入

```shell
sudo kill -9 pid
```
即可杀死进程
