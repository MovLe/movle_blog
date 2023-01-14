---
title: 'Sqoop简介与原理'
date: 2019-05-09 11:00:00
tags: Sqoop
categories: Sqoop
---
### 一.Sqoop简介

Apache Sqoop(TM)是一种旨在有效地在Apache Hadoop和诸如关系数据库等结构化数据存储之间传输大量数据的工具。

Sqoop于2012年3月孵化出来，现在是一个顶级的Apache项目。

请注意，1.99.7与1.4.6不兼容，且没有特征不完整，它并不打算用于生产部署。

### 二.Sqoop原理

将导入或导出命令翻译成mapreduce程序来实现。

在翻译出的mapreduce中主要是对inputformat和outputformat进行定制。

### 三.架构：
#### 1.区别
(1)flume数据采集 采集日志数据
(2)sqoop数据迁移 hdfs->mysql
(3)azkaban任务调度 flume->hdfs->shell->hive->sql->BI

* sqoop数据迁移=mapreduce 处理离线数据 整个过程就是数据导入处理导出过程 直接使用map

#### 2.sqoop作用：
* sqoop作用:简化开发
