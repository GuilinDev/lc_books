# 合并两个有序链表

## 21  Merge Two Sorted Lists

### 原题概述

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

**Example:**

```text
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```

### 题意和分析



### 代码

递归

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
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        }
        if (l2 == null) {
            return l1;
        }

        if (l1.val <= l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;//l1是第一个node
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;//l2是第一个node
        }
    }
}
```

迭代

```text
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if(l1 == null || l2 == null){
            return l1 == null ? l2 : l1;
        }
        ListNode head = new ListNode(-1), current = head;//维持住头部好返回

        while(l1 != null && l2 != null){
            if(l1.val <=l2.val){
                current.next = l1;
                l1 = l1.next;
            } else{
                current.next = l2;
                l2 = l2.next;
            }
            current = current.next;
        }
        current.next = l1==null ? l2 : l1; //如果一个链表已经迭代结束，那么就把另外一条链表剩余部分直接加入
        return head.next;
    }
}
```

## 23  Merge k Sorted Lists

### 原题概述

### 题意和分析

这道题很可能跟[Merge Sorted Array](https://guilindev.gitbook.io/interview/~/edit/primary/leetcode/ji-chu-shu-ju-jie-gou-zai-suan-fa-zhong-de-ying-yong/array/he-bing-you-xu-de-arrays)一起问。

### 代码

