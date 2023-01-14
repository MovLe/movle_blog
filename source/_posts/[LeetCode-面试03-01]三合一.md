---
title: '[LeetCode-面试03.01]三合一'
date: 2019-05-19 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：

三合一。描述如何只用一个数组来实现三个栈。
你应该实现push(stackNum, value)、pop(stackNum)、isEmpty(stackNum)、peek(stackNum)方法。stackNum表示栈下标，value表示压入的值。
构造函数会传入一个stackSize参数，代表每个栈的大小。

>示例1:
 输入：
["TripleInOne", "push", "push", "pop", "pop", "pop", "isEmpty"]
[[1], [0, 1], [0, 2], [0], [0], [0], [0]]
 输出：
[null, null, null, 1, -1, -1, true]
说明：当栈为空时`pop, peek`返回-1，当栈满时`push`不压入元素。
示例2:
 输入：
["TripleInOne", "push", "push", "push", "pop", "pop", "pop", "peek"]
[[2], [0, 1], [0, 2], [0, 3], [0], [0], [0], [0]]
 输出：
[null, null, null, null, 2, 1, -1, -1]

### 二.题解：
#### 1.第一种解法：
##### (1)解题思路：
* 定义一个数组arr，数组的位置分配规则如下：
* 数组的下标为[0, 0 + 3, ... , 0 + 3 * (stackSize - 1)]存放stack0
* 数组的下标为[1, 1 + 3, ... , 1 + 3 * (stackSize - 1)]存放stack1
* 数组的下标为[2, 2 + 3, ... , 2 + 3 * (stackSize - 1)]存放stack2
* 然后，新建一个数组stackTop，用来标记每个栈的栈顶可插入元素的下标（在arr中的下标）。
* 当执行push操作的时候，需要处理判满，当执行pop或peek操作的时候需要处理判空。
* 其中判空和判满都是根据stackTop来判断

##### (2)代码：
```java
class TripleInOne {

    private int[] arr;
    private int[] stackTop;
    private int stackSize;

    public TripleInOne(int stackSize) {
        this.stackSize=stackSize;
        arr= new int[stackSize*3];
        stackTop = new int[]{0,1,2};
    }
    
    public void push(int stackNum, int value) {
        int curStackTop = stackTop[stackNum];
        if(curStackTop /3 == stackSize){
            return;
        }
        arr[curStackTop] = value;
        stackTop[stackNum]+=3;

    }
    
    public int pop(int stackNum) {
        if(isEmpty(stackNum)){
            return -1;
        }
        int value = arr[stackTop[stackNum]-3];
        stackTop[stackNum]-=3;

        return value;

    }
    
    public int peek(int stackNum) {
        if(isEmpty(stackNum)){
            return -1;
        }
        int value = arr[stackTop[stackNum]-3];
        return value;
    }
    
    public boolean isEmpty(int stackNum) {
        return stackTop[stackNum]<3;

    }
}

/**
 * Your TripleInOne object will be instantiated and called as such:
 * TripleInOne obj = new TripleInOne(stackSize);
 * obj.push(stackNum,value);
 * int param_2 = obj.pop(stackNum);
 * int param_3 = obj.peek(stackNum);
 * boolean param_4 = obj.isEmpty(stackNum);
 */
```