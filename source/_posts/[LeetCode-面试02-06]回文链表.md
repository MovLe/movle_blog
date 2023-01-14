---
title: '[LeetCode-面试02.06]回文链表'
date: 2019-05-12 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：
编写一个函数，检查输入的链表是否是回文的。

>示例 1：
输入： 1->2
输出： false 
示例 2：
输入： 1->2->2->1
输出： true 

### 二.题解：
#### 1.第一种解法：
##### (1)解题思路：
* 首先生成新的反转head的链表newHead
* 然后判断两个链表是否相等即可
* 若相等则是回文链表，否则不是

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
    public boolean isPalindrome(ListNode head){
        ListNode newHead =reverseAndClone(head);
        return isEquals(head,newHead);
    }
    public ListNode reverseAndClone(ListNode node){
        ListNode head = null;

        //     n                     n
        //node:1->2->3->4    head:1<-2<-3<-4
        while(node!=null){
            ListNode n = new ListNode(node.val);   //复制
            n.next=head;
            head=n;

            node=node.next;
        }
        return head;
    }
    public boolean isEquals(ListNode l1,ListNode l2){

        while(l1!=null&&l2!=null){
            if(l1.val!=l2.val){
                return false;
            }
            l1=l1.next;
            l2=l2.next;
        }
        
        return l1==null&&l2==null;
    }

}
```
