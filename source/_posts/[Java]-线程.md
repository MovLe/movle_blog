---
title: '[Java]-线程'
date: 2020-01-08 17:20:00
tags:  知识点速览
categories: Java
---

### 一.线程

#### 1.创建多线程程序的第一种方式：创建Thread类的子类

##### (1)实现步骤

* 创建一个Thread类的子类
* 在Thread类的子类中重写Thread类中的run方法，设置线程任务(开启线程要做什么？)
* 在创建Thread类的子类对象
* 调用Thread类中的start方法，开启新的线程，执行run方法



#### 2.Thread类

##### (1)构造方法

```java
public Thread();      //分配一个新的线程对象
public Thread(String name);      //分配一个指定名字的新线程对象
public Thread(Runnable target);      //分配一个带有指定目标的新的线程对象
public Thread(Runnable target,String name);      //分配一个带有指定目标的新线程对象并指定名字
```



##### (2)常用方法

```java
public String getName();		//获取当前线程的名称
public void start();		//导致此线程开始执行，Java虚拟机调用此线程的run方法
public void run();		//此线程要执行的任务在此定义
public static void sleep(long millis);		//使当前正在执行的线程以指定的毫秒数暂停
public static Thread currentThread();		//返回对当前正在执行的线程对象的引用
```



##### (3)设置线程的名称

* 使用Thread类中的方法setName(名字)
		void setName(String name) 改变线程名称，使之与参数 name 相同。
* 创建一个带参数的构造方法,参数传递线程的名称;调用父类的带参构造方法,把线程名称传递给父类,让父类(Thread)给子线程起一个名字
		Thread(String name) 分配新的 Thread 对象。



#### 3.创建线程方式二：实现Runnable接口 

##### (1)概念

* Runnable 接口应该由那些打算通过某一线程执行其实例的类来实现。类必须定义一个称为run的无参数方法。

##### (2)实现步骤:

* 创建一个Runnable接口的实现类
* 在实现类中重写Runnable接口的run方法,设置线程任务
* 创建一个Runnable接口的实现类对象
* 创建Thread类对象,构造方法中传递Runnable接口的实现类对象
* 调用Thread类中的start方法,开启新的线程执行run方法



##### (3)实现Runnable接口创建多线程程序的好处:

a.避免了单继承的局限性
* 一个类只能继承一个类(一个人只能有一个亲爹),类继承了Thread类就不能继承其他的类
* 实现了Runnable接口,还可以继承其他的类,实现其他的接口

b.增强了程序的扩展性,降低了程序的耦合性(解耦)
* 实现Runnable接口的方式,把设置线程任务和开启新线程进行了分离(解耦)
* 实现类中,重写了run方法:用来设置线程任务
* 创建Thread类对象,调用start方法:用来开启新线程
