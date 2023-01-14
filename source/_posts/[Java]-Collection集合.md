---
title: '[Java]-Collection集合'
date: 2020-01-02 14:02:00
tags:  知识点速览
categories: Java
---
#### 1.Collection常用方法
```java
boolean add(E e);       //向集合中添加元素
boolean remove(E e);		//删除集合中的某个元素
void clear();  					//清空集合中的所有元素
boolean contains(E e);  //判断集合中是否包含某个元素
boolean isEmpty();			//判断集合是否为空
int size();							//获取集合的长度
Object[] toArray();			//将集合转换为一个数组
```


#### 2.Iterator迭代器：对集合进行遍历
```java
常用方法
	boolean hasNext()   //如果仍有元素可以迭代，则返回true判断集合是否还有元素
	E next()						//返回迭代的下一个元素，取出集合中的下一个元素

Iterator迭代器是一个接口，无法直接使用，需要使用Iterator接口的实现类对象，获取实现类的方式比较特殊
Collection接口中有一个方法iterator()，这个方法返回的就是迭代器的实现类对象

迭代器的使用步骤(重点)：
	1.使用集合中的方法iterator()获取迭代器的实现类对象，使用Iterator接口接收(多态)
	2.使用Iterator接口中的方法hasNext()判断是否还有元素
	3.使用Iterator接口中的方法next()取出集合中的下一个元素
```

#### 3.集合的两种遍历方式
##### (1)使用迭代器遍历
```java
迭代器的使用步骤(重点)：
	1.使用集合中的方法iterator()获取迭代器的实现类对象，使用Iterator接口接收(多态)
	2.使用Iterator接口中的方法hasNext()判断是否还有元素
	3.使用Iterator接口中的方法next()取出集合中的下一个元素

	Collection<String> coll = new ArrayList<>();
  coll.add("科比");
  coll.add("詹姆斯");
  coll.add("杜兰特");
  coll.add("库里");
  coll.add("乔丹");

  Iterator<String> it = coll.iterator();

  while(it.hasNext()){
  	String e = it.next();
    System.out.println("我最爱的球星有："+e);
  }
```
##### (2)使用增强for循环遍历集合
```java
增强for循环底层使用的也是迭代器，使用for循环的格式，简化了迭代器的书写
是JDK1.5之后出现的新特性
tips：增强for循环必须有被遍历的目标，目标只能是Collection集合或数组，增强for循环仅仅作为遍历操作出现

for(数据类型 变量名:容器对象){
	//循环体语句
}
for(String s:coll){
	System.out.println(s);
}
```