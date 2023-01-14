---
title: 'Object类常用API'
date: 2020-01-01 14:02:00
tags:  知识点速览
categories: Java
---
#### 1.System类
##### (1)成员方法
```java
public static Long currentTimeMillis()    //返回以毫秒为单位的当前时间
public static void arraycopy(Object src,int srcPos,Object dest,int destPos,int Length)  //将数组中指定的数据拷贝到另一个数组中 src - 源数组 srcPos -源数组起始位置 dest - 目标数组 destPos - 目标数组起始位置 Length - 要复制的数组元素的个数

```

#### 2.StringBuilder类
##### (1)构造方法
```java
StringBulider();            //创建一个空的字符串缓冲区对象

StringBulider(String str);  //根据传入的内容创建一个字符串缓冲区对象
```

##### (2)成员方法
```java
StringBuilder append(Object obj); //添加内容
StringBuilder reverse();          //反转内容
String toString();								//将缓冲区内容转换为字符串
```

#### 3.包装类
##### (1)装箱
```java
构造方法
	Integer(int value)       //构造一个新分配的Integer对象，它表示指定的int值
	Integer(String s)				 //构造一个新分配的Integer对象，它表示String参数所指的int值 如“100” 注意：“a”是不行的

静态方法
	static Integer valueOf(int value)   //返回一个表示指定int值的Integer实例
	static Integer ValueOf(String s)    //返回保存指定的String的值的Integer对象
	
成员方法(拆箱)
	int intValue()  //以int类型返回该Integer的值
```

#### 4.字符串与基本数据类型的转换
##### (1)基本数据类型转换为字符串
a.基本类型数据与“”相连接即可
```java
String s1=100+"";
String s2=28.02+"";
```
b.使用包装类中的静态方法
```java
static String toString(int i)       //返回一个指定整数的String对象
```
c.使用String类中的静态方法
```java
static String valueOf(int i)        //返回int参数的字符串表示形式
```
##### (2)String类型转换为对应的基本类型：使用包装类的静态方法parseXX("字符串")方法

```java
public static byte parseByte(String s)     //将字符串转换为对应的byte基本类型
public static short parseShort(String s)     //将字符串转换为对应的Short基本类型
public static int parseInt(String s)     //将字符串转换为对应的int基本类型
public static long parseLong(String s)     //将字符串转换为对应的long基本类型
public static float parseFloat(String s)     //将字符串转换为对应的float基本类型
public static double parseDouble(String s)     //将字符串转换为对应的double基本类型
public static boolean parseBoolean(String s)     //将字符串转换为对应的boolean基本类型
```