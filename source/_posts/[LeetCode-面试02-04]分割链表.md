---
title: '[LeetCode-面试02.04]分割链表'
date: 2019-04-12 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：
编写程序以 x 为基准分割链表，使得所有小于 x 的节点排在大于或等于 x 的节点之前。如果链表中包含 x，x 只需出现在小于 x 的元素之后(如下所示)。分割元素 x 只需处于“右半部分”即可，其不需要被置于左右两部分之间。

>示例:
输入: head = 3->5->8->5->10->2->1, x = 5
输出: 3->1->2->10->5->5->8

### 二.题解：
#### 1.第一种方法：
##### (1)解题思路：
* 先创建两个头结点分别用于连接小于部分和大于等于部分
* 遍历该链表，若当前结点小于x,将其插到小于的链表上否则插到大于链表上。
* 遍历完毕后，将大于等于部分链表的连到小于部分的后面即可

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
    public ListNode partition(ListNode head, int x) {
        ListNode preHead = new ListNode(-1);
        ListNode nextHead = new ListNode(-1);
        ListNode preCurrent = preHead;
        ListNode nextCurrent = nextHead;
        ListNode current = head;

        while(current != null){
            if(current.val < x){
                preCurrent.next = current;
                preCurrent = preCurrent.next;
            }else{
                nextCurrent.next = current;
                nextCurrent = nextCurrent.next;
            }
            current = current.next;
        }
        preCurrent.next=nextHead.next;
        nextCurrent.next=null;

        return preHead.next;
    }
}
```