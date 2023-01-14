---
title: '[Hadoop]-MapReduce工作流程'
date: 2019-01-01 17:50:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
#### 1.流程示意图：
![MapReduce工作流程一](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY5MWMxZjRiM2E0YWIyMjUucG5n?x-oss-process=image/format,png)
![Mapreduce工作流程二](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJmYTZhNzY5OTBjZmUyODIucG5n?x-oss-process=image/format,png)

#### 2.流程详解
&nbsp;&nbsp;&nbsp;&nbsp;上面的流程是整个mapreduce最全工作流程，但是shuffle过程只是从第7步开始到第16步结束，具体shuffle过程详解，如下：
* (1)maptask收集我们的map()方法输出的kv对，放到内存缓冲区中

* (2)从内存缓冲区不断溢出本地磁盘文件，可能会溢出多个文件

* (3)多个溢出文件会被合并成大的溢出文件

* (4)在溢出过程中，及合并的过程中，都要调用partitioner进行分区和针对key进行排序

* (5)reducetask根据自己的分区号，去各个maptask机器上取相应的结果分区数据

* (6)reducetask会取到同一个分区的来自不同maptask的结果文件，reducetask会将这些文件再进行合并（归并排序）

* (7)合并成大文件后，shuffle的过程也就结束了，后面进入reducetask的逻辑运算过程（从文件中取出一个一个的键值对group，调用用户自定义的reduce()方法）

#### 3.注意
&nbsp;&nbsp;&nbsp;&nbsp;Shuffle中的缓冲区大小会影响到mapreduce程序的执行效率，原则上说，缓冲区越大，磁盘io的次数越少，执行速度就越快。
&nbsp;&nbsp;&nbsp;&nbsp;缓冲区的大小可以通过参数调整，参数：io.sort.mb  默认100M。