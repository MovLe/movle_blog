---
title: '[LeetCode-1]两数之和'
date: 2019-06-07 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目
>给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

>示例:
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

### 二.题解
#### 1.第一种方法：暴力法
##### (1)解题思路：
* 嵌套循环
* 第一个循环，遍历数组nums，下标为i，从0开始
* 嵌套循环，遍历数组nums，下表为j，从i+1开始
* 判定nums[i]+num[j]是否等于target，
* 若等于，则返回数组[i,j]
* 否则进行下一轮循环

##### (2)代码：
```java
public static int[] twoSum(int[] num,int target){
    int index1=0;
    int index2=0;
    for(int i =0;i<num.length;i++){
        for(int j=i+1;j<num.length;j++){
            if(num[i]+num[j]==target){
                index1=i;
                index2=j;
            }
        }
    }
    int[] result ={index1,index2};
    return result;
}
```

#### 2.第二种方法：两遍哈希表：
##### (1)解题思路：
* 保持数组中的每个元素与其索引相互对应的最好方法是哈希表
* 首先将数组元素和下标存入哈希表map中
* 再进行for循环
* 再新建一个int类型的元素compont，其值为target与数组nums[i]的差值
* 判定哈希表map中是否存在compont元素，若存在，则返回该下表i与哈希表map key值为compont的value值
* 结束

##### (2)代码：
```java
public static int[] twoSum2(int[] nums,int target) throws Exception {
    Map<Integer,Integer> map = new HashMap<Integer,Integer>();
    for(int i=0;i<nums.length;i++){
        map.put(nums[i],i);
    }
    for(int i=0;i<nums.length;i++){
        int compont = target-nums[i];
        if(map.containsKey(compont)&&map.get(compont)!=i){
            return new int[]{i,map.get(compont)};
        }
    }
    throw new IllegalArgumentException("无匹配结果");
}
```
