---
title: '[mac]-Mac上查看端口占用和释放端口的教程'
date: 2023-01-14 20:56:00
tags: 
- Mac
categories: Mac
published: true
---

### 1.查看端口被占用的程序
```shell
sudo lsof -i tcp:port 

sudo lsof -i tcp:4000 #示例 查看端口为4000的程序
```

### 2.根据进程的PID，将进程结束
```shell
sudo kill -9 PID

sudo kill -9 35927 # 示例 结束端口号为35927的进程
```