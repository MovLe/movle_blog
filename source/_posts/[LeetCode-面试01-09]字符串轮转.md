---
title: '[LeetCode-面试01.09]字符串轮转'
date: 2019-04-01 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目
字符串轮转。给定两个字符串s1和s2，请编写代码检查s2是否为s1旋转而成（比如，waterbottle是erbottlewat旋转后的字符串）。

>示例1:
 输入：s1 = "waterbottle", s2 = "erbottlewat"
 输出：True

>示例2:
 输入：s1 = "aa", "aba"
 输出：False

### 二.题解
#### 1.第一种方法：
##### (1)解题思路：
* 首先判定两个字符串s1,s2长度是否相等，不想等返回false
* 在判定两个字符串是否equals.若相同返回true
* 再使s1叠加到s1后面
* 判定新形成的s1是否包含s2，若包含返回true
* 否则返回false

##### (2)代码：
```java
    public static boolean isFlipedString(String s1,String s2){
        if(s1.length()!=s2.length()){
            return false;
        }
        if(s1.equals(s2)){
            return true;
        }
        s1+=s1;
        if(s1.contains(s2)){
            return true;
        }else {
            return false;
        }
    }
```
#### 2.第二种方法：
##### (1)解题思路：
* 还是先判定字符串s1,s2长度是否相等，以及s1是否与s2相同
* 利用for循环，找到s1中的字符与s2第一个字符相同的下标i,
* 新建一个StringBuilder sb，截取s2从第i位到最后的字符串，赋值给sb，sb=s2.substring(i)
* 再截取s2从0位到i位字符串，添加到sb,sb.append(s2.substring(0,i))
* 再比较sb.toString与s1是否相同
* 若相同返回true，否则返回false

##### (2)代码：
```java
    public static boolean isFlipedString2(String s1,String s2){
        if(s1.length()!=s2.length()){
            return false;
        }
        if(s1.equals(s2)){
            return true;
        }
        //"[erbottle]wat","wat[erbottle]"
        for(int i=0;i<s1.length();i++){
            if(s1.charAt(i)==s2.charAt(0)){
                //i=3
                StringBuilder sb = new StringBuilder(s1.substring(i));
                sb.append(s1.substring(0,i));

                if(s2.equals(sb.toString())){
                    return true;
                }
            }
        }
        return false;
    }
```