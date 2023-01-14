---
title: '[LeetCode-面试01.01]判断字符是否唯一'
date: 2019-02-03 16:02:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目描述：
实现一个算法，确定一个字符串 s 的所有字符是否全都不同。

```
示例 1：
输入: s = "leetcode"
输出: false 

示例 2：
输入: s = "abc"
输出: true

限制：
0 <= len(s) <= 100
如果不使用额外的数据结构，会很加分
```

### 二.题解
#### 1.第一种方法
##### (1)解题思路

* 第一次遍历字符串所有字母；
* 第二次遍历从第一次遍历的后一位开始；
* 判断两次遍历的字母是否相等，一旦相等则返回false；
* 默认返回true

##### (2).代码：
```java
class Solution {
    public boolean isUnique(String astr) {
        for (int i = 0; i < astr.length() - 1; i++) {
			for (int j = i + 1; j < astr.length(); j++) {
				if (astr.charAt(i) == astr.charAt(j)) {
					return false;
				}
			}
		}
		return true;
    }
}
```

#### 2.方法二
##### (1)解题思路：
* 利用set集元素不同的性质判定
* 将String中的字符添加到set集中
* 遍历结束后，若set集的大小和String集合的长度相同，则证明不重复
* 否则有重复元素

##### (2)代码：
```java
public static boolean isUnique2(String astr) {

        Set set = new HashSet();

        for (int i = 0; i < astr.length(); i++) {
            set.add(astr.charAt(i));
        }

        if (set.size() == astr.length()) {
            return true;
        } else {
            return false;
        }
}
```