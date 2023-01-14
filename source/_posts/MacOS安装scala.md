---
title: 'MacOS安装scala'
date: 2019-03-05 15:50:00
tags: 
- Scala
- 安装部署
categories: Scala
---

#### 1.下载安装包：
下载地址：http://www.scala-lang.org/download/
![下载](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRhNTA5NmU5ZWE1ZGFkNzAucG5n?x-oss-process=image/format,png)

#### 2.将下载好的scala压缩包解压到目录：
```shell
tar -zxvf scala-2.13.1.tgz -C /Users/macbook/Documents/scala
```

![解压](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYwM2I5YTgyMjFiMDRiMGMucG5n?x-oss-process=image/format,png)

#### 3.配置环境变量：

```shell
vi ~/.bash_profile
```


添加内容：
```shell
export SCALA_HOME=/Users/macbook/Documents/scala/scala-2.13.1

export PATH=$PATH:$SCALA_HOME/bin
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRjMWRjOGNjNmVlY2IxZDkucG5n?x-oss-process=image/format,png)

使环境变量生效
```shell
source ~/.bash_profile
```
![ 使环境变量生效](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBlMjY5N2I2MGNhZGI0YmUucG5n?x-oss-process=image/format,png)

#### 4.验证：

```shell
scala
```
![验证](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFlYzQyNGM2NmFlOTM3YzMucG5n?x-oss-process=image/format,png)
