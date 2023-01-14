---
title: 'Map'
date: 2020-01-07 16:20:00
tags:  知识点速览
categories: Java
---


#### 1.Map<key,value>集合的特点
* Map集合是一个双列集合，一个元素包含两个值(一个key，一个value)
* Map集合中的元素，key和value的数据类型可以相同，也可以不同
* Map集合中的元素，key是不允许重复的，value是可以重复的
* Map集合中的元素，key和value是一一对应的

#### 2.HashMap
##### (1)HashMap集合的特点：
* HashMap底层是哈希表：查询的速度特别快：JDK1.8之前：数组+单向链表-JDK1.8之后：数组+单向链表/红黑树(链表的长度超过8)：提高查询的速度
* HashMap集合是一个无序的集合，存储元素和取出元素的顺序有可能不一致

#### 3.LinkedHashMap
(1).LinkedHashMap的特点
* LinkedHashMap继承自HashMap
* LinkedHashMap集合底层是哈希表+链表(保证迭代的顺序)
* LinkedHashMap集合是一个有序的集合，存储元素和取出元素的顺序是一致的

#### 4.Map中的常用方法
##### (1)常用方法
```java
public V put(K key,V value);       //	把指定的键与指定的值添加到Map集合中
public V remove(Object key);   //把指定的键所对应的键值对元素在Map集合中删除，返回被删除元素的值
public V get(Object key);           //根据指定的键，在Map集合中获取对应的值
boolean containsKey(Object key);    //判断集合中是否包含指定的键
public Set<K> keySet();             //获取Map集合中所有的键，存储到Set集合中
public Set<Map.Entry<K,V>> entrySet();    //获取到Map集合中所有的键值对对象的集合(Set集合)
```

#### 5.Map.Entry<K,V>：
##### (1)在Map接口中有一个内部接口Entry
##### (2)作用：
* 当Map集合一创建，那么就会在Map集合中创建一个Entry对象，用来记录键与值(键值对对象，键与值的映射关系)-->结婚证

##### (3)entrySet()：把Map集合内部的多个Entry对象取出来存储到一个Set集合中
```java
格式
	Set<Map.Entry<K,V>> entrySet()
```

#### 6.Map集合的遍历方法
##### (1)Map集合的第一种遍历方式：通过键找值
```java
Map集合中的方法：
	Set<K> keySet() 返回此映射中包含的键的Set视图
实现步骤：
	1.使用Map集合中的keySet()方法，把Map集合所有的key取出来，存储到一个Set集合中
	2.遍历Set集合，获取Map集合中的每一个key
	3.通过Map集合中的方法get(key)，通过key找到value
```

**示例**：
*Map02KeySetDemo.java*
```java
public class Map02KeySetDemo {

    public static void main(String[] args) {
        demo01();
    }

    /**
     * 使用keySet方法遍历Map
     */
    private static void demo01() {
        Map<String,Integer> map = new HashMap<>();
        map.put("林志玲",18);
        map.put("吴亦凡",38);
        map.put("林志颖",28);
        map.put("赵忠祥",48);
        map.put("成龙",57);

        Set<String> set = map.keySet();

        for(String k:set){
            Integer value = map.get(k);
            System.out.println(k+"="+value);
        }

    }
}

```
##### (2)Map集合的第二种遍历方式：使用Entry对象遍历
```java
Map集合中的方法：
	Set <Map.Entry<K,V>> entrySet()  返回此映射中包含的映射关系的Set视图
	
实现步骤：
	1.使用Map集合中的方法entrySet()，把Map集合中多个Entry对象取出来，存储到一个Set集合中
	2.遍历Set集合，获取每一个Entry对象
	3.视图Entry对象中的方法getKey()和getValue()获取键和值
```

**示例**：
*Map03EntrySetDemo.java*
```java
public class Map03EntrySetDemo {

    public static void main(String[] args) {
        demo01();
    }

    private static void demo01() {
        Map<String,Integer> map = new HashMap<>();
        map.put("林志玲",18);
        map.put("吴亦凡",38);
        map.put("林志颖",28);
        map.put("赵忠祥",48);
        map.put("成龙",57);

        Set<Map.Entry<String,Integer>> set = map.entrySet();

        System.out.println("========使用迭代器遍历=========");
        Iterator<Map.Entry<String,Integer>> it = set.iterator();

        while(it.hasNext()){
            Map.Entry<String,Integer> m = it.next();
            String K = m.getKey();
            Integer V = m.getValue();

            System.out.println(K+"="+V);
        }
        System.out.println("========增强for循环遍历=========");
        for (Map.Entry<String,Integer> m:set) {
            String Key = m.getKey();
            Integer Value = m.getValue();
            System.out.println(Key+"="+Value);
        }
    }
```
#### 7.HashMap存储自定义类型的键值
##### (1)Map集合保证key是唯一的
* 作为key的元素，必须重写hashCode方法和equals方法，以保证key唯一

##### (2)示例
*a.HashMap01Demo.java*
```java
public class HashMap01Demo {
    public static void main(String[] args) {
        //demo01();
        demo02();
    }

    /**
     * HashMap存储自定义类型键值
     *      key:Person类型-Person类必须重写hashCode方法与equals方法，以保证key唯一
     *      value:String类型-可以重复
     */
    private static void demo02() {
        HashMap<Person,String> map = new HashMap<>();
        map.put(new Person("张三",19),"北京");
        map.put(new Person("李四",22),"南京");
        map.put(new Person("王五",99),"重庆");
        map.put(new Person("赵六",38),"上海");
        map.put(new Person("张三",19),"天津");

        Set<Map.Entry<Person, String>> entrys = map.entrySet();
        for (Map.Entry<Person,String> en:entrys) {
            Person person = en.getKey();
            String value = en.getValue();
            System.out.println(person+"-->"+value);
        }

    }

    /**
     * HashMap存储自定义类型键值
     *      key：String类型 -String类型重写HashCode和equals方法，可以保证唯一
     *      value：Person类型 - value可以重复(同名同年龄的人视为同一个)
     */
    private static void demo01() {
        HashMap<String,Person> map = new HashMap<>();

        map.put("北京",new Person("张三",19));
        map.put("南京",new Person("李四",17));
        map.put("重庆",new Person("王五",22));
        map.put("上海",new Person("赵六",30));
        map.put("天津",new Person("胡八",18));

        Set<String> set = map.keySet();
        for (String s:set) {
            Person value = map.get(s);
            System.out.println(s+"-->"+value);
        }
    }
}

```
*b.Person.java*
```java
public class Person {
    private String name;
    private int age;
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public Person() {
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age &&
                Objects.equals(name, person.name);
    }
    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```
#### 8.LinkedHashMap
##### (1)LinkedHashMap继承自HashMap
##### (2)LinkedHashMap接口的哈希表和链表实现，具有可预知的迭代顺序
##### (3)底层原理：哈希表+链表(记录元素的顺序)

#### 9.HashTable
##### (1)HashTable特点
* HashTable继承自Map<K,V>接口
* HashTable：底层是一个哈希表，是一个线程安全的集合，是单线程集合，速度慢
* HashMap：底层是一个哈希表，是一个线程不安全的集合，是多线程集合，速度快
* HashMap集合：可以存储null值，null键
* HashTable集合：不能存储null值，null键
* HashTable和Vector集合一样，在jdk1.2版本之后被更先进的集合(HashMap,ArrayList)取代了
* HashTable的子类Properties依然活跃在历史舞台
* Properties集合是一个唯一和IO流相结合的集合