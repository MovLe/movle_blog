---
title: '[Java]-Collections集合工具类'
date: 2020-01-07 16:05:00
tags:  知识点速览
categories: Java
---


#### 1.常用功能
```java
public static <T> boolean addAll(Collection<T> c,T...elements);  //往集合中添加一些元素
public static void shuffle(List<?> list);					//打乱集合的顺序
public static <T> void sort(List<T> list);				//将集合中元素按照默认规则排序
public static <T> void sort(List<T> list,Comparator<? super T>);   //将集合中元素按照指定规则排序
```
#### 2.注意
##### (1)sort(List<T> list)使用前提：
* 被排序的集合里面存储的元素，必须实现comparable，重写接口中的方法compareTo定义排序的规则

##### (2)Comparable接口的排序规则：
* 自己(this)-参数  ：升序
* 参数-自己(this)：降序

##### (3)Comparator和Comparable的区别
* Comparable:自己(this)和别人(参数)比较，自己需要实现Comparable接口，重写比较的规则compareTo方法
* Comparator:相当于找一个第三方的裁判，比较两个对象

##### (4)Comparator的排序规则：
* o1-o2：生序
* o2-o1：降序

#### 3.示例
##### (1).CollectionsAPI.java
```java
public class CollectionsAPI {
    public static void main(String[] args) {
        //demo01();
        //demo02();
        //demo03();
        demo04();
    }

    /**
     * Collections工具类之sort排序另一种方法
     */
    private static void demo04() {
        ArrayList<Integer> Lisa = new ArrayList<>();
        Lisa.add(1);
        Lisa.add(4);
        Lisa.add(2);
        Lisa.add(3);
        Lisa.add(9);
        Lisa.add(8);
        System.out.println("排序前：lisa"+Lisa);

        Collections.sort(Lisa, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                //生序
                //return o1-o2;
                //降序
                return o2-o1;
            }
        });
        System.out.println("排序后：lisa"+Lisa);

        ArrayList<Student> stu = new ArrayList<>();
        stu.add(new Student("迪丽热巴",18));
        stu.add(new Student("古力娜扎",20));
        stu.add(new Student("胡歌",17));
        stu.add(new Student("胡歌",18));
        System.out.println("排序前的stu:"+stu);

        Collections.sort(stu, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                int result = o1.getAge()-o2.getAge();
                if(result==0){
                    result = o1.getName().charAt(0)-o2.getName().charAt(0);
                }
                return result;
            }
        });
        System.out.println("排序前后stu:"+stu);
    }

    /**
     * Collections工具类sort排序之自定义类型的排序
     * 自己(this)-参数 ：升序
     * 参数-自己(this)：降序
     */
    private static void demo03() {
        ArrayList<Person> person = new ArrayList<>();
        person.add(new Person("张三",18));
        person.add(new Person("李四",20));
        person.add(new Person("王武",15));
        System.out.println("排序前的集合：person"+person);

        Collections.sort(person);
        System.out.println("排序后的集合：person"+person);
    }

    /**
     * Collections工具类之排序
     */
    private static void demo02() {
        ArrayList<Integer> list1 = new ArrayList<>();
        list1.add(1);
        list1.add(4);
        list1.add(2);
        list1.add(3);
        list1.add(9);
        list1.add(8);
        System.out.println("排序前的集合:list1"+list1);

        Collections.sort(list1);
        System.out.println("排序后的集合:list1"+list1);
    }

    /**
     * Collections工具类的单次加入多个元素与打乱集合顺序
     */
    private static void demo01() {
        ArrayList<String> list1 = new ArrayList<>();
        list1.add("a");
        list1.add("b");
        list1.add("c");
        list1.add("d");
        list1.add("e");
        System.out.println("多次加入单个元素：list1"+list1);

        //添加多个元素
        ArrayList<String> list2 = new ArrayList<>();
        Collections.addAll(list2,"a","b","c","d","e");
        System.out.println("单次加入多个元素：list2"+list2);

        //打乱顺序
        Collections.shuffle(list1);
        System.out.println("打乱顺序后的集合：list1"+list1);

    }
}
```
##### (2).Person.java
```java
public class Person implements Comparable<Person>{
    private String name;
    private int age;
    @Override
    public int compareTo(Person o) {
        /*年龄生序排列*/
        return this.getAge()-o.getAge();

        //return 0;
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
    public int getAge() {
        return age;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
    public void setAge(int age) {
        this.age = age;
    }
}
```
##### (3).Student.java
```java
public class Student {
    private String name;
    private int age;
    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public Student() {
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
}
```