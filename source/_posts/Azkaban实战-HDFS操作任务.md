---
title: 'Azkaban实战-HDFS操作任务'
date: 2019-06-02 16:00:00
tags: 
- Azkaban
- Azkaban实战
- HDFS
categories: Azkaban
---

#### 1.创建job描述文件
```shell
vi hdfs.job
```
添加内容：
```shell
#hdfs job
type=command
command=/opt/module/hadoop-2.8.4/bin/hadoop fs -mkdir /azkaban
```
#### 2.将job资源文件打包成zip文件
```shell
zip fs.zip fs.job 
```

#### 3.通过azkaban的web管理平台创建project并上传job压缩包

![创建](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg5NjA5NGNmYTFjOGYwOTMucG5n?x-oss-process=image/format,png)

![上传](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU5MDY4OGY2MDY1ZWJhYzgucG5n?x-oss-process=image/format,png)

#### 4.执行：

![执行](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM0NTBkOWI3Y2RlMzE4YzAucG5n?x-oss-process=image/format,png)

![执行成功](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA2YjIyMDA5NTdkMDZjYjAucG5n?x-oss-process=image/format,png)

#### 5.查看结果
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTExZWExZTA2MDUyNmYyNzgucG5n?x-oss-process=image/format,png)