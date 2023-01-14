---
title: '[Spark]-Spark Core：Spark的算子'
date: 2020-03-10 15:00:00
tags: 
- Spark
- SparkCore
categories: Spark
---

### 一.RDD基础

![RDD](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY3NjRhNDY1ZWE1ZGM2YmEucG5n?x-oss-process=image/format,png)

#### 1.什么是RDD？
&nbsp;&nbsp;&nbsp;&nbsp;RDD(Resilient Distributed Dataset)叫做弹性分布式数据集，是Spark中最基本的数据抽象，它代表一个不可变、可分区、里面的元素可并行计算的集合。RDD具有数据流模型的特点：自动容错、位置感知性调度和可伸缩性。RDD允许用户在执行多个查询时显式地将工作集缓存在内存中，后续的查询能够重用工作集，这极大地提升了查询速度。

#### 2.RDD的属性(源码中的一段话)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjMzIxNzk5MmRmNTI0OWYucG5n?x-oss-process=image/format,png)


###### (a)是一组分区	
&nbsp;&nbsp;&nbsp;&nbsp;理解：RDD是由分区组成的，每个分区运行在不同的Worker上，通过这种方式，实现分布式计算。

###### (b)split理解为分区
&nbsp;&nbsp;&nbsp;&nbsp;在RDD中，有一系列函数，用于处理计算每个分区中的数据。这里把函数叫做算子。

###### (c )RDD之间存在依赖关系。窄依赖，宽依赖
&nbsp;&nbsp;&nbsp;&nbsp;需要用依赖关系来划分Stage，任务是按照Stage来执行的。

###### (d)可以自动以分区规则来创建RDD
&nbsp;&nbsp;&nbsp;&nbsp;创建RDD时，可以指定分区，也可以自定义分区规则。

###### (e)优先选择离文件位置近的节点来执行任务

#### 3.RDD的创建方式
##### (1)通过外部的数据文件创建，如HDFS
```shell
val rdd1 = sc.textFile(“hdfs://192.168.1.121:9000/data/data.txt”)
```
##### (2)通过sc.parallelize进行创建

```shell
val rdd1 = sc.parallelize(Array(1,2,3,4,5,6,7,8))
```
#### 4.RDD的类型：

&nbsp;&nbsp;&nbsp;&nbsp;**Transformation**和**Action**

#### 5.RDD的基本原理

![RDD的基本原理](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVjNjQxN2I2NmFlYmRkZDUucG5n?x-oss-process=image/format,png)

### 二.Transformation
&nbsp;&nbsp;&nbsp;&nbsp;RDD中的所有转换都是**延迟加载**的，也就是说，它们并不会直接计算结果。相反的，它们只是记住这些应用到基础数据集(例如一个文件)上的转换动作。只有当发生一个要求返回结果给Driver的动作时，这些转换才会真正运行。这种设计让Spark更加有效率地运行。

|转换|含义|
|:-:|:-:|
|map(func)|返回一个新的RDD，该RDD由每一个输入元素经过func函数转换后组成|
|filter(func)|返回一个新的RDD，该RDD由经过func函数计算后返回值为true的输入元素组成|
|flatMap(func)|类似于map，但是每一个输入元素可以被映射为0或多个输出元素(所以func应该返回一个序列，而不是单一元素)|
|mapPartitions(func)|类似于map，但独立地在RDD的每一个分片上运行，因此在类型为T的RDD上运行时，func的函数类型必须是Iterator[T] => Iterator[U]|
|mapPartitionsWithIndex(func)|类似于mapPartitions，但func带有一个整数参数表示分片的索引值，因此在类型为T的RDD上运行时，func的函数类型必须是(Int, Interator[T]) => Iterator[U]|
|sample(withReplacement, fraction, seed)|根据fraction指定的比例对数据进行采样，可以选择是否使用随机数进行替换，seed用于指定随机数生成器种子|
|union(otherDataset)|对源RDD和参数RDD求并集后返回一个新的RDD|
|intersection(otherDataset)|	对源RDD和参数RDD求交集后返回一个新的RDD|
|distinct([numTasks]))|对源RDD进行去重后返回一个新的RDD|
|groupByKey([numTasks])|在一个(K,V)的RDD上调用，返回一个(K, Iterator[V])的RDD|
|reduceByKey(func, [numTasks])|在一个(K,V)的RDD上调用，返回一个(K,V)的RDD，使用指定的reduce函数，将相同key的值聚合到一起，与groupByKey类似，reduce任务的个数可以通过第二个可选的参数来设置|
|aggregateByKey(zeroValue)(seqOp,combOp,[numTasks])|
|sortByKey([ascending], [numTasks])|在一个(K,V)的RDD上调用，K必须实现Ordered接口，返回一个按照key进行排序的(K,V)的RDD|
|sortBy(func,[ascending], [numTasks])|与sortByKey类似，但是更灵活|
|join(otherDataset, [numTasks])|在类型为(K,V)和(K,W)的RDD上调用，返回一个相同key对应的所有元素对在一起的(K,(V,W))的RDD|
|cogroup(otherDataset, [numTasks])|在类型为(K,V)和(K,W)的RDD上调用，返回一个(K,(Iterable<V>,Iterable<W>))类型的RDD|
|cartesian(otherDataset)|笛卡尔积|
|pipe(command, [envVars])	||
|coalesce(numPartitions)||	
|repartition(numPartitions)||
|repartitionAndSortWithinPartitions(partitioner)	||

#### 1.举例：
##### (1)创建一个RDD，使用List

```java
val rdd1 = sc.parallelize(List(1,2,3,4,5,8,34,100,79))

val rdd2 = rdd1.map(_*2)

rdd2.collect

rdd2.sortBy(x=>x,true)

rdd2.sortBy(x=>x,true).collect

rdd2.sortBy(x=>x,false).collect

rdd2.filter(_>20).collect
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM3ODg1MWZhZGIyNmZmZTcucG5n?x-oss-process=image/format,png)

##### (2)创建一个RDD，字符串

```java
val rdd4 = sc.parallelize(Array("a b c","d e f","x y z"))

val rdd5 = rdd4.flatMap(_.split(" "))

rdd5.collect
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM2N2QwNmI0ODIyZGFmOTEucG5n?x-oss-process=image/format,png)

##### (3)集合操作

```java
val rdd6 = sc.parallelize(List(5,6,7,8,9,10))

val rdd7 = sc.parallelize(List(1,2,3,4,5,6))

val rdd8 = rdd6.union(rdd7)

rdd8.collect

rdd8.distinct.collect

val rdd9 = rdd6.intersection(rdd7)

rdd9.collect
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU3ZGVhOGVjMjU0ZGE0MTkucG5n?x-oss-process=image/format,png)

##### (4)分组操作：reduceByKey groupByKey

```java
val rdd1 = sc.parallelize(List(("Tom",1000),("Jerry",3000),("Mary",2000)))

val rdd2 = sc.parallelize(List(("Jerry",1000),("Tom",3000),("Mike",2000)))

val rdd3 = rdd1 union rdd2

rdd3.collect

val rdd4 = rdd3.groupByKey

rdd4.collect

rdd3.reduceByKey(_+_).collect
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgyZmIzZTRjMTBlM2RiYzQucG5n?x-oss-process=image/format,png)


注意：使用分组函数时，不推荐使用groupByKey，因为性能不好，官方推荐reduceByKey

##### (5)cogroup操作
```java
val rdd1 = sc.parallelize(List(("Tom",1),("Tom",2),("jerry",1),("Mike",2)))

val rdd2 = sc.parallelize(List(("jerry",2),("Tom",1),("Bob",2)))

val rdd3 = rdd1.cogroup(rdd2)

rdd3.collect
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM4YzE3YzRjN2JiM2I1NWQucG5n?x-oss-process=image/format,png)

##### (6)reduce操作(reduce是一个Action)
```java
val rdd1 = sc.parallelize(List(1,2,3,4,5))

val rdd2 = rdd1.reduce(_+_)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY3MWQyZjMyODU3MWI5ODAucG5n?x-oss-process=image/format,png)

##### (7)需求：按value排序，SortByKey按照key排序。
做法：把Key Value交换位置，并且交换两次。
(a).第一步交换，把key value交换，然后调用sortByKey
(b).调换位置
```java
val rdd1 = sc.parallelize(List(("tom",1),("jerry",1),("kitty",2),("bob",1)))

val rdd2 = sc.parallelize(List(("jerry",2),("tom",3),("kitty",5),("bob",2)))

val rdd3 = rdd1 union(rdd2)

val rdd4 = rdd3.reduceByKey(_+_)

rdd4.collect

val rdd5 = rdd4.map(t => (t._2,t._1)).sortByKey(false).map(t=>(t._2,t._1))

rdd5.collect
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJjMzNjZTFjOTc1YzI2NjIucG5n?x-oss-process=image/format,png)


### 三.Action

|动作|含义|
|:-:|:-:|
|reduce(func)|通过func函数聚集RDD中的所有元素，这个功能必须是课交换且可并联的|
|collect()|在驱动程序中，以数组的形式返回数据集的所有元素|
|count()|返回RDD的元素个数|
|first()|返回RDD的第一个元素（类似于take(1)）|
|take(n)|返回一个由数据集的前n个元素组成的数组|
|takeSample(withReplacement,num, [seed])|返回一个数组，该数组由从数据集中随机采样的num个元素组成，可以选择是否用随机数替换不足的部分，seed用于指定随机数生成器种子|
|takeOrdered(n, [ordering])	||
|saveAsTextFile(path)|将数据集的元素以textfile的形式保存到HDFS文件系统或者其他支持的文件系统，对于每个元素，Spark将会调用toString方法，将它装换为文件中的文本|
|saveAsSequenceFile(path)|	将数据集中的元素以Hadoop sequencefile的格式保存到指定的目录下，可以使HDFS或者其他Hadoop支持的文件系统|
|saveAsObjectFile(path)||
|countByKey()|针对(K,V)类型的RDD，返回一个(K,Int)的map，表示每一个key对应的元素个数|
|foreach(func)|在数据集的每一个元素上，运行函数func进行更新|


### 四.RDD的缓存机制

&nbsp;&nbsp;&nbsp;&nbsp;RDD通过persist方法或cache方法可以将前面的计算结果缓存，但是并不是这两个方法被调用时立即缓存，而是触发后面的action时，该RDD将会被缓存在计算节点的内存中，并供后面重用。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNiYmM2NDBkZDk4MTU5ZTMucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;通过查看源码发现cache最终也是调用了persist方法，默认的存储级别都是仅在内存存储一份，Spark的存储级别还有好多种，存储级别在object StorageLevel中定义的。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY4ZDQ4NGU2MzA3M2U3MzMucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;缓存有可能丢失，或者存储存储于内存的数据由于内存不足而被删除，RDD的缓存容错机制保证了即使缓存丢失也能保证计算的正确执行。通过基于RDD的一系列转换，丢失的数据会被重算，由于RDD的各个Partition是相对独立的，因此只需要计算丢失的部分即可，并不需要重算全部Partition。

##### (1)Demo示例：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdiYTM2ZDAwYjg0NjllOWYucG5n?x-oss-process=image/format,png)

##### (2)通过UI进行监控：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgwOTJhZWNhOWZkYzI2YTEucG5n?x-oss-process=image/format,png)


### 五.RDD的Checkpoint(检查点)机制：容错机制
&nbsp;&nbsp;&nbsp;&nbsp;检查点(本质是通过将RDD写入Disk做检查点)是为了通过lineage(血统)做容错的辅助，lineage过长会造成容错成本过高，这样就不如在中间阶段做检查点容错，如果之后有节点出现问题而丢失分区，从做检查点的RDD开始重做Lineage，就会减少开销。

&nbsp;&nbsp;&nbsp;&nbsp;设置checkpoint的目录，可以是本地的文件夹、也可以是HDFS。一般是在具有容错能力，高可靠的文件系统上(比如HDFS, S3等)设置一个检查点路径，用于保存检查点数据。
&nbsp;&nbsp;&nbsp;&nbsp;分别举例说明：

##### (1)本地目录
注意：这种模式，需要将spark-shell运行在**本地模式**上

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMwYWNkM2ZiNDcxZGRlZDIucG5n?x-oss-process=image/format,png)

##### (2)HDFS的目录

注意：这种模式，需要将spark-shell运行在**集群模式**上
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVlNjY5OThhNjRiMjkzZjkucG5n?x-oss-process=image/format,png)

##### (3)源码中的一段话

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlmYjVhZjViYzA0YWUyNmEucG5n?x-oss-process=image/format,png)

### 六.RDD的依赖关系和Spark任务中的Stage
#### 1.RDD的依赖关系
&nbsp;&nbsp;&nbsp;&nbsp;RDD和它依赖的父RDD（s）的关系有两种不同的类型，即窄依赖(narrow dependency)和宽依赖(wide dependency)。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZkODY2ZTY5NTUwYzU1Y2YucG5n?x-oss-process=image/format,png)

##### (1)窄依赖指的是每一个父RDD的Partition最多被子RDD的一个Partition使用

总结：窄依赖我们形象的比喻为独生子女

##### (2)宽依赖指的是多个子RDD的Partition会依赖同一个父RDD的Partition

总结：窄依赖我们形象的比喻为超生

#### 2.Spark任务中的Stage
&nbsp;&nbsp;&nbsp;&nbsp;DAG(Directed Acyclic Graph)叫做有向无环图，原始的RDD通过一系列的转换就就形成了DAG，根据RDD之间的依赖关系的不同将DAG划分成不同的Stage，对于窄依赖，partition的转换处理在Stage中完成计算。对于宽依赖，由于有Shuffle的存在，只能在parent RDD处理完成后，才能开始接下来的计算，因此**宽依赖是划分Stage的依据**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM5MTQxMmEwMjIwYmEyNjQucG5n?x-oss-process=image/format,png)

### 七.RDD基础练习
##### (1)练习1：
```java
//通过并行化生成rdd
val rdd1 = sc.parallelize(List(5, 6, 4, 7, 3, 8, 2, 9, 1, 10))
//对rdd1里的每一个元素乘2然后排序
val rdd2 = rdd1.map(_ * 2).sortBy(x => x, true)
//过滤出大于等于十的元素
val rdd3 = rdd2.filter(_ >= 10)
//将元素以数组的方式在客户端显示
rdd3.collect
```

##### (2)练习2：
```java
val rdd1 = sc.parallelize(Array("a b c", "d e f", "h i j"))
//将rdd1里面的每一个元素先切分在压平
val rdd2 = rdd1.flatMap(_.split(' '))
rdd2.collect
```

##### (3)练习3：
```java
val rdd1 = sc.parallelize(List(5, 6, 4, 3))
val rdd2 = sc.parallelize(List(1, 2, 3, 4))
//求并集
val rdd3 = rdd1.union(rdd2)
//求交集
val rdd4 = rdd1.intersection(rdd2)
//去重
rdd3.distinct.collect
rdd4.collect
```
##### (4)练习4：
```java
val rdd1 = sc.parallelize(List(("tom", 1), ("jerry", 3), ("kitty", 2)))
val rdd2 = sc.parallelize(List(("jerry", 2), ("tom", 1), ("shuke", 2)))
//求jion
val rdd3 = rdd1.join(rdd2)
rdd3.collect
//求并集
val rdd4 = rdd1 union rdd2
//按key进行分组
rdd4.groupByKey
rdd4.collect

```

##### (5)练习5：
```java
val rdd1 = sc.parallelize(List(("tom", 1), ("tom", 2), ("jerry", 3), ("kitty", 2)))
val rdd2 = sc.parallelize(List(("jerry", 2), ("tom", 1), ("shuke", 2)))
//cogroup
val rdd3 = rdd1.cogroup(rdd2)
//注意cogroup与groupByKey的区别
rdd3.collect
```
##### (6)练习6：
```java
val rdd1 = sc.parallelize(List(1, 2, 3, 4, 5))
//reduce聚合
val rdd2 = rdd1.reduce(_ + _)
rdd2.collect
```
##### (7)练习7：
```java
val rdd1 = sc.parallelize(List(("tom", 1), ("jerry", 3), ("kitty", 2),  ("shuke", 1)))
val rdd2 = sc.parallelize(List(("jerry", 2), ("tom", 3), ("shuke", 2), ("kitty", 5)))
val rdd3 = rdd1.union(rdd2)
//按key进行聚合
val rdd4 = rdd3.reduceByKey(_ + _)
rdd4.collect
//按value的降序排序
val rdd5 = rdd4.map(t => (t._2, t._1)).sortByKey(false).map(t => (t._2, t._1))
rdd5.collect
```
