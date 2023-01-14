---
title: '[Spark]-Spark Core：Spark RDD的高级算子'
date: 2020-03-10 16:00:00
tags: 
- Spark
- SparkCore
categories: Spark
---

#### 1.mapPartitionsWithIndex

把每个partition中的分区号和对应的值拿出来

```java
def mapPartitionsWithIndex[U](f: (Int, Iterator[T]) ⇒ Iterator[U])
```

##### (1)参数说明：
* f是一个函数参数，需要自定义。
* f 接收两个参数，第一个参数是Int，代表分区号。第二个Iterator[T]代表分区中的元素。
			
##### (2)通过这两个参数，可以定义处理分区的函数。
Iterator[U] ： 操作完成后，返回的结果。

##### (3)示例：将每个分区中的元素和分区号打印出来。
###### (a)
```java
val rdd1 = sc.parallelize(List(1,2,3,4,5,6,7,8,9), 3)
```
###### (b)创建一个函数返回RDD中的每个分区号和元素：
```java
def func1(index:Int, iter:Iterator[Int]):Iterator[String] ={
   iter.toList.map( x => "[PartID:" + index + ", value=" + x + "]" ).iterator
}
```
###### (c )调用：

```java
rdd1.mapPartitionsWithIndex(func1).collect
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTYzNzJjZjQzYzViY2M1NjYucG5n?x-oss-process=image/format,png)
#### 2.aggregate

**含义**：先对局部聚合，再对全局聚合

```java
def aggregate[U](zeroValue: U)(seqOp: (U, T) ⇒ U, combOp: (U, U) ⇒ U)
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEyZmE5ZTBhNjViODQ1MDQucG5n?x-oss-process=image/format,png)

**举例**：

##### (1)第一个例子：
```java
val rdd1 = sc.parallelize(List(1,2,3,4,5), 2)

def func1(index:Int, iter:Iterator[Int]):Iterator[String] ={
   iter.toList.map( x => "[PartID:" + index + ", value=" + x + "]" ).iterator
}

//查看每个分区中的元素：
rdd1.mapPartitionsWithIndex(func1).collect
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ2YmJjZTg1ZjZkMjUxNDUucG5n?x-oss-process=image/format,png)

###### (a)需求：将每个分区中的最大值求和，注意：初始值是0；

![分析过程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZhZDk0YmZjNjM2MTY3NGUucG5n?x-oss-process=image/format,png)
```java
//如果初始值为0，结果为7
rdd1.aggregate(0)(max(_,_),_+_)

//如果初始值为10，则结果为：30
rdd1.aggregate(10)(max(_,_),_+_)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFhMGQxNzMzMzA4ZDUwYWQucG5n?x-oss-process=image/format,png)

###### (b)需求：如果是求和，注意：初始值是0：
```java
//如果初始值是0
rdd1.aggregate(0)(_+_,_+_)

//如果初始值是10，则结果是：45
rdd1.aggregate(10)(_+_,_+_)
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWZjNmYzZTA0Y2NjODBlNTQucG5n?x-oss-process=image/format,png)
##### (2)第二个例子：一个字符串的例子：
```java
val rdd2 = sc.parallelize(List("a","b","c","d","e","f"),2)

//修改一下刚才的查看分区元素的函数
def func2(index: Int, iter: Iterator[(String)]) : Iterator[String] = {
  iter.toList.map(x => "[partID:" +  index + ", val: " + x + "]").iterator
}

//查看两个分区中的元素：
rdd2.mapPartitionsWithIndex(func2).collect
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU3Y2RhNjA2ZDcxMjNlMWMucG5n?x-oss-process=image/format,png)

运行结果：

```java
rdd2.aggregate("")(_+_,_+_)

rdd2.aggregate("*")(_+_,_+_)
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFhMmJhMmEwNDI5OGQyZjAucG5n?x-oss-process=image/format,png)
##### (3)例三：更复杂一点的例子
```java
val rdd3 = sc.parallelize(List("12","23","345","4567"),2)

rdd3.mapPartitionsWithIndex(func2).collect

rdd3.aggregate("")((x,y) => math.max(x.length, y.length).toString, (x,y) => x + y)
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZlZDJiNGJiODE1N2I2YzEucG5n?x-oss-process=image/format,png)

程序执行分析：
```
第一个分区： "12"  "23"
    第一次比较："" 和 "12" 比，求长度的最大值: 2 。 2 ---> "2"
    第二次比较："2" 和 "23" 比，求长度的最大值: 2。 2 ---> "2"
				
第二个分区："345"  "4567"
    第一次比较："" 和 "345" 比，求长度的最大值: 3 。 3 ---> "3"
    第二次比较："3" 和 "4567" 比，求长度的最大值: 4。 4 ---> "4"
```
结果可能是：”24”，也可能是：”42”

##### (4)例四：
```java
val rdd4 = sc.parallelize(List("12","23","345",""),2)

rdd4.mapPartitionsWithIndex(func2).collect

rdd4.aggregate("")((x,y) => math.min(x.length, y.length).toString, (x,y) => x + y)
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYwOTY4MDVjMTg1OWQyZGUucG5n?x-oss-process=image/format,png)

程序执行分析：
```
第一个分区： "12"  "23"
    第一次比较："" 和 "12" 比长度，求长度的最小值。 0 。 0 ---> "0"
    第二次比较："0" 和 "23" 比长度，求长度的最小值。 1。 1 ---> "1"
				
第二个分区："345"  ""
    第一次比较："" 和 "345" 比，求长度的最小值。 0 。 0 ---> "0"
    第二次比较："0" 和 "" 比，求长度的最小值。 0。 0 ---> "0"
```
结果是：”10”，也可能是”01”，

原因：注意有个初始值””，其长度0，然后0.toString变成字符串

##### (5)例5:
```java
val rdd5 = sc.parallelize(List("12","23","","345"),2)

rdd5.mapPartitionsWithIndex(func2).collect

rdd5.aggregate("")((x,y) => math.min(x.length, y.length).toString, (x,y) => x + y)
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWMxYmJmMWQ1OWYwMWVjY2MucG5n?x-oss-process=image/format,png)

结果是：”11”，原因同上

程序执行分析：
```
第一个分区： "12"  "23"
		第一次比较："" 和 "12" 比长度，求长度的最小值。 0 。 0 ---> "0"
		第二次比较："0" 和 "23" 比长度，求长度的最小值。 1。 1 ---> "1"
				
第二个分区：""  "345"
		第一次比较："" 和 "" 比，求长度的最小值。 0 。 0 ---> "0"
		第二次比较："0" 和 "345" 比，求长度的最小值。 1。 1 ---> "1"	
```
#### 3.aggregateByKey:类似于aggregate操作，区别：操作的 <key value> 的数据
##### (1)准备数据：
```java
val pairRDD = sc.parallelize(List( ("cat",2), ("cat", 5), ("mouse", 4),("cat", 12), ("dog", 12), ("mouse", 2)), 2)

def func3(index: Int, iter: Iterator[(String, Int)]) : Iterator[String] = {
  iter.toList.map(x => "[partID:" +  index + ", val: " + x + "]").iterator
}

pairRDD.mapPartitionsWithIndex(func3).collect
```

![准备数据](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBkYTYxNmIwOTZkZThmZTEucG5n?x-oss-process=image/format,png)

##### (2)两个分区中的元素：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg0ODFhZjc0ZDM2YmVjM2YucG5n?x-oss-process=image/format,png)
##### (3) 示例：

###### (a)将每个分区中的动物最多的个数求和
```java
pairRDD.aggregateByKey(0)(math.max(_, _), _ + _).collect
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTUxNjVhMmZjMDI3NDBmMGIucG5n?x-oss-process=image/format,png)

###### (b)将每种动物个数求和
```java
pairRDD.aggregateByKey(0)(_+_, _ + _).collect
```

![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNmOTkyYmQ5NmY2NDkwZmMucG5n?x-oss-process=image/format,png)
###### (c )这个例子也可以使用：reduceByKey
```java
pairRDD.reduceByKey(_+_).collect
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBkMGJjY2MyNDY0M2YxOTQucG5n?x-oss-process=image/format,png)

**与reduceByKey相比，aggregateByKey 效率更高**

#### 4.coalesce与repartition

##### (1)都是将RDD中的分区进行重分区。

##### (2)区别是：coalesce默认不会进行shuffle(false)；而repartition会进行shuffle(true)，即：会将数据真正通过网络进行重分区。

##### (3)示例：
```java
def func4(index: Int, iter: Iterator[(Int)]) : Iterator[String] = {
  iter.toList.map(x => "[partID:" +  index + ", val: " + x + "]").iterator
}

val rdd1 = sc.parallelize(List(1,2,3,4,5,6,7,8,9), 2)

rdd1.mapPartitionsWithIndex(func4).collect
```
![数据准备](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcxZGYxMjIzY2M4NjM4NWQucG5n?x-oss-process=image/format,png)

下面两句话是等价的：
```java
val rdd2 = rdd1.repartition(3)

val rdd3 = rdd1.coalesce(3,true)    //--->如果是false，查看RDD的length依然是2
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEwOTk1MDZjNTFmZDI0YmQucG5n?x-oss-process=image/format,png)

#### 5、其他高级算子

参考：[http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html](http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html)