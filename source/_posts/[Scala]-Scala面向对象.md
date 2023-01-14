---
title: '[Scala]-Scala面向对象'
date: 2020-03-05 17:50:00
tags: Scala
categories: Scala
---


#### 1.面向对象的基本概念
把数据及对数据的操作方法放在一起，作为一个相互依存的整体——对象

面向对象的三大特征：
* 封装: 类 class。把属性和操作属性的方法，放在一起
* 继承
* 多态

#### 2.类的定义
简单类和无参方法：

```java
class Counter{
  private var value =0;
  
  def increment() { value +=1};
  def current() = value;

}
```
案例：注意没有class前面没有public关键字修饰。

```java
//scala中类的定义
class Student1{
    //定义属性
    private var stuName:String = "Tom"
    private var stuAge:Int = 20
    //成员方法
    def getStuName():String = stuName
    def setStuName(newName:String) = this.stuName=newName
    def getStuAge():Int = stuAge
    def setStuAge(newAge:Int) = this.stuAge=newAge
}
```
如果要开发main方法，需要将main方法定义在该类的伴生对象中，即：object对象中

```java
/**
  * 测试student1类，创建main函数
  *
  * 注意：object和class名字可以不一样的
  * 如果oject和class的名字相同，object成为该class的 伴生对象
  *
  */
//创建Student1的伴生对象
object Student1{

    def main(args:Array[String]){
        
    //创建一个学生对象
    var s1 = new Student1

    //访问属性并输出
    println(s1.getStuName() + "\t" + s1.getStuAge())

    //访问set方法
    s1.setStuName("Mary")
    s1.setStuAge(22)
    println(s1.getStuName() + "\t" + s1.getStuAge())

    /**
      * 直接访问私有属性
      *
      * 上面定义中，属性都是private的
      * 如果属性是private的，在类的外部，是不能访问的
      *
      */
    println("------------直接访问私有属性-----------")
    println(s1.stuId +"\t"+ s1.stuName +"\t"+ s1.age)

    /**
      * 注意：我们可以直接访问类的私有成员，为什么？
      * s1.stuId +"\t"+ s1.stuName +"\t"+ s1.age
      * 使用 . 方式访问的
      *
      * scala中属性的get set 方法：
      *
      * 1、当一个属性是private的时候，scala会自动为其生成对应的get set 方法
      * s1.stuId 访问的是scala中，自动为stuId生成的get方法，并且该方法的名字为stuId
      *
      * 2、如果值希望scala生成get方法，不生成set方法，可以将其定义为常量
      * 因为常量的值不可改变
      *
      * 3、如果希望属性，不能被外部访问（希望scala不要生成set get 方法），使用 private [this] 关键字
      *
      */
    }
}
```

#### 3.属性的getter和setter方法
##### (1)当定义属性是private时候，scala会自动为其生成对应的get和set方法
```java
private var stuName:String = "Tom"
```
* get方法: stuName    ----> s2.stuName() 由于stuName是方法的名字，所以可以加上一个括号
* set方法: stuName_=  ----> stuName_= 是方法的名字

##### (2)定义属性：
private var money:Int = 1000 希望money只有get方法，没有set方法？？

解决办法：将其定义为常量private val money:Int = 1000

##### (3) private[this]的用法：
该属性只属于该对象私有，就不会生成对应的set和get方法。如果这样，就不能直接调用，例如：s1.stuName ---> 错误

#### 4、内部类(嵌套类)
&nbsp;&nbsp;&nbsp;&nbsp;我们可以在一个类的内部在定义一个类，如下：我们在Student类中，再定义了一个Course类用于保存学生选修的课程。

```java
import scala.collection.mutable.ArrayBuffer
//嵌套类，内部类
class Student3{
    //定义一个内部类，记录学生选修的课程信息
    class Course(val courseName:String,val credit:Int){
      //定义方法
    }

    //属性
    private var stuName:String = "Tom"
    private var stuAge:Int = 20
    //定义一个ArrayBuffer记录该学生选修的所有课程
    private var courseList = new ArrayBuffer[Course]()

    //定义方法往学生信息中添加新的课程
    def addNewCourse(cname:String,credit:Int){
        //创建新的课程
        var c = new Course(cname,credit)

        //将课程加入list
        courseList +=c
    }
}
```
测试程序：

```java
Object Student3{
    def main(args:Array[String]){
        //创建学生对象
        var s3 = new Student3

        //给该学生添加新的课程
        s3.addNewCourse("Chinese",2)
        s3.addNewCourse("English",3)
        s3.addNewCourse("Math",4)    

        //输出
        println(s3.stuName+"\t"+s3.stuAge)
        println("********选修的课程********")

        for(s <- s3.courseList) println(s.courseName+"\t"+s.credit)
    }
}
```

#### 5.类的构造器
类的构造器分为：主构造器、辅助构造器

##### (1)主构造器：和类的声明结合在一起，只能有一个主构造器
Student4(val stuName:String,val stuAge:Int)

* (a) 定义类的主构造器：两个参数
* (b) 声明了两个属性：stuName和stuAge 和对应的get和set方法


```java
class Student4(val stuName:String,val stuAge:Int){
}

object Student4{
    def main(args:Array[String]){
        //创建Student4的一个对象
        var s4 = new Student4("Tom",20)

        println(s4.stuName+"\t"+s4.stuAge)        //调用了主构造器
    }
}
```

##### (2)辅助构造器：可以有多个辅助构造器，通过关键字this来实现

```java
class Student4(val stuName:String,val stuAge:Int){
    //定义辅助构造器
    def this(age:Int){
        //调用主构造器
        this("no name",age)
    }
}
object Student4{
    def main(args:Array[String]){

        //创建一个新的Student4对象，并调用辅助构造器
        var s42 = new Student4(25)

        println(s42.stuName+"\t"+s42.stuAge)
    }
}
```

主构造器和辅助构造器：
```java
class Student3(var stuName : String , var age:Int) {
  //属性
  private var gender:Int = 1

  //定义一个辅助构造器，辅助构造器可以有多个
  //辅助构造器就是一个函数，只不过这个函数的名字叫 this

  def this (age : Int){
    this("Mike",age) // new Student3("Mike",age)
    println("这是辅助构造器")
  }

  def this (){
    this(10)// new Student3("Mike",10)
    println("这是辅助构造器2")
  }

}

object  Student3{
  def main(args: Array[String]): Unit = {
    //使用主构造器 创建学生对象
    var s1 = new Student3("Tom",20)
    println(s1.stuName+"\t"+s1.age)

    //使用辅助构造器
    var s2 = new Student3(20)
    s2.gender = 0
    s2.stuName = "have name"
    println(s2.stuName+"\t"+s2.age+"\t"+s2.gender)
  }
}
```

#### 6.Scala中的Object对象

##### (1)什么是object对象？
* Object中的内容都是静态的
* scala中，没有static关键字，所有static都定义在object中
* 如果class的名字，跟object的名字一样，就把这个object叫做类的伴生对象。

注意：main函数需要写在object中，但是不一定必须写在伴生对象中。

下面是Java中的静态块的例子。在这个例子中，就对JDBC进行了初始化。
java:
```java
static{
    try{
        Class.forName(driver);
    } catch(ClassNotFoundException e){
          throw new ExceptionInInitializerError(e);
    }
}
```

**而Scala中的Object就相当于Java中静态块**

**Object对象的应用**
##### (1)单例对象：使用object来实现单例模式：一个类只有一个对象

```java
//利用object对象实现单例模式
object CreditCard{

    //变量保存信用卡号
    private [this] var creditCardNumber:Long = 0

    //产生新的卡号
    def generateNewCCNumber():Long = {
        creditCardNumber += 1
        creditCardNumber
    }

    //测试程序
    def main(args:Array[String]){
        //产生新的卡号
        println(CreditCard.generateNewCCNumber())
        println(CreditCard.generateNewCCNumber())
        println(CreditCard.generateNewCCNumber())
        println(CreditCard.generateNewCCNumber())
    }
}
```
##### (2)使用APP对象：可以省略main方法；需要从父类App继承。

```java
//使用应用程序对象：可以省略main方法
object HelloWorld extends App{

//    def main(args:Array[String]){
//        
//    }
//这里的main可以不写，相当于下面的代码是在main方法中执行的

    println("Hello World")

    //如何取得命令行的参数
    if(args.length>0){
        println(args(0))
    }else{
        println("no argument")
    }
}
```

#### 7. Scala中的apply方法
遇到如下形式的表达式时，apply方法就会被调用：
```java
Object(参数1,参数2,......,参数N)
```
通常，这样一个apply方法返回的是伴生类的对象；其作用是为了省略new关键字

```java
var marry = Array(1,2,3)
//这里没有用new关键字，其实就是用了apply方法
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU0Y2Q1NjcwZTc1NzZjZTEucG5n?x-oss-process=image/format,png)
##### (1)Object的apply方法举例：

```java
//object的apply方法
class Student5(val stuName:String){

}

object Student5{
    def apply(stuName:String) = {
        println("******Apply in Object***********")
    }

    def main(args:Array[String]){
        //创建一个Student5的对象
        var s51 = new Student5("Tom")
        println(s51.stuName)
        
        //创建一个Student5的对象
        var s52 = new Student5("Tom")
        println(s52.stuName)
    }
}
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTE3Mjg1YWQxNDllYjgzYmIucG5n?x-oss-process=image/format,png)


* 使用apply方法，让程序更加简洁。
* apply方法必须写在伴生对象中。

#### 8.Scala中的继承
Scala和Java一样，使用extends关键字扩展类。
##### (1)案例一：Employee类继承Person类

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTA1ZDU0OWFlZWJiODA5MTEucG5n?x-oss-process=image/format,png)


##### (2)案例二：在子类中重写父类的方法

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTI4M2RkYWNjYTE3NjkxMWYucG5n?x-oss-process=image/format,png)
##### (3)案例三：使用匿名子类
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg4MmRiYTU1ZTE2OWNiMmEucG5n?x-oss-process=image/format,png)



##### (4)案例四：使用抽象类。抽象类中包含抽象方法，抽象类只能用来继承。
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk5NGM2MGQ0ZDQxODY1ZTcucG5n?x-oss-process=image/format,png)
##### (5)案例五：使用抽象字段。抽象字段就是一个没有初始值的字段
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRmNjRhYjVlMjk1MDc1OWEucG5n?x-oss-process=image/format,png)
#### 9.Scala中的trait（特质）
trait就是抽象类。trait跟抽象类最大的区别：trait支持多重继承

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTEwMzBlYWVmYjAzNmZhZDQucG5n?x-oss-process=image/format,png)

#### 10.包的使用
&nbsp;&nbsp;&nbsp;&nbsp;Scala的包和Java中的包或者C++中的命名空间的目的是相同的，管理大型程序中的名称

Scala中包的定义和使用：
##### (1)包的定义

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTU0MzNhMzhkZTViOGVlMTcucG5n?x-oss-process=image/format,png)


##### (2)包的引入：Scala中依然使用import作为引用包的关键字，例如
```java
import com.my.io.XXX     //可以不写XXX的全路径
import com.my.io._       //引用import com.my.io下所有的类型
import com.my.io.XXX._   //引用import com.my.io.XXX的所有成员
```
##### (3)而且Scala中的import可以写在任意地方
```java
def method(fruit :Fruit){
    import fruit._
    println(name)
}
```

#### 11.包对象
&nbsp;&nbsp;&nbsp;&nbsp;包可以包含类、对象和特质，但不能包含函数或者变量的定义。很不幸，这是Java虚拟机的局限。
&nbsp;&nbsp;&nbsp;&nbsp;把工具函数或者常量添加到包而不是某个Utils对象，这是更加合理的做法。Scala中，包对象的出现正是为了解决这个局限。
&nbsp;&nbsp;&nbsp;&nbsp;**Scala中的包对象：常量，变量，方法，类，对象，trait(特质)**

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgxYjAxMjBmNjI0Y2Q0ZTgucG5n?x-oss-process=image/format,png)
