---
title: '[LeetCode-面试02.02]返回倒数第 k 个节点'
date: 2019-04-05 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目：
实现一种算法，找出单向链表中倒数第 k 个节点。返回该节点的值。
注意：本题相对原题稍作改动

>示例：
输入： 1->2->3->4->5 和 k = 2
输出： 4

### 二.题解：
#### 1.第一种解法
##### (1)解题思路:
![双指针法](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE1NmQwMjJmYjUyZjA0N2MucG5n?x-oss-process=image/format,png)

* 新建指针p，指向头结点
* 再利用for循环，让p后移k位
* 再利用while循环，同时使p向后移，head指针也向后移
* 直到p指向null，即尾结点，此时head指向的就是倒数第k个结点
* 返回此时head的val值即可

##### (2)代码
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
    public int kthToLast(ListNode head, int k) {
        ListNode p = head;

        for(int i=0;i<k;i++){
            p=p.next;
        }

        while(p!=null){
            p=p.next;
            head=head.next;
        }
        return head.val;
    }
}
```