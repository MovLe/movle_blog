---
title: 'Azkaban实战-单一job案例'
date: 2019-06-02 13:00:00
tags: 
- Azkaban
- Azkaban实战
categories: Azkaban
---

Azkaba内置的任务类型支持command、java

#### 1.创建job描述文件(可以在linux里写，也可以在windows或者mac中写完在打包上传)
##### (1)方法一：在linux里创建并压缩
创建：
```shell
vi first.job
```
添加内容：
```shell
#first.job
type=command
command=echo 'this is my first job'
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlhZGE0MjZiMjYxMjZmOWEucG5n?x-oss-process=image/format,png)

#### 2. linux中:将job资源文件打包成zip文件

```shell
zip first.zip first.job
```
![在linux中创建并压缩](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY4NTYwYzAxNzVkMWY4ZDIucG5n?x-oss-process=image/format,png)


注意：

目前，Azkaban上传的工作流文件只支持xxx.zip文件。zip应包含xxx.job运行作业所需的文件和任何文件（文件名后缀必须以.job结尾，否则无法识别）。作业名称在项目中必须是唯一的。

#### 3.通过azkaban的web管理平台创建project并上传job的zip包

首先创建project

![创建project](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE2ZmM2OTJjZWIxNzc2ZmEucG5n?x-oss-process=image/format,png)

上传zip包
![上传zip包](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE4MDg5YmI0MWRlY2VmNTAucG5n?x-oss-process=image/format,png)
![选择压缩包](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE4ZmRlZDNkNWIyNDlmNTQucG5n?x-oss-process=image/format,png)
#### 4.启动执行该job
![启动执行](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE3MDJlZGYxOWRiNzZhYTAucG5n?x-oss-process=image/format,png)
点击执行工作流
![点击执行工作流](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThhMWEyOGM2MGFmN2ZhZWEucG5n?x-oss-process=image/format,png)
点击继续
![点击继续](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA3Y2M2NWI3ZTE5Y2ZkOWMucG5n?x-oss-process=image/format,png)

#### 5.Job执行成功
![Job执行成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThkNmY0NWQ2MzhlMTgyN2MucG5n?x-oss-process=image/format,png)
#### 6.点击查看job日志
![点击查看job日志](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI1ODg1N2ZhNmU0NmZmYWIucG5n?x-oss-process=image/format,png)

