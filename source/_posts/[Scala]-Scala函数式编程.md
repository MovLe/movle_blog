---
title: '[Scala]-Scala函数式编程'
date: 2020-03-05 18:50:00
tags: Scala
categories: Scala
---

#### 1.Scala中的函数
&nbsp;&nbsp;&nbsp;&nbsp;在Scala中，函数是“头等公民”，就和数字一样。**可以在变量中存放函数，即：将函数作为变量的值(值函数)**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRmOGNlMzhmODZiYzcxMTgucG5n?x-oss-process=image/format,png)

**举例**：使用Spark来执行WordCount

```java
var result = sc.textFile("hdfs://....").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
```
#### 2.匿名函数:没有名字的函数
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE0YmZiZGEzZGIwYjExMjEucG5n?x-oss-process=image/format,png)
```java
//普通函数
def fun1(x:Int):Int = x*3

//匿名函数
(x:Int)=>x*3
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIxMGNlMDM0ZjE4NzY5NjgucG5n?x-oss-process=image/format,png)

注：匿名函数：一个普通的函数，把返回值去掉，再把函数名和“def”去掉，再在"="后面的加一个">"

调用匿名函数作为函数参数
#### 3.带函数参数的函数，即：高阶函数
##### (1)示例1：
* (a)首先，定义一个最普通的函数

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZkNDM5ODJjZTRmMGY1Y2UucG5n?x-oss-process=image/format,png)

* (b)再定义一个高阶函数

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk3YzUwZGU3NmI3Y2NkNzUucG5n?x-oss-process=image/format,png)

* (c )分析这个高阶函数调用的过程

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg4ZDM1MzJjOTQ5ZWEyMWYucG5n?x-oss-process=image/format,png)


##### (2)示例2：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE4YTYyZjU5NGJkYzQyM2QucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;在这个例子中，首先定义了一个普通的函数mytest，然后定义了一个高阶函数myFunction；myFunction接收三个参数：第一个f是一个函数参数，第二个是x，第三个是y。而f是一个函数参数，本身接收两个Int的参数，返回一个Int的值。


#### 4.高阶函数示例
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJiZDdhMTQ0OTI1YWNiYWEucG5n?x-oss-process=image/format,png)

##### (1)map:相当于一个循环，对某个集合中的每个元素进行操作(接收一个函数)，返回一个新的集合

```java
//map
//在列表中的每个元素上计算一个函数，并且返回一个包含相同数目元素的列表
val numbers = List(1,2,3,4,5,6,7,8,9,10)

numbers.map((i:Int)=>i*2)

numbers.map(_*2)

//map函数不改变numbers值
numbers
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVhZmQ3OTZlNGI3YWRjYjUucG5n?x-oss-process=image/format,png)

```java
_ 相当于循环变量 i
_*2 与 (i:Int)=>i*2 功能相同
_+_ 与 (i:Int,j:Int)=>i+j
```
##### (2)foreach：相当于一个循环，对某个集合中的每个元素进行操作(接收一个函数)，不返回结果。
```java
//foreach
//foreach和map相似，只不过它没有返回值，foreach主要是为了对参数进行作用
numbers.foreach((i:Int) => i * 2)

numbers.foreach(_*2)

numbers

numbers.foreach(println(_))

numbers.map(_*2).foreach(println)
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYyOWYzNzQ3OWQzMzJhMmUucG5n?x-oss-process=image/format,png)


##### (3)filter：过滤，选择满足的数据

举例：查询能够被2整除的数字
```java
//filter
//移除任何使得传入的函数返回false的元素

numbers.filter((i:Int) =>i%2==0)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThiM2E3Zjk3M2VjZjRiMGUucG5n?x-oss-process=image/format,png)

说明：(i:Int)=>i%2==0  如果是true 就返回

##### (4)zip：合并两个集合
```java
//zip
//zip把两个列表的原色合成一个由元素对组成的列表
List(1,2,3).zip(List(4,5,6))

List(1,2,3).zip(List(4,5))

List(3).zip(List(4,5))
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVkYWM3NzU0M2RmZjcyMWUucG5n?x-oss-process=image/format,png)

##### (5)partition：根据断言(就是某个条件，可以通过一个匿名函数来实现)的结果，进行分区

举例：把能够被2整除的分成一个区，不能整除的分成另一个区
```java
//partition
//partition根据断言函数的返回值对列表进行拆分
numbers.partition((i:Int)=>i%2==0)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFkODFiNzFmNzM1MDg4NTEucG5n?x-oss-process=image/format,png)

在这个例子中，可以被2整除的被分到一个分区；不能被2整除的被分到另一个分区。

##### (6)find：查找第一个满足条件(断言)的元素
```java
//find
//find返回集合里第一个匹配断言函数的元素
numbers.find(_ % 3==0)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZhMmJiYTMxNmQwMDU0OTIucG5n?x-oss-process=image/format,png)

##### (7)flatten:把嵌套的结果展开,合并成为一个集合
```java
//flatten
//flatten可以把嵌套的结构展开
List(List(1,2,3),List(4,5,6)).flatten
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJhZDZmNDhjN2JmODk0ZGMucG5n?x-oss-process=image/format,png)

##### (8)flatmap：相当于map+flatten
```java
//flatMap
//flatMap是一个常用的combinator，它结合了map和flatten的功能
val myList = List(List(2,4,6,8,10),List(1,3,5,7,9))

myList.flatMap(x=>x.map(_*2))
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM0NTk3NmZhOTIwZmI4MzMucG5n?x-oss-process=image/format,png)

执行过程：
```java
1.将List(2,4,6,8,10)和List(1,3,5,7,9)调用了x=>x.map(_*2)  这里x代表某个List
List(4, 8, 12, 16, 20)  和 List(2, 6, 10, 14, 18)

2.合并成一个List
List(4, 8, 12, 16, 20, 2, 6, 10, 14, 18)
```
#### 5.闭包
&nbsp;&nbsp;&nbsp;&nbsp;就是函数的嵌套，即：在一个函数定义中，包含另外一个函数的定义；**并且在内函数中可以访问外函数中的变量**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE2MWJhNjY5YTk0NTZjY2EucG5n?x-oss-process=image/format,png)

测试上面的函数：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFkOTFkN2M2NGQ0NDA0M2YucG5n?x-oss-process=image/format,png)

#### 6.柯里化：Currying

&nbsp;&nbsp;&nbsp;&nbsp;柯里化函数(Curried Function)是把具有多个参数的函数转换为一条函数链，每个节点上是单一参数。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ2YTc4OWYyZGFhYzIyYzkucG5n?x-oss-process=image/format,png)
一个简单的例子：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMxNTEwNWZmZmQzM2U4NGYucG5n?x-oss-process=image/format,png)

