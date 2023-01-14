---
title: '[Java]-异常'
date: 2020-01-08 16:20:00
tags:  知识点速览
categories: Java
---

#### 1.异常的特点
##### (1)java.Lang.Throwable：类是java语言中所有错误或异常的超类
##### (2)Exception:
* 编译期异常，进行编译(写代码)java程序时出现的问题
* RuntimeException:运行期异常，java程序在运行过程中出现的问题
* 异常就相当于程序得了一个小毛病(感冒，发烧)，把异常处理掉，程序可以继续执行(吃点药，继续工作)

##### (3)Error:错误
* 错误就相当于程序得了一个无法治愈的病(艾滋)，必须修改源代码，程序才能继续执行

#### 2.异常的处理

#### 3.throw关键字
##### (1)作用：可以使用throw关键字在指定的方法中抛出指定的异常
##### (2)使用格式：throw new xxxException("异常产生的原因");
##### (3)注意：
* throw关键字必须写在方法的内部
* throw关键字后边new的对象必须是Exception或者Exception的子类对象
* throw关键字抛出指定的异常对象，就必须处理这个异常对象：
* thorw关键字后边创建的事RuntimeException或者是RuntimeException的子类对象，我们可以不处理，默认交给JVM处理(打印异常对象，中断程序)
* throw关键字后边创建的是编译异常，我们就必须处理这个异常，要么throws，要么try...catch



#### 4.Objects类中的静态方法
##### (1)代码源码：

```java
public static <T> requireNoNull(T obj);    //查看指定引用对象不是null

public static <T> requireNoNull(T obj){
	if(obj == null){
		throw new NullPointerException();
	}
	return obj;
}
```

##### (2)用法：
```java
public class ObjectsDemp {
    public static void main(String[] args) {
        demo01(null);
    }

    private static void demo01(Object obj) {

        //Objects.requireNonNull(obj);
        Objects.requireNonNull(obj,"传递对象的值是null");
    }
}
```


#### 5.Throws声明异常

##### (1)throws关键字：异常处理的第一种方式，交给别人处理

##### (2)作用：

* 当方法内部抛出异常对象的时候，我们必须处理这个异常
* 可以使用throws关键字处理异常对象，会把异常对象声明抛出给方法的调用者处理(自己不处理，交给别人处理)，最终交给JVM处理(中断)

##### (3)使用格式：在方法声明时使用

```java
修饰符 返回值类型 方法名(参数列表) throws{
	throw new XXXException("产生原因");
	...
}
```

##### (4)注意：

* throws关键字必须写在方法的声明处
* throws关键字后边声明的异常必须是Exception或Exception的子类
* 方法内部如果抛出了多个异常对象，那么throws后边必须也声明多个异常，如果抛出的多个异常对象有子父类关系，那么直接声明父类异常即可
* 调用了一个声明抛出的方法，我们就必须处理声明的异常，要么继续使用throws声明抛出，交给方法的调用者处理，最终交给JVM，要么try...catch自己处理异常

#### 6.try...catch：异常处理的第二种方式 自己处理异常
##### (1)格式：
```java
try{
	可能产生异常的代码
}catch(定义一个异常的变量，用来接收try中抛出的异常对象){
	异常的处理逻辑，异常对象之后，怎么处理异常对象
	一般在工作中，会把异常的信息记录到一个日志中
}
...
catch(异常类名 变量名){
	
}
```

##### (2)注意：

* try中可能抛出多个异常对象，那么就可以使用多个catch来处理这些异常对象
* 如果try中产生了异常，那么就会执行catch中的异常处理逻辑，执行完毕catch中的处理逻辑，继续执行try...catch之后的代码
* 如果try中没有产生异常，就不会执行catch中的异常处理逻辑，执行完try中的代码，继续执行try...catch之后的代码

#### 7.Throwable类中定义了3个异常处理的方法
##### (1)代码
```java
String getMessage();  //返回此throwable的简短描述
String toString();    //返回此throwable的详细消息字符串
void printStackTrace();   //JVM打印异常对象，默认此方法，打印的异常信息是最全面的
```

#### 8.异常的注意事项
##### (1)多个异常使用捕获该如何处理
* 多个异常分别处理
* 多个异常一次捕获，多次处理
* 多个异常一次捕获，一次处理

##### (2)多个异常一次捕获，多次处理-注意：

* catch里边定义的异常变量，如果有子父类关系，那么子类的异常变量必须写在上边，否则会报错

##### (3)运行时一次被抛出可以不处理，即不捕获也不声明抛出

##### (4)如果finally有return语句，永远返回finally中的结果，避免该情况

##### (5)如果父类抛出了多个异常，子类覆盖父类的方法时，只能抛出相同的异常或者是它的子类

##### (6)父类方法没有抛出异常，子类覆盖父类该方法时也不可抛出异常，此时子类产生该异常，只能捕获处理，不能声明抛出

##### (7)在try/catch后可以追加finally代码块，其中的代码一定会执行，通过用于资源回收


#### 9.自定义异常

##### (1)自定义异常类：java提供的异常类，不够我们使用，需要自己定义一些异常类

##### (2)格式：
```java
public class XXXException extends Exception|RuntimeException{
	添加一个空参数的构造方法
	添加一个带异常信息的构造方法
}
```

##### (3)注意：

* 自定义异常类一般都是以Exception结尾，说明该类是一个异常类
* 自定义异常类，必须得继承Exception或者RuntimeException
* 继承Exception，那么该自定义的异常类就是一个编译期异常，如果方法内部抛出了编译期异常，就必须处理这个异常，要么throws,要么t r y...catch
* 继承RuntimeException，那么该自定义的异常类就是一个运行期异常，无需处理，交给虚拟机处理(中断)

#### 10.finally代码块
##### (1)格式：
```java
try{
	可能产生异常的代码
}catch(定义一个异常的变量，用来接收try中抛出的异常对象){
	异常的处理逻辑，异常对象之后，怎么处理异常对象
	一般在工作中，会把异常的信息记录到一个日志中
}
...
catch(异常类名 变量名){
	
}finally{
	无论是否出现异常都会执行
}
```

##### (2)注意：
* finally不能单独使用，必须和try一起使用
* finally一般用于资源释放(资源回收)，无论程序是否出现异常，最后都要是否(IO)