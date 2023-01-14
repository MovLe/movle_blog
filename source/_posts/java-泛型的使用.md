---
title: '泛型的使用'
date: 2020-01-03 14:02:00
tags:  知识点速览
categories: Java
---
#### 1.泛型类和泛型方法
##### (1)格式
```java
	类名<泛型>
	方法名(泛型)
```
##### (2)示例
例如
a.泛型类
```java
public class MyClass<T>{
	public void print(T t){
		System.out.println(t);
	}
}
```
b.实现方法
```
public class Test01{
	public static void main(String[] args){
		MyClass<String> mc1 = new MyClass<>();
		mc1.print("hello1");
		
		MyClass<Integer> mc2 = new MyClass();
		mc2.print(99);
	}
}		
```

#### 2.泛型接口和泛型方法
##### (1)格式
```java
	接口<泛型>
	方法名(泛型)
```
##### (2)示例
例如
a.接口
```java
public interface MyInter<O>{
	public abstract void print(O o);
}
```
b.接口实现
```java
public class MyInterImpl<O> implements MyInter<O>{
	@Overide
	public void print(O o){
		System.out.println(o);
	}
}
```
c.调用
```java
public class Test02{
	public static void main(String[] args){
		MyInterImpl<String> m1 = new MyInterImpl<>();
		m1.print("world");
		
		MyInterImpl<Double> m2 = new MyInterImpl();
		m2.print(8.8)
		
	}
}
```
#### 3.泛型通配符
##### (1)概念
```
泛型的通配符
	？：代表任意的数据类型
使用方式：
	不能创建对象使用
	只能作为方法的参数使用
泛型的上限限定
	？ extends E 代表使用的泛型只能是E类型的子类/本身
泛型类的下限限定
	？ super E 代表使用的泛型只能是E类型的父类/本身 
```
##### (2)示例
```java
public class Test02 {
    public static void main(String[] args) {
        demo1();
    }
    private static void demo1() {
        ArrayList<String> list1 = new ArrayList<>();
        list1.add("hello");
        list1.add("world");
        list1.add("beatiful");
        print(list1);

        ArrayList<Integer> list2 =  new ArrayList<>();
        list2.add(100);
        list2.add(200);
        list2.add(300);
        list2.add(500);
        print(list2);
    }
    /**
     * 泛型通配符的使用
     * @param list
     */
    private static void print(ArrayList<?> list){
        for (Object o : list) {
            System.out.println(o);
        }
    }
}
```