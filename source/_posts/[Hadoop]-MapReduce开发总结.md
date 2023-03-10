---
title: '[Hadoop]-MapReduce开发总结'
date: 2019-01-01 17:59:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
在编写mapreduce程序时，需要考虑的几个方面：
#### 1.输入数据接口：InputFormat 
&nbsp;&nbsp;&nbsp;&nbsp;默认使用的实现类是：TextInputFormat 
&nbsp;&nbsp;&nbsp;&nbsp;TextInputFormat的功能逻辑是：一次读一行文本，然后将该行的起始偏移量作为key，行内容作为value返回。
&nbsp;&nbsp;&nbsp;&nbsp;KeyValueTextInputFormat每一行均为一条记录，被分隔符分割为key，value。默认分隔符是tab（\t）。
&nbsp;&nbsp;&nbsp;&nbsp;NlineInputFormat按照指定的行数N来划分切片。
&nbsp;&nbsp;&nbsp;&nbsp;CombineTextInputFormat可以把多个小文件合并成一个切片处理，提高处理效率。
&nbsp;&nbsp;&nbsp;&nbsp;用户还可以自定义InputFormat。

#### 2.逻辑处理接口：Mapper  
   用户根据业务需求实现其中三个方法：map()   setup()   cleanup () 

#### 3.Partitioner分区
&nbsp;&nbsp;&nbsp;&nbsp;有默认实现 HashPartitioner，逻辑是根据key的哈希值和numReduces来返回一个分区号；key.hashCode()&Integer.MAXVALUE % numReduces
	如果业务上有特别的需求，可以自定义分区。

#### 4.Comparable排序
&nbsp;&nbsp;&nbsp;&nbsp;当我们用自定义的对象作为key来输出时，就必须要实现WritableComparable接口，重写其中的compareTo()方法。
* 部分排序：对最终输出的每一个文件进行内部排序。
* 全排序：对所有数据进行排序，通常只有一个Reduce。
* 二次排序：排序的条件有两个。

#### 5.Combiner合并
&nbsp;&nbsp;&nbsp;&nbsp;Combiner合并可以提高程序执行效率，减少io传输。但是使用时必须不能影响原有的业务处理结果。

#### 6.reduce端分组：Groupingcomparator
&nbsp;&nbsp;&nbsp;&nbsp;reduceTask拿到输入数据（一个partition的所有数据）后，首先需要对数据进行分组，其分组的默认原则是key相同，然后对每一组kv数据调用一次reduce()方法，并且将这一组kv中的第一个kv的key作为参数传给reduce的key，将这一组数据的value的迭代器传给reduce()的values参数。
&nbsp;&nbsp;&nbsp;&nbsp;利用上述这个机制，我们可以实现一个高效的分组取最大值的逻辑。
&nbsp;&nbsp;&nbsp;&nbsp;自定义一个bean对象用来封装我们的数据，然后改写其compareTo方法产生倒序排序的效果。然后自定义一个Groupingcomparator，将bean对象的分组逻辑改成按照我们的业务分组id来分组（比如订单号）。这样，我们要取的最大值就是reduce()方法中传进来key。

#### 7.逻辑处理接口：Reducer
&nbsp;&nbsp;&nbsp;&nbsp;用户根据业务需求实现其中三个方法：reduce()   setup()   cleanup () 
#### 8.输出数据接口：OutputFormat
&nbsp;&nbsp;&nbsp;&nbsp;默认实现类是TextOutputFormat，功能逻辑是：将每一个KV对向目标文本文件中输出为一行。
&nbsp;&nbsp;&nbsp;&nbsp;SequenceFileOutputFormat将它的输出写为一个顺序文件。如果输出需要作为后续 MapReduce任务的输入，这便是一种好的输出格式，因为它的格式紧凑，很容易被压缩。
&nbsp;&nbsp;&nbsp;&nbsp;用户还可以自定义OutputFormat。