---
title: 'List接口'
date: 2020-01-03 15:05:00
tags:  知识点速览
categories: Java
---
#### 1.List接口特点
* 有序的集合，存储元素和取出元素的顺序是一致的
* 有索引，包含了一些带索引的方法
* 允许存储重复的元素

#### 2.List接口中带索引的方法
```java
public void add(int index,E element);  //将指定的元素，添加到集合中的指定位置
public E get(int index);							 //返回集合中指定位置的元素
public E remove(int index);						 //移除列表中指定位置的元素，返回的是被移除的元素
public E set(int index,E element);		 //用指定的元素替换指定位置的元素，返回值是更新前的元素

//注意：操作索引时，一定要防止索引越界异常
```

#### 3.ArrayList
(1)ArrayList集合数据存储的结构是数组
(2)元素增删慢，查找快

#### 4.LinkedList
(1)LinkedList集合的特点
* 底层是一个链表结构：查询慢，增删块
* 里边包含了大量操作首尾元素的方法

(2)常用方法
```java
public void addFrist(E e);   //将指定元素插入到此列表的开头
public void addLast(E e);		 //将指定元素插入到此列表的结尾
public void push(E e);			 //将元素推入此列表所表示的堆栈

public E getFrist();         //返回此列表的第一个元素
public E getLast();					 //返回此列表的最后一个元素

public E removeFrist();			 //移除并返回此列表的第一个元素
public E removeLast();			 //移除并返回此列表的最后一个元素
public E pop();							 //从此列表所表示的堆栈处弹出一个元素
```

#### 5.Vector
(1)Vector类可以实现可增长的对象数组
(2)Vector是同步的，单线程的