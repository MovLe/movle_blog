---
title: '[LeetCode-剑指Offer-面试题03]数组中重复的数字'
date: 2019-05-21 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：
找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

>示例 1：
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 

### 二.题解：
#### 1.第一种方法：遍历数组
##### (1)解题思路：

* 利用for循环遍历数组，将数组nums[]中的元素添加到集合HashSet set中
* 同时判定set中是否包含nums[i]的元素
* 若包含则返回nums[i]元素，同时退出循环
* 否则进行下一次循环

##### (2)代码：
```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        int rep =-1;
        Set<Integer> set = new HashSet<Integer>();

        for(int i=0;i<nums.length;i++){
            if(set.contains(nums[i])){
                rep=nums[i];
                break;
            }
            set.add(nums[i]);
        } 
        return rep;
    }
}
```