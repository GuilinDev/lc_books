# 链表排序/扭转/分割

## 148 - Sort List

### 原题概述

Sort a linked list in _O_\(_n_ log _n_\) time using constant space complexity.

**Example 1:**

```text
Input: 4->2->1->3
Output: 1->2->3->4
```

**Example 2:**

```text
Input: -1->5->3->4->0
Output: -1->0->3->4->5
```

### 题意和分析

用 _O_\(_n_ log _n_\) 的复杂度对链表中的元素进行排序。既然是_O_\(_n_ log _n_\)复杂度的排序，那自然就是想到快排（复杂度平均）或者归并排序，这里用归并排序比较合适。根据Merge Sort的基本思想，就是找到中间结点，然后对左右两半部分分别进行归并排序，最后对排好序的两部分链表进行merge。通常Merge Sort是针对数组来看的，这里是链表，找到中间结点的办法是快慢指针；然后是合并两个有序链表，迭代或递归。最后返回。

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
    public ListNode sortList(ListNode head) {
        if (head == null || head.next == null) {//递归的出口，只有/只剩一个结点的时候就不再递归
            return head;
        }
        ListNode middle = getMiddleOfList(head);
        ListNode next = middle.next;
        middle.next = null;//把链表断开分为左边（包括middle结点）和右边
        return mergeTwoList(sortList(head), sortList(next));
    }

    //快慢指针找到中间结点
    private ListNode getMiddleOfList(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    //合并两个有序链表
    private ListNode mergeTwoList(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(-1), current = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                current.next = l1;
                l1 = l1.next;
            } else {
                current.next = l2;
                l2 = l2.next;
            }
            current = current.next;
        }
        current.next = l1 == null ? l2 : l1;

        return dummy.next;
    }
}
```

## 147 - Insertion Sort List

### 原题概述

Sort a linked list using insertion sort.

![](https://upload.wikimedia.org/wikipedia/commons/0/0f/Insertion-sort-example-300px.gif)  
A graphical example of insertion sort. The partial sorted list \(black\) initially contains only the first element in the list.  
With each iteration one element \(red\) is removed from the input data and inserted in-place into the sorted list



**Algorithm of Insertion Sort:**

1. Insertion sort iterates, consuming one input element each repetition, and growing a sorted output list.
2. At each iteration, insertion sort removes one element from the input data, finds the location it belongs within the sorted list, and inserts it there.
3. It repeats until no input elements remain.

  
**Example 1:**

```text
Input: 4->2->1->3
Output: 1->2->3->4
```

**Example 2:**

```text
Input: -1->5->3->4->0
Output: -1->0->3->4->5
```

### 题意和分析

首先，For God's sake, don't try sorting a linked list during the interview.所以我们这道题练习链表的基本操作。

要求对链表实现插入排序，这是一种O\(n^2\)复杂度的算法，就是每次循环找到一个元素在当前排好的结果中相对应的位置，然后插进去，经过n次迭代之后就得到排好序的结果了。了解了思路之后就是链表的基本操作了，搜索并进行相应的插入。时间复杂度是排序算法的O\(n^2\)，空间复杂度是O\(1\)。

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
    public ListNode insertionSortList(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode dummy = new ListNode(-1);
        ListNode current = head;//准备插入的的结点
        ListNode pre = dummy;//current结点会插入到pre和pre.next之间
        ListNode next = null; //下一个准备插入的结点

        //插入排序两层循环
        while (current != null) {
            next = current.next;//先把当前结点的尾部维持好
            //找到合适的位置插入
            while (pre.next != null && pre.next.val < current.val) {
                pre = pre.next;
            }
            //找到合适位置后进行插入操作
            current.next = pre.next;
            pre.next = current;
            pre = dummy;
            current = next;
        }
        return dummy.next;
    }
}
```

## 61 - Rotate List

### 原题概述

Given a linked list, rotate the list to the right by _k_ places, where _k_ is non-negative.

**Example 1:**

```text
Input: 1->2->3->4->5->NULL, k = 2
Output: 4->5->1->2->3->NULL
Explanation:
rotate 1 steps to the right: 5->1->2->3->4->NULL
rotate 2 steps to the right: 4->5->1->2->3->NULL
```

**Example 2:**

```text
Input: 0->1->2->NULL, k = 4
Output: 2->0->1->NULL
Explanation:
rotate 1 steps to the right: 2->0->1->NULL
rotate 2 steps to the right: 1->2->0->NULL
rotate 3 steps to the right: 0->1->2->NULL
rotate 4 steps to the right: 2->0->1->NULL
```

### 题意和分析

一个链表把右边k位的结点挪到左边来，可以分三步来做：1）计算链表长度；2）定位到len - k % len的位置因为k可能比len大，所有是k % len；3）在定位好的位置处开始rotate。

时间O\(n\)，空间O\(1\)。

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
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode fast = dummy;
        ListNode slow = dummy;

        //找到链表的长度
        int len;
        for (len = 0; fast.next != null; len++) {
            fast = fast.next;
        }
        //定位到len - k%len的位置
        for (int i = 0; i < len - k%len; i++) {
            slow = slow.next;
        }

        //开始做rotation
        fast.next = dummy.next;
        dummy.next = slow.next;//把i-k%i的节点放到dummy之后（return后的第一位）
        slow.next = null;

        return dummy.next;
    }
}
```

## 86 - Partition List

### 原题概述

Given a linked list and a value _x_, partition it such that all nodes less than _x_ come before nodes greater than or equal to _x_.

You should preserve the original relative order of the nodes in each of the two partitions.

**Example:**

```text
Input: head = 1->4->3->2->5->2, x = 3
Output: 1->2->2->4->3->5
```

### 题意和分析

### 代码

