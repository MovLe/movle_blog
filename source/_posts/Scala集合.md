---
title: 'Scala集合'
date: 2020-03-06 11:50:00
tags: Scala
categories: Scala
---


#### 1.可变集合和不可变集合
##### (1)可变集合:
##### (2)不可变集合：
* 集合从不改变，因此可以安全地共享其引用。
* 甚至是在一个多线程的应用程序当中也没问题。

```java
//不可变的集合
val math = scala.collection.immutable.Map("Alice"->80,"Bob"->90,"Mary"->85)

//可变的集合
val english = scala.collection.mutable.Map("Alice"->80,"Bob"->90,"Mary"->85)
val chinese = scala.collection.mutable.Map("Alice"->80,"Bob"->90,"Mary"->85)
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI5NGZiMmViYTFhMTZjMzkucG5n?x-oss-process=image/format,png)

##### (3)集合的操作：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFjMjE3Nzk2YjI3NzE2YTMucG5n?x-oss-process=image/format,png)
**a.获取集合中的值**

```java
chinese.get("Bob")

chinese.get("Tomdfsdfd")

chinese("Bob")

chinese.getOrElse("Tomsdlkfjlskdjfls",-1)

chinese("Tomdfsdfd")
```

![获取集合中的值](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ0ODdlMzdlNjhkNmZjOTIucG5n?x-oss-process=image/format,png)

**b.更新集合中的值**：注意：必须是一个可变集合

```java
chinese

chinese("Bob")=0

chinese

math

math("Bob")=0
```
![更新集合元素](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRkZGE0NmM1NGI2ZTQwMzAucG5n?x-oss-process=image/format,png)

**c.添加新元素**

```java
chinese += "Tom"->85
```
![添加新元素](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQyYWE5NGZiN2FiMGNjNmUucG5n?x-oss-process=image/format,png)
**d.移除元素**
```
chinese -= "Bob"
```
![移除元素](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTFiY2IwMTlmODgyOTlkNjYucG5n?x-oss-process=image/format,png)
#### 2.列表
##### (1)不可变列表(List)
```java
//不可变列表：List
//字符串列表
val namelist = List("Bob","Mary","Mike")
//整数列表
val intList = List(1,2,3,4,5)
//空列表
val nullList:List[Nothing] = List()
//二维列表
val dim:List[List[Int]] = List(List(1,2,3),List(4,5,6))
```

![不可变列表](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNlZTJlNTdjOTlmZjM2MjIucG5n?x-oss-process=image/format,png)

**不可变列表的相关操作**：
a.*head*：列表中的第一个元素
```java
//输出列表中的值：head,tail,isEmpty
println("第一个人的名字："+namelist.head)
```
b.*tail*:不是返回最后一个元素，而是返回除去第一个元素后，剩下的元素列表
```java
println(namelist.tail)
```
c.*isEmpty*:判断列表是否为空
```java
println("列表是否为空："+namelist.isEmpty)
```
![不可变列表的相关操作](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQzNWYwZTliMjQwZjVhOGUucG5n?x-oss-process=image/format,png)

##### (2)可变列表(LinkedList)：scala.collection.mutable.LinkedList
LinkedList和不可变列表List类似，只不过我们可以修改列表中的值

```java
//可变列表：
val myList = scala.collection.mutable.LinkedList(1,2,3,4,5)
//操作：将上面可变列表中的每个值乘以2
//列名 elem
//定义一个指针指向列表的开始
var cur = myList

//Nil:代表Scala中的null
while(cur != Nil){
  cur.elem = cur.elem*2
  //将指针指向下一个元素
  cur = cur.next
}
//查看结果
println(myList)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg3ZTNkZTQwZDFkODBmM2IucG5n?x-oss-process=image/format,png)
```java
myList

myList.map(_*2)
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAzZTllNTA1MzhjYjczNzYucG5n?x-oss-process=image/format,png)
移除元素

```java
myList.drop(2)

myList.dropWhile(_%2!=0)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ0NTcwOGFlMjVmMTYwNWMucG5n?x-oss-process=image/format,png)

dropWhile   去除当前数组中，符合条件的元素，碰到第一个不满足条件的元素，就结束。


#### 3.序列
常用的序列有：Vector和Range

##### (1)Vector是ArrayBuffer的不可变版本，是一个带下标的序列
```java
//Vector:为了提高list列表随机存取的效率而引入的新的集合类型
//支持快速的查找和更新
val v= Vector(1,2,3,4,5,6)

//返回的是第一个满足条件的元素
v.find(_>3)
v.updated(2,1000)
```
![Vector](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVkNWI2Y2IyNmJhZGUxZjIucG5n?x-oss-process=image/format,png)
##### (2)Range表示一个整数序列

```java
//Range：有序的通过空格分割的Int序列
//以下几个例子Range是一样

Range(0,5)
println("第一种写法："+Range(0,5))
println("第二种写法："+(0 until 5))
println("第三种写法："+(0 to 4))

//两个range可以相加
('0' to '9')++('A' to 'Z')

//可以将Range转换为List
1 to 5 toList
```
![Range](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAwOTQwOTgxZjMzZGM5OTAucG5n?x-oss-process=image/format,png)

#### 4. 集(Set)和集的操作
* 集Set是不重复元素的集合
* 和列表不同，集并不保留元素插入的顺序。默认是HashSet

##### (1)示例1：创建集

```java
//a.**创建一个Set**
var s1 = Set(2,0,1)

//往s1中添加一个重复元素
s1+1

//往s1中添加一个不重复元素
s1+100

//创建一个LinkedHashSet,一定要先import下面这个，否则会保错
import collection.mutable
var weeksday = mutable.LinkedHashSet("星期一","星期二","星期三","星期四")

//创建一个排序的集
var s2 = mutable.SortedSet(1,2,3,10,4)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ2NDZkM2RkZWZlM2Q4YzAucG5n?x-oss-process=image/format,png)

##### (2)示例2：集的操作

a.**添加**

```java
weeksday+"星期五"
```
b.**判断元素是否存在**
```java
weeksday contains("星期二")
```
c.**判断一个集是否是另一个集的子集**
```java
Set("星期二","星期四","星期日") subsetOf(weeksday)
```
d.**集的运算，union并集，intersect交集，diff差集**
```java
var set1=Set(1,2,3,4,5)

var set2=Set(5,6,7,8,9,10)
//并集：集合相加，去掉重复元素
set1 union set2
//intersect交集
set1 intersect set2
//差集
set1 diff set2
```
![集的操作](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWExNjc4MjZiNTVlYTAzNDcucG5n?x-oss-process=image/format,png)

#### 5.模式匹配
##### (1)Scala有一个强大的模式匹配机制，可以应用在很多场合：
* switch语句
* 类型检查

##### (2)Scala还提供了样本类(case class)，对模式匹配进行了优化

##### (3)模式匹配示例：
* 更好的switch

```java
//更好的switch
var sign =0

var ch1 = '-'

ch1 match{
  case '+' => sign = 1
  case '-' =>sign = -1
  case _ =>sign = 0
}
println(sign)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk3ZTZkMmJhZDIyNzcwZjMucG5n?x-oss-process=image/format,png)

##### (4)Scala的守卫:匹配某种类型的所有值

```java
//scala的守卫：匹配某种类型的所有值
var ch2='6'
var digit:Int = -1
ch2 match{
  case '+' => println("这是一个+")
  case '-' => println("这是一个-")
  case _ if Character.isDigit(ch2) => digit = Character.digit(ch2,10)
 case _ => println("其他类型")
}

println("Digit:"+digit)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTcyMzQ4ZDE1NjZiMjZhY2YucG5n?x-oss-process=image/format,png)

##### (5)模式匹配中的变量

```java
//模式匹配的变量
var str3 = "Hello World"

str3(7) match{
  case '+' => println("这是一个+")
  case '-' => println("这是一个+")
  case ch => println("这是字符："+ch)
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWIwMmE5MjQ0ODk4MzVjYjcucG5n?x-oss-process=image/format,png)

##### (6)类型模式
```java
//类型模式
var v4:Any =100
v4 match{
  case x:Int => println("这是一个整数："+x)
  case s:String => println("这是一个字符串："+s)
  case _ => println("这是其他类型")
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBmZTZhZjNkZmUzMzBkZmYucG5n?x-oss-process=image/format,png)

##### (7)匹配数组和列表
```java
//匹配数组和列表
var myArray = Array(1,2,3)
myArray match{
  case Array(0) => println("0")
  case Array(x,y) =>println("这是数组包含的两个元素，和是："+(x+y))
  case Array(x,y,z) => println("这是数组包含的三个元素，乘积是："+(x*y*z))
  case Array(x,_*) => println("这是一个数组")
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBkNjc1NWI2NWFlZjYwZjMucG5n?x-oss-process=image/format,png)

```java
var myList = List(1,2,3)
myList match{
  case List(0) => println("0")
  case List(x,y) =>println("这是列表包含的两个元素，和是："+(x+y))
  case List(x,y,z) =>println("这是列表包含的三个元素，乘积是："+(x*y*z))
  case List(x,_*) => println("这个列表包含多个元素")
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg2N2FlYmVmZWIzMWFiNGMucG5n?x-oss-process=image/format,png)

#### 6.样本类(CaseClass)
&nbsp;&nbsp;&nbsp;&nbsp;简单的来说，Scala的case class就是在普通的类定义前加case这个关键字，然后你可以对这些类来模式匹配。

**case class带来的最大的好处是它们支持模式识别。**

&nbsp;&nbsp;&nbsp;&nbsp;首先，回顾一下前面的模式匹配：

![普通的模式匹配](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJhNjA1ZjUwMmVhMmM2YzQucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;其次，如果我们想判断一个对象是否是某个类的对象，跟Java一样可以使用isInstanceOf

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE4NWY1ZTYzNjFjYjcyYjIucG5n?x-oss-process=image/format,png)

下面这个好像有点问题

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTdjZmYxYWE4NThhY2RlMzEucG5n?x-oss-process=image/format,png)


最后，在Scala中有一种更简单的方式来判断，就是case class

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM2ZThmNDIyYTUxNDNlZjEucG5n?x-oss-process=image/format,png)

**注意：需要在class前面使用case关键字**



