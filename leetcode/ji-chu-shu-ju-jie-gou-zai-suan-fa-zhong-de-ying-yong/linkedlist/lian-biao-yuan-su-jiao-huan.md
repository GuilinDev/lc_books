# 链表交换/重组/复制

## 24 - Swap Nodes in Pairs

### 原题概述

Given a linked list, swap every two adjacent nodes and return its head.

**Example:**

```text
Given 1->2->3->4, you should return the list as 2->1->4->3.
```

**Note:**

* Your algorithm should use only constant extra space.
* You may **not** modify the values in the list's nodes, only nodes itself may be changed.

### 题意和分析

给一个链表，两两交换位置，每次跳两位，比如第一结点和第二结点交换位置，但第二结点和第三结点就不必交换位置，如果是单数个结点，就剩一个；要求空间为常数，所以如果用递归，先递归到最后一个结点，然后和倒数第二个交换的这种办法，因为空间是O\(n\)所以不行。另外也不能通过改变value的值来“交换”，只能交换结点。

时间复杂度O\(n\)。

### 代码

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
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode current = dummy;

        while (current.next != null && current.next.next != null) {//current的后两位进行交换
            ListNode first = current.next;
            ListNode second = current.next.next;
            first.next = second.next;//将第一个结点next指向第二个结点的next
            current.next = second;//将前面dummy或者已经交换过的结点（head）指向正在交换的的第二个结点
            current.next.next = first;//将“head”指向原本的第一个结点

            current = current.next.next;//让“head”跳两位
        }
        return dummy.next;
    }
}
```



递归的办法是先遍历到链表末尾，先交换最后两个，依次向前；空间复杂度不符合题目要求，但是依然可以过test cases。

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
    public ListNode swapPairs(ListNode head) {
        if (head == null) {
            return null;
        }
        if (head.next == null) {
            return head;
        }
        ListNode n = head.next;
        head.next = swapPairs(head.next.next);//每次跳两位递归
        n.next = head;
        return n;
    }
}
```

## 143 - Reorder List

### 原题概述

Given a singly linked list _L_: _L_0→_L_1→…→_Ln_-1→_L_n,  
reorder it to: _L_0→_Ln_→_L_1→_Ln_-1→_L_2→_Ln_-2→…

You may **not** modify the values in the list's nodes, only nodes itself may be changed.

**Example 1:**

```text
Given 1->2->3->4, reorder it to 1->4->2->3.
```

**Example 2:**

```text
Given 1->2->3->4->5, reorder it to 1->5->2->4->3.
```

### 题意和分析

### 代码

## 138 - Copy List with Random Pointer

### 原题概述

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.

### 题意和分析

### 代码

