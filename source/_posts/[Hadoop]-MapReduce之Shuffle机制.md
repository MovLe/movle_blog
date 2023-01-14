---
title: '[Hadoop]-MapReduce之Shuffle机制'
date: 2019-01-01 17:54:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
#### 1. Shuffle机制
&nbsp;&nbsp;&nbsp;&nbsp;Mapreduce确保每个reducer的输入都是按键排序的。系统执行排序的过程(即将map输出作为输入传给reducer)称为shuffle。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcwZTMyM2E2NDg4YWYzOGYucG5n?x-oss-process=image/format,png)
![shuffle机制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRlZDQwNzk0NmFlNDNiZGEucG5n?x-oss-process=image/format,png)

#### 2.Partition分区
##### (0).问题引出：
要求将统计结果按照条件输出到不同文件中（分区）。比如：将统计结果按照手机归属地不同省份输出到不同文件中（分区）

##### (1).默认partition分区
```java
public class HashPartitioner<K, V> extends Partitioner<K, V> {
    public int getPartition(K key, V value, int numReduceTasks) {
      return (key.hashCode() & Integer.MAX_VALUE) % numReduceTasks;
    }
}
```
默认分区是根据key的hashCode对reduceTasks个数取模得到的。用户没法控制哪个key存储到哪个分区。

##### (2).自定义Partitioner步骤
(a)自定义类继承Partitioner，重写getPartition()方法
```java
public class ProvincePartitioner extends Partitioner<Text, FlowBean> {
       @Override
       public int getPartition(Text key, FlowBean value, int numPartitions) {
              // 1 获取电话号码的前三位
              String preNum = key.toString().substring(0, 3);
              partition = 4;

              // 2 判断是哪个省
              if ("136".equals(preNum)) {
                     partition = 0;
              }else if ("137".equals(preNum)) {
                     partition = 1;
              }else if ("138".equals(preNum)) {
                     partition = 2;
              }else if ("139".equals(preNum)) {
                     partition = 3;
              }
              return partition;
       }
}
```

(b)在job驱动中，设置自定义partitioner：

```java
job.setPartitionerClass(CustomPartitioner.class);
```

(c )自定义partition后，要根据自定义partitioner的逻辑设置相应数量的reduce task

```java
job.setNumReduceTasks(5);
```
##### (3).注意：
* 如果reduceTask的数量> getPartition的结果数，则会多产生几个空的输出文件part-r-000xx；

* 如果1<reduceTask的数量<getPartition的结果数，则有一部分分区数据无处安放，会Exception；

* 如果reduceTask的数量=1，则不管mapTask端输出多少个分区文件，最终结果都交给这一个reduceTask，最终也就只会产生一个结果文件 part-r-00000；

例如：假设自定义分区数为5，则
(a)job.setNumReduceTasks(1);会正常运行，只不过会产生一个输出文件
(b)job.setNumReduceTasks(2);会报错
(c )job.setNumReduceTasks(6);大于5，程序会正常运行，会产生空文件

###### (4).案例实操


#### 3.WritableComparable排序
&nbsp;&nbsp;&nbsp;&nbsp;排序是MapReduce框架中最重要的操作之一。Map Task和Reduce Task均会对数据（按照key）进行排序。该操作属于Hadoop的默认行为。任何应用程序中的数据均会被排序，而不管逻辑上是否需要。默认排序是按照字典顺序排序，且实现该排序的方法是快速排序。

&nbsp;&nbsp;&nbsp;&nbsp;对于Map Task，它会将处理的结果暂时放到一个缓冲区中，当缓冲区使用率达到一定阈值后，再对缓冲区中的数据进行一次排序，并将这些有序数据写到磁盘上，而当数据处理完毕后，它会对磁盘上所有文件进行一次合并，以将这些文件合并成一个大的有序文件。

&nbsp;&nbsp;&nbsp;&nbsp;对于Reduce Task，它从每个Map Task上远程拷贝相应的数据文件，如果文件大小超过一定阈值，则放到磁盘上，否则放到内存中。如果磁盘上文件数目达到一定阈值，则进行一次合并以生成一个更大文件；如果内存中文件大小或者数目超过一定阈值，则进行一次合并后将数据写到磁盘上。当所有数据拷贝完毕后，Reduce Task统一对内存和磁盘上的所有数据进行一次合并。

每个阶段的默认排序

##### (1).排序的分类：
###### (a)部分排序：
MapReduce根据输入记录的键对数据集排序。保证输出的每个文件内部排序。

###### (b)全排序：
&nbsp;&nbsp;&nbsp;&nbsp;如何用Hadoop产生一个全局排序的文件？最简单的方法是使用一个分区。但该方法在处理大型文件时效率极低，因为一台机器必须处理所有输出文件，从而完全丧失了MapReduce所提供的并行架构。

&nbsp;&nbsp;&nbsp;&nbsp;替代方案：首先创建一系列排好序的文件；其次，串联这些文件；最后，生成一个全局排序的文件。主要思路是使用一个分区来描述输出的全局排序。例如：可以为上述文件创建3个分区，在第一分区中，记录的单词首字母a-g，第二分区记录单词首字母h-n, 第三分区记录单词首字母o-z。

###### (c )辅助排序：(GroupingComparator分组)
&nbsp;&nbsp;&nbsp;&nbsp;Mapreduce框架在记录到达reducer之前按键对记录排序，但键所对应的值并没有被排序。甚至在不同的执行轮次中，这些值的排序也不固定，因为它们来自不同的map任务且这些map任务在不同轮次中完成时间各不相同。一般来说，大多数MapReduce程序会避免让reduce函数依赖于值的排序。但是，有时也需要通过特定的方法对键进行排序和分组等以实现对值的排序。

###### (d)二次排序：
&nbsp;&nbsp;&nbsp;&nbsp;在自定义排序过程中，如果compareTo中的判断条件为两个即为二次排序。

##### (2).自定义排序WritableComparable
###### (a)原理分析
&nbsp;&nbsp;&nbsp;&nbsp;bean对象实现WritableComparable接口重写compareTo方法，就可以实现排序
```java
@Override
public int compareTo(FlowBean o) {
       // 倒序排列，从大到小
       return this.sumFlow > o.getSumFlow() ? -1 : 1;
}
```
###### (b)案例实操

#### 4.GroupingComparator分组（辅助排序）

(1).对reduce阶段的数据根据某一个或几个字段进行分组。

(2).案例实操

#### 5.Combiner合并
##### (0).在分布式的架构中，分布式文件系统HDFS，和分布式运算程序编程框架mapreduce。
* HDFS:不怕大文件，怕很多小文件
* mapreduce :怕数据倾斜

那么mapreduce是如果解决多个小文件的问题呢？

 ##### (1).mapreduce关于大量小文件的优化策略

(a)默认情况下，**TextInputFormat**对任务的切片机制是按照**文件**规划切片，不管有多少个小文件，都会是单独的切片，都**会交给一个maptask**，这样，如果有大量的小文件
就会产生大量的maptask，处理效率极端底下

(b)优化策略
* 最好的方法：在数据处理的最前端（预处理、采集），就将小文件合并成大文件，在上传到HDFS做后续的分析
* 补救措施：如果已经是大量的小文件在HDFS中了，可以使用另一种inputformat来做切片（CombineFileInputformat），它的切片逻辑跟**TextInputformat**不同,它可以将多个小文件从逻辑上规划到一个切片中，这样，多个小文件就可以交给一个maptask了
```java
       //如果不设置InputFormat，它默认的用的是TextInputFormat.class

       /*CombineTextInputFormat为系统自带的组件类

        * setMinInputSplitSize 中的2048是表示n个小文件之和不能大于2048

        * setMaxInputSplitSize 中的4096是     当满足setMinInputSplitSize中的2048情况下  在满足n+1个小文件之和不能大于4096

        */

       job.setInputFormatClass(CombineTextInputFormat.class);

       CombineTextInputFormat.setMinInputSplitSize(job, 2048);

       CombineTextInputFormat.setMaxInputSplitSize(job, 4096);
```
##### (2).示例
###### (a)输入数据：准备5个小文件
###### (b)实现过程
* (a)不做任何处理，运行需求1中的wordcount程序，观察切片个数为5
* (b)在WordcountDriver中增加如下代码，运行程序，并观察运行的切片个数为1
```java
// 如果不设置InputFormat，它默认用的是TextInputFormat.class

job.setInputFormatClass(CombineTextInputFormat.**class**);

CombineTextInputFormat.*setMaxInputSplitSize*(job, 4*1024*1024);// 4m

CombineTextInputFormat.*setMinInputSplitSize*(job, 2*1024*1024);// 2m
```
注：在看number of splits时，和最大值(MaxSplitSize)有关、总体规律就是和低于最大值是一片、高于最大值1.5倍+，则为两片；高于最大值2倍以上则向下取整，比如文件大小65MB，切片最大值为4MB,那么切片为16个.总体来说，切片差值不超过1个，不影响整体性能

##### (3).自定义Combiner实现步骤：
(a)自定义一个combiner继承Reducer，重写reduce方法
```java
public class WordcountCombiner extends Reducer<Text, IntWritable, Text, IntWritable>{
       @Override
       protected void reduce(Text key, Iterable<IntWritable> values,
                     Context context) throws IOException, InterruptedException {

        // 1 汇总操作
              int count = 0;
              for(IntWritable v :values){
                     count = v.get();
              }
        // 2 写出
              context.write(key, new IntWritable(count));
       }
}
```
(b)在job驱动类中设置：  
```java
job.setCombinerClass(WordcountCombiner.class);
```