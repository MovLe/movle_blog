---
title: '[LeetCode-面试02.01]删除单链表中的重复元素'
date: 2019-04-03 11:00:00
tags: 
- LeetCode
- 面试
- 算法
categories: LeetCode
---

### 一.题目
编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

>示例1:
 输入：[1, 2, 3, 3, 2, 1]
 输出：[1, 2, 3]
示例2:
 输入：[1, 1, 1, 1, 2]
 输出：[1, 2]
提示：
链表长度在[0, 20000]范围内。
链表元素在[0, 20000]范围内

### 二.题解
#### 1.第一种方法：双指针法
##### (1)解题思路：
* HashSet中存入未曾出现的元素，pre和current依次向后推进
* HashSet出现出现过的元素，使用pre和current删除重复结点
* 只用单层循环即可完成目标

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
    public ListNode removeDuplicateNodes(ListNode head) {
        if(head==null){
            return null;
        }
        ListNode pre = new ListNode(0);
        ListNode current = head;
        pre.next=head;
        Set<Integer> set = new HashSet<Integer>();
        while(current!=null){
            if(!set.contains(current.val)){
                pre=current;
                set.add(current.val);
            }else{
                pre.next=current.next;
            }
            current=current.next;
        }
        return head;
    }
}
```