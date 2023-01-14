---
title: '[LeetCode-面试01.03]URL化'
date: 2019-02-03 16:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目描述：
URL化。编写一种方法，将字符串中的空格全部替换为%20。假定该字符串尾部有足够的空间存放新增字符，并且知道字符串的“真实”长度。（注：用Java实现的话，请使用字符数组实现，以便直接在数组上操作。）
```
示例1:
输入："Mr John Smith    ", 13
输出："Mr%20John%20Smith"

示例2:
输入："               ", 5
输出："%20%20%20%20%20"

提示：
字符串长度在[0, 500000]范围内。
```

### 二.题解
#### 1.方法一
##### (1)解题思路：
* 新建一个StringBuilder sb
* 用for循环遍历，结束条件是小于length
* 遍历字符串S的第i个字符
* 当字符为空格时，sb新增元素%20
* 否则将S的第i个元素加到sb后面
* 循环结束，将sb转化为String返回即可

##### (2)代码：

```java
public static String replaceSpaces(String S,int length){
    StringBuilder sb = new StringBuilder();

    for(int i=0;i<length;i++){
       if(S.charAt(i)==' '){
           sb.append("%20");
       }else {
           sb.append(S.charAt(i));
       }
    }
    return sb.toString();
}
```

#### 2.方法二：
##### (1)解题思路：

* 已知字符串后面是空格，，所以将要转化的字符串除了后面的截取
* 然后再将截取后的字符串中的空格替换为%20即可

##### (2)代码：
```java
public static String replaceSpaces2(String S,int length){
    String s1 = S.substring(0,length);
    String s2 = S.replaceAll(" ","%20");
    return s2;
}
```