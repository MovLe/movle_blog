---
title: '[LeetCode-面试01.02]判断是否互为字符重排'
date: 2019-02-03 15:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目描述
给定两个字符串 s1 和 s2，请编写一个程序，确定其中一个字符串的字符重新排列后，能否变成另一个字符串。
```
示例 1：
输入: s1 = "abc", s2 = "bca"
输出: true 

示例 2：
输入: s1 = "abc", s2 = "bad"
输出: false

说明：
0 <= len(s1) <= 100
0 <= len(s2) <= 100
```

### 二.题解
#### 1.解法一：
##### (1)解题思路：
* 现将s1，s2字符串转换为字符数组c1,c2
* 再将数组c1,c2进行排序
* 再将数组c1,c2转换为字符串
* 再利用equal函数比较两个字符串是否相同即可

##### (2)Java代码实现：

```java
public boolean CheckPermutation(String s1, String s2) {
        //1.将字符串转换成数组
        char[] charArray1 = s1.toCharArray();
        char[] charArray2 = s2.toCharArray();

        //若两个字符串的长度不同，则肯定不能重排
        if(s1.length() != s2.length()){
            return false;
        }

        //给数组排序
        Arrays.sort(charArray1);
        Arrays.sort(charArray2);

        //将数组重新转换为字符串
        String c1 = String.copyValueOf(charArray1);
        String c2 = String.copyValueOf(charArray2);

        //比较两个字符串是否相等
        if(c1.equals(c2)){
            return true;
        }else{
            return false;
        }
}
```
