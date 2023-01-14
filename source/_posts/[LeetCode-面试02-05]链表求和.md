---
title: '[LeetCode-面试02.05]链表求和'
date: 2019-04-22 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：
给定两个用链表表示的整数，每个节点包含一个数位。
这些数位是反向存放的，也就是个位排在链表首部。
编写函数对这两个整数求和，并用链表形式返回结果。

>示例：
输入：(7 -> 1 -> 6) + (5 -> 9 -> 2)，即617 + 295
输出：2 -> 1 -> 9，即912

>进阶：假设这些数位是正向存放的，请再做一遍。
示例：
输入：(6 -> 1 -> 7) + (2 -> 9 -> 5)，即617 + 295
输出：9 -> 1 -> 2，即912

### 二.题解：
#### 1.第一种方法：
##### (1)解题思路：
* 新建一个链表newHead,新建一个遍历JinWei，用来标识相加是否进位
* 新建一个指针p指向newHead
* 利用while循环遍历，
* 当l1与l2都没有指向空时，sum=l1.val+l2.val+JinWei
* 然后给链表newHead新增元素，p.next=new ListNode(sum%10)
* 然后链表l1,l2,newHead指向下一位,JinWei=sum/10
* 然后当l2遍历结束，然而l1还未结束时，循环遍历，sum=l1.val+JinWei
* 然后链表newHead新增元素,p.next=new ListNode(sum%10)
* 然后链表l1,newHead指向下一位,JinWei=sum/10
* 同理，当l2未遍历结束时，与l1未遍历结束时一样，只是sum=l2.val+JinWei
* 最好判断JinWei是否为1，若JinWei==1，则还需要新建元素p.next=new ListNode(1)
* 最后返回新键的链表newHead.next即可

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
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if(l1==null){
            return l2;
        }
        if(l2==null){
            return l1;
        }

        ListNode newHead = new ListNode(-1);
        ListNode p =newHead;

        int JinWei=0;
        while(l1!=null&&l2!=null){
            int sum = l1.val+l2.val+JinWei;
            p.next=new ListNode(sum%10);
            JinWei=sum/10;

            p=p.next;
            l1=l1.next;
            l2=l2.next;
        }

        while(l1!=null){
            int sum = l1.val+JinWei;
            p.next=new ListNode(sum%10);
            JinWei=sum/10;
            p=p.next;
            l1=l1.next;
        }
        while(l2!=null){
            int sum = l2.val+JinWei;
            p.next=new ListNode(sum%10);
            JinWei=sum/10;
            p=p.next;
            l2=l2.next;

        }
        if(JinWei==1){
            p.next=new ListNode(JinWei);
        }

        return newHead.next;
    }
}
```
