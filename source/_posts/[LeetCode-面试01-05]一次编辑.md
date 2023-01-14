---
title: '[LeetCode-面试01.05]一次编辑'
date: 2019-02-17 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：

字符串有三种编辑操作:插入一个字符、删除一个字符或者替换一个字符。 给定两个字符串，编写一个函数判定它们是否只需要一次(或者零次)编辑。

```shell
示例 1:
输入: 
first = "pale"
second = "ple"
输出: True
 
示例 2:
输入: 
first = "pales"
second = "pal"
输出: False
```

### 二.题解
#### 1.第一种解法：
##### (1)解题思路

* 首先验证两个字符串字符个数是否相差小于等于1，否则返回false
* 判定first是否为长字符串，否则将first替换为长字符串，保障first为长字符串
* 利用循环，判定字符串字符是否一样，若判定不一样，则跳过不同的字符串，截取长字符串frist的i+1位后的字符串，
* 若frist字符串长度与second字符串的长度相同，则截取second的第i+1位后的字符串，否则截取second的第i为字符串
* 判定截取后的frist的与second字符串是否相同，相同则返回true，否则返回false

##### (2)代码：
```java
/**
* 解题思路
* 首先验证两个字符串字符个数是否相差小于等于1，否则返回false
* 判定first是否为长字符串，否则将first替换为长字符串，保障first为长字符串
* 利用循环，判定字符串字符是否一样，若判定不一样，则跳过不同的字符串，截取长字符串frist的i+1位后的字符串，
* 若frist字符串长度与second字符串的长度相同，则截取second的第i+1位后的字符串，否则截取second的第i为字符串
* 判定截取后的frist的与second字符串是否相同，相同则返回true，否则返回false
*/

public static boolean oneEditAway(String first,String second){

   int len1 = first.length()
   int len2 = second.length();

   if(Math.abs(len1-len2)>1){
       return false;
   }

   if(len2>len1){
       String tem = first;
       first=second;
       second=tem;
    }
    int l1=first.length();
    int l2= second.length();

    for(int i=0;i<l2;i++){
        if(first.charAt(i) != second.charAt(i)){
            return first.substring(i+1).equals(second.substring(l1==l2?i+1:i));
        }else {
            return true;
        }
    }
    return true;
}
```
