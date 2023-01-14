---
title: 'Scala高级特性'
date: 2020-03-07 11:50:00
tags: Scala
categories: Scala
---

### 一.范型
#### 1.什么是泛型类

&nbsp;&nbsp;&nbsp;&nbsp;和Java或者C++一样，类和特质可以带类型参数。在Scala中，使用方括号来定义类型参数
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRhNTBlYzNkYmIyYjY0YmEucG5n?x-oss-process=image/format,png)

测试程序：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRkZTliNDNlY2MzODhkYzQucG5n?x-oss-process=image/format,png)

#### 2.什么是泛型函数

&nbsp;&nbsp;&nbsp;&nbsp;函数和方法也可以带类型参数。和泛型类一样，我们需要把类型参数放在方法名之后。

**注意：这里的ClassTag是必须的，表示运行时的一些信息，比如类型**
```java
import scala.reflect.ClassTag

def mkIntArray(elems:Int*) = Array[Int](elems:_*)

mkIntArray(1,2,3,100)

def mkStringArray(elems:String*) = Array[String](elems:_*)

mkStringArray("Mike","Tom","Mary")

def mkArray[T:ClassTag](elems:T*) = Array[T](elems:_*)

mkArray(1,2,3,5,8)

mkArray("Tom","Marry")
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI1ZGFmOWJlYWYzMjg5NWYucG5n?x-oss-process=image/format,png)

#### 3.上界和下界：Upper Bounds 与 Lower Bounds

##### (1)作用：规定泛型的取值范围：

**举例：定义一个范型:T**
```txt
类的继承关系   A---->B----->C----->D  箭头指向子类
可以规定T的取值范围    D  <:  T   <:  B
T 的取值范围只能是 B C D
				
<:  就是上下界表示方法
```

##### (2)定义：
* 上界：s <: T ,规定了S的类型必须是T的子类或本身

* 下界：u >: T ,规定了U的类型必须是T的父类或本身

##### (3)一个简单的例子：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE1MTYzM2UxZTNjN2Y5MWUucG5n?x-oss-process=image/format,png)

##### (4)一个复杂一点的例子（上界）：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEwNTAzNDUzYTY4YTdlNzYucG5n?x-oss-process=image/format,png)

##### (5)再来看一个例子：

```java
def addTwoString[T<:String](x:T,y:T) = x + " **** " + y

addTwoString("Hello","123")

addTwoString(1,2)
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRkZjUzY2MyZDUwODBhYjkucG5n?x-oss-process=image/format,png)

报错原因：Int不是String类型。
		
解决：1和2转换成字符串再调用

```java
addTwoString(1.toString,2.toString)
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY0YmMyNWFjMTJiNWJmMTUucG5n?x-oss-process=image/format,png)

另外：可以使用 视图界定 来解决这个问题。

#### 4.视图界定(View bounds)
&nbsp;&nbsp;&nbsp;&nbsp;它比 <: 适用的范围更广，除了所有的子类型，还允许隐式转换过去的类型。用 <% 表示。**尽量使用视图界定，来取代泛型的上界，因为适用的范围更加广泛**

**示例**：
##### (1)上面写过的一个列子。这里由于T的上界是String，当我们传递100和200的时候，就会出现类型不匹配。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVjMWY2ZjkxYmM4YTA3MTkucG5n?x-oss-process=image/format,png)
##### (2)但是100和200是可以转成字符串的，所以我们可以使用视图界定让addTwoString方法接收更广泛的数据类型，**即：字符串及其子类、可以转换成字符串的类型。**

注意：使用的是 <% 
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTVhYmEyM2VhM2YyYTQ4YmUucG5n?x-oss-process=image/format,png)
##### (3)但实际运行的时候，会出现错误：
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg5MGQyZDEyNTY0YWIyZGEucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;这是因为：Scala并没有定义如何将Int转换成String的规则，所以要使用视图界定，我们就必须创建转换的规则。

##### (4)创建转换规则

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTRlYzhmZGFiOTBlN2MyM2EucG5n?x-oss-process=image/format,png)
##### (5)运行成功
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE3NWNiNzMyZTRhYmFkN2IucG5n?x-oss-process=image/format,png)
#### 5.协变和逆变
##### (1)协变：
&nbsp;&nbsp;&nbsp;&nbsp;Scala的类或特征的范型定义中，如果在类型参数前面加入+符号，就可以使类或特征变为协变了。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM3MWFiNzhlNmQzYTg2N2EucG5n?x-oss-process=image/format,png)

##### (2)逆变：
&nbsp;&nbsp;&nbsp;&nbsp;在类或特征的定义中，在类型参数之前加上一个-符号，就可定义逆变范型类和特征了。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFkMTQ1Y2EzNjg2ZGY2YjYucG5n?x-oss-process=image/format,png)


**总结一下：**
Scala的协变：泛型变量的值可以是**本身类型**或者其**子类**的类型

Scala的逆变：泛型变量的值可以是**本身类型**或者其**父类**的类型

### 二.隐式转换

#### 1.隐式转换函数

&nbsp;&nbsp;&nbsp;&nbsp;所谓隐式转换函数指的是以**implicit**关键字申明的带有**单个参数**的函数。

##### (1)前面讲视图界定时候的一个例子：
```java
implicit def int2String(n:Int):String = {n.toString}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTkyNTY5YTQ3N2NhZTgyYzUucG5n?x-oss-process=image/format,png)

##### (2)再举一个例子：把Fruit对象转换成了Monkey对象

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk0MDhhZDNiYTEyZjU2OGIucG5n?x-oss-process=image/format,png)

#### 2.隐式参数
&nbsp;&nbsp;&nbsp;&nbsp;使用implicit申明的函数参数叫做隐式参数。我们也可以使用隐式参数实现隐式的转换

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTViZjc5N2M1OTdiN2M1MjcucG5n?x-oss-process=image/format,png)
##### (1)区别：
* 参数：定义函数的时候，会接收参数
* 隐式参数：使用implicit修饰的函数参数

##### (2)作用：当你去调用函数的时候，如果没有给函数传递参数值，就会使用隐式参数

##### (3)举例：定义一个带有隐式参数的函数

```java
def testParam(name:String) = println("The value is " + name)

def testParam(implicit name:String) = println("The value is " + name)

implicit val name:String = "AAAAAA"

testParam("dfsfdsdf")

testParam
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYyMGIyMTFiZTM4YjQ2ZjUucG5n?x-oss-process=image/format,png)

#### 3.隐式类
所谓隐式类： 就是对类增加implicit 限定的类，其作用主要是**对类的功能加强**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI2ZDFlYWQxZjE4NTc5ZDgucG5n?x-oss-process=image/format,png)

### 三.Actor并发模型
#### 1.Java并发与Scala并发区别：

##### (1)Java中的并发编程是基于 共享数据 和 加锁 的一种机制。synchronized关键字，锁共享数据

##### (2)Scala中的并发：Scala中的Actor是一种 不共享数据 ，依赖于 消息传递 的一种并发编程模式。
&nbsp;&nbsp;&nbsp;&nbsp;避免了死锁、资源争夺等情况。

#### 2.如果 Actor A 和 Actor B 要相互沟通，步骤如下：
##### (1)A是要给B传递一个信息，B会有一个收件箱，然后B会不断地循环查询自己的收件箱。

##### (2)若B看见A发来的消息，B就会解析A的消息，并执行。
&nbsp;&nbsp;&nbsp;&nbsp;使用 case class 来区分 消息类型

##### (3)处理完之后，有可能将处理的结果发送给A。

&nbsp;&nbsp;&nbsp;&nbsp;在学习Actor时，一定要区分，发消息 与回复消息。这两个在代码中实现是不同的。

AKKA 负责来回传递消息

#### 3.示例代码：
添加配置：
pom.xml
```xml
<properties>
        <actor.version>2.5.4</actor.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.typesafe.akka</groupId>
            <artifactId>akka-actor_2.11</artifactId>
            <version>${actor.version}</version>
        </dependency>
        <dependency>
            <groupId>com.typesafe.akka</groupId>
            <artifactId>akka-remote_2.11</artifactId>
            <version>${actor.version}</version>
        </dependency>
        <dependency>
            <groupId>com.typesafe.akka</groupId>
            <artifactId>akka-http_2.11</artifactId>
            <version>10.0.9</version>
        </dependency>
        <dependency>
            <groupId>com.typesafe.akka</groupId>
            <artifactId>akka-protobuf_2.11</artifactId>
            <version>${actor.version}</version>
        </dependency>
    </dependencies>
```
##### (1)示例1:
```java
package Test01

import akka.actor.{Actor, ActorSystem, Props}

class HelloActor extends Actor{
  def receive = {
    /**
     * 使用 case 来处理不同类型的信息
     */
    case "Hello" => println("你好！")
    case _ => println("你是？")
  }
}
object DemoActor {

  def main(args: Array[String]): Unit = {
    //新建一个ActorSystem
    val system = ActorSystem("HelloSystem")

    val helloActor = system.actorOf(Props( new HelloActor),name="helloactor")

    //给 helloactor 发送消息

    helloActor ! "Hello123123123"
  }
}

```

![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY0MTQ1Y2Y3MGE5MGNkYWMucG5n?x-oss-process=image/format,png)

##### (2)示例2:

```java
import akka.actor._
/**
  *
  * 两个Actor 相互发消息
  *
  * Ping   Pong
  */
//定义消息类型
case object PongMessage
case object PingMessage
case object StopMessage
case object StartMessage

class Pong(ping:ActorRef) extends Actor{
  var count = 0
  def incrementAndPrint { count += 1; println("count + 1 and pong")}
  def receive = {
  case PingMessage =>
    if (count > 9){
      sender ! StopMessage
      println("count > 9")
      context.stop(self)
    } else {
      println("Receive PingMessage")
      incrementAndPrint
      sender ! PongMessage

    }

  case StartMessage =>
    println("Receive StartMessage")
      //发送一个消息
      ping ! PongMessage
  }
}


class Ping extends Actor{
  def receive = {
    case PongMessage =>
      println("Receive PongMessage")
      //回复一个消息
      sender ! PingMessage
    case StopMessage =>
      println("Stop Message")
      context.stop(self)
      //context.system.finalize()
  }
}
object Demo2 {
  def main(args: Array[String]): Unit = {
    //pong ! StartMessage
    val system = ActorSystem("PingPongSystem")
    val ping = system.actorOf(Props[Ping],name="ping")
    val pong = system.actorOf(Props(new Pong(ping)),name="pong")
    pong ! StartMessage
  }
}
```
![结果](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU3ZGIwY2Q5NWNlYzljOWIucG5n?x-oss-process=image/format,png)
