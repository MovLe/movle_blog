---
title: '[Hadoop]-Hadoop序列化'
date: 2019-01-01 17:50:00
tags: 
- Hadoop
- MapReduce
categories: Hadoop
---
#### 1 为什么要序列化？
&nbsp;&nbsp;&nbsp;&nbsp;一般来说，“活的”对象只生存在内存里，关机断电就没有了。而且“活的”对象只能由本地的进程使用，不能被发送到网络上的另外一台计算机。 然而序列化可以存储“活的”对象，可以将“活的”对象发送到远程计算机。

#### 2 什么是序列化？

&nbsp;&nbsp;&nbsp;&nbsp;序列化就是把内存中的对象，转换成字节序列（或其他数据传输协议）以便于存储（持久化）和网络传输。 
反序列化就是将收到字节序列（或其他数据传输协议）或者是硬盘的持久化数据，转换成内存中的对象。

#### 3 为什么不用Java的序列化？

&nbsp;&nbsp;&nbsp;&nbsp;Java的序列化是一个重量级序列化框架（Serializable），一个对象被序列化后，会附带很多额外的信息（各种校验信息，header，继承体系等），不便于在网络中高效传输。所以，hadoop自己开发了一套序列化机制（Writable），精简、高效。

#### 4 为什么序列化对Hadoop很重要？

&nbsp;&nbsp;&nbsp;&nbsp;因为Hadoop在集群之间进行通讯或者RPC调用的时候，需要序列化，而且要求序列化要快，且体积要小，占用带宽要小。所以必须理解Hadoop的序列化机制。
&nbsp;&nbsp;&nbsp;&nbsp;序列化和反序列化在分布式数据处理领域经常出现：进程通信和永久存储。然而Hadoop中各个节点的通信是通过远程调用（RPC）实现的，那么RPC序列化要求具有以下特点：
* 紧凑：紧凑的格式能让我们充分利用网络带宽，而带宽是数据中心最稀缺的资
* 快速：进程通信形成了分布式系统的骨架，所以需要尽量减少序列化和反序列化的性能开销，这是基本的；
* 可扩展：协议为了满足新的需求变化，所以控制客户端和服务器过程中，需要直接引进相应的协议，这些是新协议，原序列化方式能支持新的协议报文；
* 互操作：能支持不同语言写的客户端和服务端进行交互；

#### 5.常用数据序列化类型

常用的数据类型对应的hadoop数据序列化类型

|Java类型|Hadoop Writable类型|
|:-:|:-:|
|boolean|BooleanWritable|
|byte|ByteWritable|
|int|IntWritable|
|float|FloatWritable|
|long|LongWritable|
|double|DoubleWritable|
|string|Text|
|map|MapWritable|
|array|ArrayWritable|


#### 6 自定义bean对象实现序列化接口（Writable）

##### (1)自定义bean对象要想序列化传输，必须实现序列化接口，需要注意以下7项
(a)必须实现Writable接口

(b)反序列化时，需要反射调用空参构造函数，所以必须有空参构造
```java
public FlowBean() {
    super();
}
```

(c )重写序列化方法
```java
@Override
public void write(DataOutput out) throws IOException {

    out.writeLong(upFlow);

    out.writeLong(downFlow);

    out.writeLong(sumFlow);
}
```
(d)重写反序列化方法
```java
@Override
public void readFields(DataInput in) throws IOException {
    upFlow = in.readLong();

    downFlow = in.readLong();

    sumFlow = in.readLong();
}
```
(e)注意反序列化的顺序和序列化的顺序完全一致

(f)要想把结果显示在文件中，需要重写toString()，可用”\t”分开，方便后续用。

(g)如果需要将自定义的bean放在key中传输，则还需要实现comparable接口，因为mapreduce框中的shuffle过程一定会对key进行排序。
```java
@Override
public int compareTo(FlowBean o) {
    // 倒序排列，从大到小
    return this.sumFlow > o.getSumFlow() ? -1 : 1;
}
```