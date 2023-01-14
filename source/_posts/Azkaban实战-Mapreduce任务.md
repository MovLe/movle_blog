---
title: 'Azkaban实战-MapReduce任务'
date: 2019-06-02 17:00:00
tags: 
- Azkaban
- Azkaban实战
- MapReduce
categories: Azkaban
---

mapreduce任务依然可以使用azkaban进行调度

#### 1.创建job描述文件，及mr程序jar包
```shell
vi mapreduce.job
```
添加内容：
```shell
#mapreduce job
type=command
command=/opt/module/hadoop-2.8.4/bin/hadoop jar /opt/module/hadoop-2.8.4/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.4.jar wordcount /wordcount/input /wordcount/output
```

#### 2.将所有job资源文件打到一个zip包中
```shell
zip mapreduce.zip mapreduce.job 
```
  adding: mapreduce.job (deflated 43%)

#### 3.在azkaban的web管理界面创建工程并上传zip包

#### 4.启动job

#### 5.查看结果
