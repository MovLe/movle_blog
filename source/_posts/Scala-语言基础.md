---
title: 'Scala语言基础'
date: 2020-03-05 16:50:00
tags: Scala
categories: Scala
---

#### 1、Scala简介
&nbsp;&nbsp;&nbsp;&nbsp;Scala是一种多范式的编程语言，其设计的初衷是要集成面向对象编程和函数式编程的各种特性。Scala运行于Java平台（Java虚拟机），并兼容现有的Java程序。它也能运行于CLDC配置的Java ME中。目前还有另一.NET平台的实现，不过该版本更新有些滞后。Scala的编译模型（独立编译，动态类加载）与Java和C#一样，所以Scala代码可以调用Java类库（对于.NET实现则可调用.NET类库）。Scala包括编译器和类库，以及BSD许可证发布。

#### 2、Scala的常用数据类型
注意：在Scala中，任何数据都是对象。

![任何数据都是对象](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTMxODU0MzFjOTg4MTZmOWQucG5n?x-oss-process=image/format,png)


##### (1)数值类型：Byte，Short，Int，Long，Float，Double
* Byte:  8位有符号数字，从-128 到 127
* Short: 16位有符号数据，从-32768 到 32767
* Int：  32位有符号数据
* Long： 64位有符号数据

例如：
```shell
scala> val a:Byte = 10

scala> a+10

scala> val b:Short = 20

scala> a+b
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU4NzQ4OGE4NDY0Yjg2ODIucG5n?x-oss-process=image/format,png)


**注意：在Scala中，定义变量可以不指定类型，因为Scala会进行类型的自动推导。**

##### (2)字符类型和字符串类型：Char和String

对于字符串，在Scala中可以进行插值操作。

```shell
scala> val s1="Hello Movle"

scala> "My Name is ${s1}"

scala> s"My Name is ${s1}"
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEwN2YzY2NlOGRmY2MzM2IucG5n?x-oss-process=image/format,png)


**注意：前面有个s；相当于执行："My Name is " + s1**


##### (3)Unit类型：相当于Java中的void类型

```shell
scala> val f = ()

scala>val f = {}
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU5MmVmOGNkZWM5NzgxZDQucG5n?x-oss-process=image/format,png)

##### (4)Nothing类型：一般表示在执行过程中，产生了Exception
例如，我们定义一个函数如下：

```shell
def myfunction = throw new Exception("a lot error")
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZlMjJkYzFmYzk5YTk1NzQucG5n?x-oss-process=image/format,png)

#### 3.Scala变量的申明和使用
##### (1)使用val和var申明变量
例如：
```shell
scala> val answer = 8 * 3 + 2
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNiOGYzNDQ2NmMyZjllODkucG5n?x-oss-process=image/format,png)

可以在后续表达式中使用这些名称

##### (2)val：定义的值实际是一个常量,var:申明其值是可变的变量

**注意：可以不用显式指定变量的类型，Scala会进行自动的类型推导**

#### 4. Scala的函数和方法的使用
##### (1)可以使用Scala的预定义函数
例如：求两个值的最大值
```shell
scala> import scala.math._

scala> max(1,2)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWUxZWI2ODRkNzZiNDBiYmQucG5n?x-oss-process=image/format,png)

##### (2)也可以使用def关键字自定义函数

语法：
```shell
def 函数名称(参数列表:参数类型):返回类型={
        //函数体
}
```

示例1：

```shell
// 求两个数字的和
def sum(x:Int, y:Int):Int= x+y

sum(1,2)
```
![示例1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU5MjkzNmY4ZmJlMTI3ZGYucG5n?x-oss-process=image/format,png)

示例2:
```shell
//求某个数字的阶乘
def myFactor(x:Int):Int={
  if(x<1)
    1
  else
    x*myFactor(x-1)
}

myFactor(5)
```
![示例2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU1NGQxNGUyMjQ1ZjZkNTYucG5n?x-oss-process=image/format,png)

#### 5.Scala的条件表达式

&nbsp;&nbsp;&nbsp;&nbsp;Scala的if/else语法结构和Java或C++一样。
&nbsp;&nbsp;&nbsp;&nbsp;不过，在Scala中，if/else是表达式，有值，这个值就是跟在if或else之后的表达式的值。

#### 6.Scala的循环

&nbsp;&nbsp;&nbsp;&nbsp;Scala拥有与Java和C++相同的while和do循环
&nbsp;&nbsp;&nbsp;&nbsp;Scala中，可以使用for和foreach进行迭代

##### (1)for的四种写法：
```java
    var list = List("LiMing","May","Mike")

    println("for循环的第一种写法")
    for(s<-list) println(s)

    println("for循环的第二种写法")
    for{
      s <-list
      if(s.length >3)
    }println(s)

    println("for循环的第三种写法")
    for(s <- list if s.length < 3) println(s)

    println("for循环的第四种写法")
    var newList = for{
      s <- list
      s1=s.toUpperCase()
    }yield(s1)
    for(s1<-newList) println(s1)
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJhZTk4ZTNkNTlhMjhhNmMucG5n?x-oss-process=image/format,png)

注意：
*  <-  表示Scala中的generator，即：提取符
* 第三种写法是第二种写法的简写
* 在for循环中，还可以使用yield关键字来产生一个新的集合，for循环的第四种写法

在上面的案例，for循环的第四种写法中，将list集合中的每个元素转换成了大写，并且使用yield关键字生成了一个新的集合。

##### (2)使用while循环：注意使用小括号，不是中括号
```java
    var list = List("LiMing","May","Mike")

    println("******while循环******")
    var i=0
    println("while循环")
    while(i<list.length){
      println(list(i))
      i+=1
    }
```

##### (3)使用do ... while循环
```java
    println("******dowhile循环******")
    var j=0
    do{
      println(list(i))
      j+=1
    }while(j<list.length)
```
![while循环与do while循环](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWY5ZTVkODViZDg5YzY5YWQucG5n?x-oss-process=image/format,png)

##### (4)使用foreach进行迭代

```java
val list = List("LiMing","XiaoHong","PengPeng")

list.foreach(println)   //相当于for(s<-list) println(s )
```
![foreach](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQwNTFjMzhmMTdjM2Q5MmQucG5n?x-oss-process=image/format,png)
注意：在上面的例子中，foreach接收了另一个函数(println)作为值
还有一个循环map

foreach和map的区别：
* foreach没有返回值
* map有返回值

#### 7.Scala函数的参数
##### (1)Scala中，有两种函数参数的求值策略
* Call By Value：对函数实参求值，且仅求一次
* Call By Name：函数实参每次在函数体内被用到时都会求值  所用符号：=>

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY5YTc1MDdmODQ0ZTIyMjkucG5n?x-oss-process=image/format,png)
执行过程对比

```shell
test1  -->	test1(3+4,8)	-->	test1(7,8)	-->	7+7	-->	14
		
test2  -->	test2(3+4,8)	-->	(3+4)+(3+4)	-->	7+7	-->	14
```

**举例**
定义bar函数，其中x 是 call by value , y 是 call by name
```shell
def bar(x:Int,y: => Int) : Int = 1
```
定义一个死循环函数
```shell
def loop() : Int = loop
```	
调用 bar 函数：
```shell
bar(1,loop)    //第一种

bar(loop,1)    //第二种
```	
哪种方式会产生死循环？
答案是：第二种方式
解析：
```
y 每次在函数中用到时，才会被求值，bar函数没有用到y，所以不会调用loop。
x call by value ，对函数参数求值，并且只求一次，所以会产生死循环。
```

##### (2)Scala中的函数参数的类型
* 默认参数：当你没有给参数赋值的时候，就使用默认值。

```shell
//默认参数
def func1(name:String="Tom"):String="Hello"+name

func1()

func1("Marry")
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQxNTlhZDQ4ZDA4OTBjMDIucG5n?x-oss-process=image/format,png)
* 代名参数：当有多个默认参数的时候，通过代名参数可以确定给哪个参数赋值。

```shell
//代名参数
def func2(str:String="good morning",name:String="Tome",age:Int=20)
=str+name+"and the age of "+name+"is"+age

func2()

func2(age=22)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM4NmIzNDMyZmIxOGY3NDYucG5n?x-oss-process=image/format,png)


* 可变参数：类似于java中的可变参数，即 参数数量不固定

```shell
//变长参数：求多个数字的和
def sum(args:Int*){
  var result=0
  for(arg<-args) result+=arg
  result
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVmMDBmNzU4ZjMwY2IzMDkucG5n?x-oss-process=image/format,png)

#### 8.Scala的Lazy值(懒值)

定义：如果常量是lazy的，他的初始化会被延迟，推迟到第一次使用该常量的时候

##### (1)当val被申明为lazy时，它的初始化将被推迟，直到我们首次对它取值。

```shell
val x:Int = 10

lazy val y:Int=x+1

y
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRhY2ViZjIxMDY1MTZiNGMucG5n?x-oss-process=image/format,png)

**举例**：
(a)读一个存在的文件
```shell
lazy val words = scala.io.Source.fromFile("/users/macbook/TestInfo/student.txt").mkString

words
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRhM2Y1ZWUzM2Q3NjU3ZjMucG5n?x-oss-process=image/format,png)

(b)读一个不存在的文件
```shell
val words = scala.io.Source.fromFile("/users/macbook/TestInfo/studen1231312312t.txt").mkString
```
![产生异常](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ2ZmUyMjg3NjQ5YzAzY2YucG5n?x-oss-process=image/format,png)

```shell
lazy val words = scala.io.Source.fromFile("/users/macbook/TestInfo/studen1231312312t.txt").mkString
```
![不会产生异常](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWJlNTNjMmRkZTBkYmQwNTgucG5n?x-oss-process=image/format,png)


#### 9.异常的处理

&nbsp;&nbsp;&nbsp;&nbsp;Scala异常的工作机制和Java或者C++一样。直接使用throw关键字抛出异常。

```shell
throw new Exception("a lot error happened!")
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcxMTJlOWMzMjA1M2M3YjYucG5n?x-oss-process=image/format,png)
#### 10.Scala中的数组
##### (1)Scala数组的类型：
* 定长数组：使用关键字Array

```java
//定长数组
val a = new Array[Int](10)

val b = new Array[String](5)

val c = Array("Tom","Marry","Mike")
```
![定长数组](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEyMmY5YjA2ZGY1MDgyYjUucG5n?x-oss-process=image/format,png)

* 变长数组：使用关键字ArrayBuffer

```java
//一定要先导包
import scala.collection.mutable.ArrayBuffer
val d = ArrayBuffer[Int]()
//在变长数组种添加元素
d+=1
d+=2
d+=3
//在变长数组中添加多个元素
d+=(10,12,13)

//将ArrayBuffer转换为Array
d.toArray
```

![变长数组](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTllNTdmYjVjZDNlYjBiNGIucG5n?x-oss-process=image/format,png)

##### (2)遍历数组

```java
//遍历数组
var a =Array("aa","bb","cc")
//使用for循环进行遍历
for(s <-a) println(s)

//对数组进行转换，新生成一个数组yield
val b = for{
  s<-a
  s1=s.toUpperCase
}yield(s1)

//使用foreach进行循环输出
a.foreach(println)
```
![遍历数组](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNhMzlmYjM0NjMzMDgzYTcucG5n?x-oss-process=image/format,png)

##### (3)Scala数组的常用操作

```java
import scala.collection.mutable.ArrayBuffer

val myArray = Array(1,10,2,3,5,4)

//最大值
myArray.max
//最小值
myArray.min
//求和
myArray.sum
//定义一个变长数组
var myArray1= ArrayBuffer(1,10,2,3,5,4)

//排序
myArray1.sortWith(_>_)
//升序
myArray1.sortWith(_<_)
```
![数组的常用操作](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTllMTM3NmZlYTc3YWYxYmIucG5n?x-oss-process=image/format,png)

##### (4)Scala的多维数组

* 和Java一样，多维数组是通过数组的数组来实现的。
* 也可以创建不规则的数组，每一行的长度各不相同。

```java
//定义一个固定长度的二维数组

val matrix = Array.ofDim[Int](3,4)

matrix(1)(2)=10

matrix
```
![固定长度的二维数组](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkyYTAzMmNjZmNjMzE0OTcucG5n?x-oss-process=image/format,png)
```java
//定义一个二维数组，其中每个元素是一个一维数组，其长度不固定
val triangle = new Array[Array[Int]](10)

for(i<-0 until triangle.length)
  triangle(i)=new Array[Int](i+1)

triangle
```
![长度不固定的二维数组](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWExNTk4ZTk1M2YzNzBjNzAucG5n?x-oss-process=image/format,png)

#### 11.映射
##### (1).映射就是Map集合，由一个(key,value)组成。

-> 操作符用来创建

例如：
```java
val scores = Map("Alice" -> 10,"Bob" -> 3,"Cindy" -> 8)
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkwNzA4MGI3MDUxMzQ3ZjkucG5n?x-oss-process=image/format,png)

##### (2)映射的类型分为：不可变Map和可变Map

```java
//不可变的Map
val math = scala.collection.immutable.Map("Alice"->80,"Bob"->95,"Mary"->70)

//可变的Map
val english = scala.collection.mutable.Map("Alice"->80,"Bob"->95,"Mary"->70)

val chinese = scala.collection.mutable.Map(("Alice",80),("Bob",95),("Mary",70))
```
![不可不map和可变map](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU1YmZmODFjMDNkMmEwMjQucG5n?x-oss-process=image/format,png)

##### (3)映射的操作
* 获取映射中的值

```java
//获取Map中的值
chinese("Bob")


//需要判定是否存在才能够获得该映射的值
if(chinese.contains("Alice")){
  chinese("Alice")
}else{
  -1
}
```
![获取映射中的值](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFiNzE4YjllNzUwNGU3YzAucG5n?x-oss-process=image/format,png)

```java
chineses.getOrElse("To123123m",-1)

chineses.get("dlfsjldkfjlsk")

chineses.get("Tom")
```
使用get方法不会报错

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVlNzJiZTQyNjg1ZDdiMDYucG5n?x-oss-process=image/format,png)


* 更新映射中的值（必须是可变Map）

```java
//更新Map中的值
chinese("Bob")=100

chinese

//往Map中添加新的元素
chinese +="Tom"->85

//移除Map中的元素
chinese -="Bob"
```
![更新映射中的值](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU1NjBhNDcwOTc5ZTAzMTcucG5n?x-oss-process=image/format,png)


* 迭代映射

可以使用 for foreach
```java
//迭代Map:使用for或者foreach
for(s <-chinese) println(s)

chinese.foreach(println)
```
![迭代映射](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVlZTk0ZDIzMDM3MTFkODQucG5n?x-oss-process=image/format,png)

#### 12.元组（Tuple）

##### (1)元组是不同类型的值的聚集。
例如：
```java
val t = (1, 3.14, "Fred") // 类型为Tuple3[Int, Double, java.lang.String]
```
这里：Tuple是类型，3是表示元组中有三个元素。

##### (2)Tuple的声明：
```java
val t1 = Tuple3(1,0.3,"Hello")
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ3OTY2MmQ3NThkMGJmOWYucG5n?x-oss-process=image/format,png)
##### (3)元组的访问和遍历：

```java
//定义tuple
val t1 = (1,2,"Tom")

val t2 = new Tuple4("Marry",3.14,100,"Hello")

//访问tuple中的组员
t2._1
t2._2
t2._3
t2._4

//遍历Tuple：foreach
t2.productIterator.foreach(println)
```
![元组](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU3MzZmNWQ1ZjExMDIwOTgucG5n?x-oss-process=image/format,png)

遍历Tuple中的元素
注意：Tuple中没有提供foreach函数，我们需要使用 productIterator
	
遍历分成2个步骤：
* 使用productIterator 生成迭代器
* 遍历

**注意：要遍历Tuple中的元素，需要首先生成对应的迭代器。不能直接使用for或者foreach**

#### 13.Scala中的文件操作	

类似于java中的io操作
举例：

##### (1)读取文件
```java
def main(args: Array[String]): Unit = {
    //读取行
    val source = fromFile("/users/macbook/TestInfo/student.txt")

    /**
     * 1、将整个文件作为字符串输出
     */
    println("--------mkString---------")
    println(source.mkString)
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ0MTE4MDM1ZDBhMmQ0YzMucG5n?x-oss-process=image/format,png)
```java
def main(args: Array[String]): Unit = {
    //读取行
    val source = fromFile("/users/macbook/TestInfo/student.txt")

    /**
     * 2、将文件的每一行读入 java BufferedReader readLine方法类似
     */
    println("--------lines---------")
    val lines = source.getLines()
    lines.foreach(println)
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU2M2MzZjM1ZmQxYTkwMTUucG5n?x-oss-process=image/format,png)

##### (2)读取二进制文件
```java
def main(args: Array[String]): Unit = {
    /**
     * 读取二进制文件：
     * scala中并不支持直接读取二进制
     * 可以通过调用java的InputStream来进行读入
     */
    println("--------Read Bytes---------")
    var file = new File("/users/macbook/TestInfo/hudson.war")
    //构建一个inputstream
    var in = new FileInputStream(file)
    //构造buffer
    var buffer = new Array[Byte](file.length().toInt)
    //读取
    in.read(buffer)
    println(buffer.length)
    //关闭
    in.close()
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFjYzNkYzlkNjViYmQzMTUucG5n?x-oss-process=image/format,png)

##### (3)从url中获取信息

```java
def main(args: Array[String]): Unit = {
   println("--------fromURL---------")
        var source2 = fromURL("https://www.baidu.com","UTF-8")
        println(source2.mkString)
}
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkwMDc3ODQ2NDFjMmViZjQucG5n?x-oss-process=image/format,png)

##### (4)写入文件

```java
def main(args: Array[String]): Unit = {
  /**
     * 写入文件
     */
    println("--------Write File---------")
    var out = new PrintWriter("/users/macbook/TestInfo/insert_0601.txt")
    for (i <- 0 until 10) out.println(i)
    out.close()
}
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNiNDhjZmU2ZmY0NDc4NTkucG5n?x-oss-process=image/format,png)
