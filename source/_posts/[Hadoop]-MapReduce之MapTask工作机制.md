---
title: '[Hadoop]-MapReduce之MapTask工作机制'
date: 2019-01-01 17:53:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
#### 1.并行度决定机制

##### (1).问题引出

&nbsp;&nbsp;&nbsp;&nbsp;maptask的并行度决定map阶段的任务处理并发度，进而影响到整个job的处理速度。那么，mapTask并行任务是否越多越好呢？

##### (2).MapTask并行度决定机制

&nbsp;&nbsp;&nbsp;&nbsp;一个job的map阶段MapTask并行度（个数），由客户端提交job时的切片个数决定。

![数据切片及Maptask并行度决定机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkyOTQ2ODVmZjkyMTc5MGYucG5n?x-oss-process=image/format,png)

#### 2.MapTask工作机制
![MapTask工作机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgzNjIxZjZhOWZiMjc0ZjAucG5n?x-oss-process=image/format,png)

(1)Read阶段：Map Task通过用户编写的RecordReader，从输入InputSplit中解析出一个个key/value。

(2)Map阶段：该节点主要是将解析出的key/value交给用户编写map()函数处理，并产生一系列新的key/value。

(3)Collect收集阶段：在用户编写map()函数中，当数据处理完成后，一般会调用OutputCollector.collect()输出结果。在该函数内部，它会将生成的key/value分区（调用Partitioner），并写入一个环形内存缓冲区中。

(4)Spill阶段：即“溢写”，当环形缓冲区满后，MapReduce会将数据写到本地磁盘上，生成一个临时文件。需要注意的是，将数据写入本地磁盘之前，先要对数据进行一次本地排序，并在必要时对数据进行合并、压缩等操作。

&nbsp;&nbsp;&nbsp;&nbsp;溢写阶段详情：
* 步骤1：利用快速排序算法对缓存区内的数据进行排序，排序方式是，先按照分区编号partition进行排序，然后按照key进行排序。这样，经过排序后，数据以分区为单位聚集在一起，且同一分区内所有数据按照key有序。

* 步骤2：按照分区编号由小到大依次将每个分区中的数据写入任务工作目录下的临时文件output/spillN.out（N表示当前溢写次数）中。如果用户设置了Combiner，则写入文件之前，对每个分区中的数据进行一次聚集操作。

* 步骤3：将分区数据的元信息写到内存索引数据结构SpillRecord中，其中每个分区的元信息包括在临时文件中的偏移量、压缩前数据大小和压缩后数据大小。如果当前内存索引大小超过1MB，则将内存索引写到文件output/spillN.out.index中。

(5)Combine阶段：当所有数据处理完成后，MapTask对所有临时文件进行一次合并，以确保最终只会生成一个数据文件。

&nbsp;&nbsp;&nbsp;&nbsp;当所有数据处理完后，MapTask会将所有临时文件合并成一个大文件，并保存到文件output/file.out中，同时生成相应的索引文件output/file.out.index。
&nbsp;&nbsp;&nbsp;&nbsp;在进行文件合并过程中，MapTask以分区为单位进行合并。对于某个分区，它将采用多轮递归合并的方式。每轮合并io.sort.factor（默认100）个文件，并将产生的文件重新加入待合并列表中，对文件排序后，重复以上过程，直到最终得到一个大文件。
&nbsp;&nbsp;&nbsp;&nbsp;让每个MapTask最终只生成一个数据文件，可避免同时打开大量文件和同时读取大量小文件产生的随机读取带来的开销。