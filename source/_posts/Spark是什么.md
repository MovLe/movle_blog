---
title: 'Spark是什么'
date: 2020-03-10 11:50:00
tags: Spark
categories: Spark
---

#### 1.什么是Spark？
&nbsp;&nbsp;&nbsp;&nbsp;Spark是一种快速、通用、可扩展的大数据分析引擎，2009年诞生于加州大学伯克利分校AMPLab，2010年开源，2013年6月成为Apache孵化项目，2014年2月成为Apache顶级项目。目前，Spark生态系统已经发展成为一个包含多个子项目的集合，其中包含SparkSQL、Spark Streaming、GraphX、MLlib等子项目，Spark是基于内存计算的大数据并行计算框架。Spark基于内存计算，提高了在大数据环境下数据处理的实时性，同时保证了高容错性和高可伸缩性，允许用户将Spark部署在大量廉价硬件之上，形成集群。Spark得到了众多大数据公司的支持，这些公司包括Hortonworks、IBM、Intel、Cloudera、MapR、Pivotal、百度、阿里、腾讯、京东、携程、优酷土豆。当前百度的Spark已应用于凤巢、大搜索、直达号、百度大数据等业务；阿里利用GraphX构建了大规模的图计算和图挖掘系统，实现了很多生产系统的推荐算法；腾讯Spark集群达到8000台的规模，是当前已知的世界上最大的Spark集群。
#### 2.为什么要使用Spark？
##### (1)Hadoop的MapReduce计算模型存在的问题：
&nbsp;&nbsp;&nbsp;&nbsp;MapReduce的核心是Shuffle(洗牌)。在整个Shuffle的过程中，至少会产生6次的I/O。下图是MapReduce的Shuffle的过程。
&nbsp;&nbsp;&nbsp;&nbsp;**中间结果输出**：基于MapReduce的计算引擎通常会将中间结果输出到磁盘上，进行存储和容错。另外，当一些查询(如：Hive)翻译到MapReduce任务时，往往会产生多个Stage(阶段)，而这些串联的Stage又依赖于底层文件系统(如HDFS)来存储每一个Stage的输出结果，而I/O的效率往往较低，从而影响了MapReduce的运行速度。

![MapReduce的Shuffle](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE1MDc1NWEyZTdkYjJmYWQucG5n?x-oss-process=image/format,png)


##### (2)Spark的最大特点：基于内存
&nbsp;&nbsp;&nbsp;&nbsp;Spark是MapReduce的替代方案，而且兼容HDFS、Hive，可融入Hadoop的生态系统，以弥补MapReduce的不足。

#### 3.Spark的特点：快、易用、通用、兼容性
##### (1)快
&nbsp;&nbsp;&nbsp;&nbsp;与Hadoop的MapReduce相比，Spark基于内存的运算速度要快100倍以上，即使，Spark基于硬盘的运算也要快10倍。Spark实现了高效的DAG执行引擎，从而可以通过内存来高效处理数据流。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNjMWNiNWNjMDA1ZDkzYmEucG5n?x-oss-process=image/format,png)

##### (2)易用
&nbsp;&nbsp;&nbsp;&nbsp;Spark支持Java、Python和Scala的API，还支持超过80种高级算法，使用户可以快速构建不同的应用。而且Spark支持交互式的Python和Scala的shell，可以非常方便地在这些shell中使用Spark集群来验证解决问题的方法。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA4OGM1ZmU1NTFjZmNlZDkucG5n?x-oss-process=image/format,png)


##### (3)通用
&nbsp;&nbsp;&nbsp;&nbsp;Spark提供了统一的解决方案。Spark可以用于批处理、交互式查询(Spark SQL)、实时流处理(Spark Streaming)、机器学习(Spark MLlib)和图计算(GraphX)。这些不同类型的处理都可以在同一个应用中无缝使用。Spark统一的解决方案非常具有吸引力，毕竟任何公司都想用统一的平台去处理遇到的问题，减少开发和维护的人力成本和部署平台的物力成本。
&nbsp;&nbsp;&nbsp;&nbsp;另外Spark还可以很好的融入Hadoop的体系结构中可以直接操作HDFS，并提供Hive on Spark、Pig on Spark的框架集成Hadoop。

##### (4)兼容性
&nbsp;&nbsp;&nbsp;&nbsp;Spark可以非常方便地与其他的开源产品进行融合。比如，Spark可以使用Hadoop的YARN和Apache Mesos作为它的资源管理和调度器，器，并且可以处理所有Hadoop支持的数据，包括HDFS、HBase和Cassandra等。这对于已经部署Hadoop集群的用户特别重要，因为不需要做任何数据迁移就可以使用Spark的强大处理能力。Spark也可以不依赖于第三方的资源管理和调度器，它实现了Standalone作为其内置的资源管理和调度框架，这样进一步降低了Spark的使用门槛，使得所有人都可以非常容易地部署和使用Spark。此外，Spark还提供了在EC2上部署Standalone的Spark集群的工具。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA5NzIyZWU1YzYwY2M5ZTIucG5n?x-oss-process=image/format,png)

#### 4.Spark集群的体系结构

![Spark集群的体系架构1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVmNzUyM2RiYjc3ODg5MjcucG5n?x-oss-process=image/format,png)

![Spark集群的体系架构2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU4OGE2NjA0MDU4NWIwYTMucG5n?x-oss-process=image/format,png)

![Spark集群的体系架构3](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTc3OWZkNzYwMGIzYzVlMjQucG5n?x-oss-process=image/format,png)

##### (1)结构
###### (a)Master：接收任务请求，分发任务

###### (b)Worker：本节点任务调度，资源管理。默认占用该节点所有资源
* Spark对内存没有很好管理，容易出现OOM。Spark把内存管理交给应用程序。	
* Spark架构出现单点故障问题，通过HA解决。
		
###### (c )Driver：客户端

##### (2)启动方式：
###### (a)Spark-submit：
&nbsp;&nbsp;&nbsp;&nbsp;用于提交Spark 任务。每个任务是一个jar。
###### (b)spark-shell：
&nbsp;&nbsp;&nbsp;&nbsp;类似于 REPL

#### 5.Spark运行机制及原理分析
###### (1).WordCount执行的流程分析

![WordCount执行的流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThlOWIyNzhjMjUyNzk5NzcucG5n?x-oss-process=image/format,png)

###### (2).Spark提交任务的流程
![Spark提交任务的流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRiODNiZDJjZjg2ZmM0OWQucG5n?x-oss-process=image/format,png)