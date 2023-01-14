---
title: 'redis安装报错:jemalloc'
date: 2018-12-12 12:00:00
tags: 
- Redis
- 填坑
- CentOS
categories: Redis
---

#### 1.在安装redis时，make编译时报错：jemalloc/jemalloc.h：没有那个文件或目录
![报错](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRiNWQzMWM0ODc4NWNlNTkucG5n?x-oss-process=image/format,png)

#### 2.解决方法：

执行命令

```shell
make MALLOC=libc
```

![执行命令](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM1Njg0MTYyY2VjNGY0NWUucG5n?x-oss-process=image/format,png)

#### 3.问题解决