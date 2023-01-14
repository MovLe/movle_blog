---
title: '[Hadoop]-MapRedude之Join，数据清洗，计数器应用'
date: 2019-01-01 17:57:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
### 一. Join多种应用
#### 1.Reduce join
##### (1).原理：

&nbsp;&nbsp;&nbsp;&nbsp;Map端的主要工作：为来自不同表(文件)的key/value对打标签以区别不同来源的记录。然后用连接字段作为key，其余部分和新加的标志作为value，最后进行输出。
&nbsp;&nbsp;&nbsp;&nbsp;Reduce端的主要工作：在reduce端以连接字段作为key的分组已经完成，我们只需要在每一个分组当中将那些来源于不同文件的记录(在map阶段已经打标志)分开，最后进行合并就ok了。
##### (2).该方法的缺点

这种方式的缺点很明显就是会造成map和reduce端也就是shuffle阶段出现大量的数据传输，效率很低。
##### (3).案例实操

#### 2.Map join（Distributedcache分布式缓存）
##### (1).使用场景：一张表十分小、一张表很大。

##### (2).解决方案
在map端缓存多张表，提前处理业务逻辑，这样增加map端业务，减少reduce端数据的压力，尽可能的减少数据倾斜。

###### (3).具体办法：采用distributedcache

(a)在mapper的setup阶段，将文件读取到缓存集合中。
(b)在驱动函数中加载缓存。
```java
job.addCacheFile(new URI("file:/e:/mapjoincache/pd.txt"));// 缓存普通文件到task运行节点
```
##### (4).实操案例：

### 二. 数据清洗(ETL)
#### 1.概述
&nbsp;&nbsp;&nbsp;&nbsp;在运行核心业务Mapreduce程序之前，往往要先对数据进行清洗，清理掉不符合用户要求的数据。清理的过程往往只需要运行mapper程序，不需要运行reduce程序。
#### 2.实操案例

### 三.计数器应用
&nbsp;&nbsp;&nbsp;&nbsp;Hadoop为每个作业维护若干内置计数器，以描述多项指标。例如，某些计数器记录已处理的字节数和记录数，使用户可监控已处理的输入数据量和已产生的输出数据量。

#### 1.API
(1)采用枚举的方式统计计数
```java
enum MyCounter{MALFORORMED,NORMAL}
//对枚举定义的自定义计数器加1
context.getCounter(MyCounter.MALFORORMED).increment(1);
```
(2)采用计数器组、计数器名称的方式统计
```java
context.getCounter("counterGroup", "countera").increment(1);
//组名和计数器名称随便起，但最好有意义。
```
(3)计数结果在程序运行后的控制台上查看。
#### 2.案例实操
