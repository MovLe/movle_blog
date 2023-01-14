---
title: 'MapReduce之ReduceTask工作机制'
date: 2019-01-01 17:55:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
#### 1.设置ReduceTask并行度（个数）
&nbsp;&nbsp;&nbsp;&nbsp;reducetask的并行度同样影响整个job的执行并发度和执行效率，但与maptask的并发数由切片数决定不同，Reducetask数量的决定是可以直接手动设置：

```java
//默认值是1，手动设置为4
job.setNumReduceTasks(4);
```
#### 2.注意
(1)reducetask=0 ，表示没有reduce阶段，输出文件个数和map个数一致。

(2)reducetask默认值就是1，所以输出文件个数为一个。

(3)如果数据分布不均匀，就有可能在reduce阶段产生数据倾斜

(4)reducetask数量并不是任意设置，还要考虑业务逻辑需求，有些情况下，需要计算全局汇总结果，就只能有1个reducetask。

(5)具体多少个reducetask，需要根据集群性能而定。

(6)如果分区数不是1，但是reducetask为1，是否执行分区过程。答案是：不执行分区过程。因为在maptask的源码中，执行分区的前提是先判断reduceNum个数是否大于1。不大于1肯定不执行。

#### 3.实验：测试reducetask多少合适。
(1)实验环境：1个master节点，16个slave节点：CPU:8GHZ，内存: 2G

(2)实验结论：

表1 改变reduce task （数据量为1GB）

|maptask=16|||||||||||
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|Reduce task|1|5|10|15|16|20|25|30|45|60|
|总时间|892|146|110|92|88|100|128|101|145|104|

#### 4.ReduceTask工作机制
![ReduceTask工作机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTliYzNmZjE5MmJmYjczYTEucG5n?x-oss-process=image/format,png)


(1)Copy阶段：ReduceTask从各个MapTask上远程拷贝一片数据，并针对某一片数据，如果其大小超过一定阈值，则写到磁盘上，否则直接放到内存中。

(2)Merge阶段：在远程拷贝数据的同时，ReduceTask启动了两个后台线程对内存和磁盘上的文件进行合并，以防止内存使用过多或磁盘上文件过多。

(3)Sort阶段：按照MapReduce语义，用户编写reduce()函数输入数据是按key进行聚集的一组数据。为了将key相同的数据聚在一起，Hadoop采用了基于排序的策略。由于各个MapTask已经实现对自己的处理结果进行了局部排序，因此，ReduceTask只需对所有数据进行一次归并排序即可。

(4)Reduce阶段：reduce()函数将计算结果写到HDFS上。