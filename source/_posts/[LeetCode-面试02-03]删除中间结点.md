---
title: '[LeetCode-面试02.03]删除中间结点'
date: 2019-04-07 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：
实现一种算法，删除单向链表中间的某个节点（除了第一个和最后一个节点，不一定是中间节点），假定你只能访问该节点。

>示例：
输入：单向链表a->b->c->d->e->f中的节点c
结果：不返回任何数据，但该链表变为a->b->d->e->f

### 二.题解：
#### 1.第一种解法：
##### (1)解题思路：
* 把节点c的val变为节点d(node.next)的val，
* 此时可以看成节点c已经变成了节点d，
* 改变c(node)的下一个节点为e(node.next.next)

##### (2)代码：
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next=node.next.next;
    }
}
```