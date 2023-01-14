---
title: '[LeetCode-面试01.08]零矩阵'
date: 2019-03-11 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：
编写一种算法，若M × N矩阵中某个元素为0，则将其所在的行与列清零。
>示例 1：
输入：
[[1,1,1],
  [1,0,1],
  [1,1,1]]
输出：
[[1,0,1],
  [0,0,0],
  [1,0,1]]

>示例 2：
输入：
[[0,1,2,0],
  [3,4,5,2],
  [1,3,1,5]]
输出：
[[0,0,0,0],
  [0,4,5,0],
  [0,3,1,0]]

### 二题解：
#### 1.第一种解法：
##### (1)解题思路：

* 利用两个HashSet表row_set,col_set存储矩阵元素为0的横纵坐标
* 再根据横坐标row_set，将元素0所在行重置为0
* 再根据纵坐标col_set，将元素0所在列重置为0

##### (2)具体实现：
* 利用HashSet表记录矩阵中元素为0的下标，其中row_set记录元素0所在的行数，col_set记录元素0所在的列数
* 再利用for循环，循环元素为int row_index,row_set将行数为row_set中成员所在行重置为0，Arrays.fill(matrix[row_index],0);
* 再利用嵌套for循环，将0元素所在的列重置为0
* 外循环循环元素为 int col_index,col_set,内循环为下标为i,从0开始遍历，直到matrix.length-1
* 内层循环下标为i，从0开始，遍历到matrix[0].length-1，matrix[i][col_index]=0;
* 循环结束即转换完毕


##### (3)代码：
```java
    public static int[][] setZeros(int[][] matrix){
        HashSet<Integer> row_set = new HashSet<Integer>();
        HashSet<Integer> col_set = new HashSet<Integer>();

        for(int i=0;i<matrix.length;i++){
            for(int j=0;j<matrix[0].length;j++){
                if(matrix[i][j]==0){
                    row_set.add(i);
                    col_set.add(j);
                }
            }
        }

        for(int row_index:row_set){
            Arrays.fill(matrix[row_index],0);
        }
        for(int col_index:col_set){
            for(int i=0;i<matrix[0].length;i++){
                matrix[i][col_index]=0;
            }
        }
        return matrix;
    }
```