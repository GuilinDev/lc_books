# 单链表的翻转

## 206 - Reverse Linked List

### 原题概述

Reverse a singly linked list.

**Example:**

```text
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
```

**Follow up:**

A linked list can be reversed either iteratively or recursively. Could you implement both?

### 题意和分析

翻转一个单向链表，用递归和迭代的办法各实现一下。递归的办法；迭代的办法就是遍历每个结点然后把该节点的赋值为之前链表结点的next，然后把该结点的next赋值为之前链表的结点即可。

Time：都是O\(n\)；Space：递归是O\(n\)，迭代是O\(1\)。

![&#x8FD9;&#x662F;&#x7EBF;&#x6027;&#x8868;&#xFF08;ZHAO, QIAN, SUN, LI, ZHOU, WU, ZHENG, WANG&#xFF09;&#x7684;&#x903B;&#x8F91;&#x72B6;&#x6001;&#x3002;&#x5934;&#x6307;&#x9488;&#x6307;&#x793A;&#x94FE;&#x8868;&#x4E2D;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#xFF08;&#x5373;&#x7B2C;&#x4E00;&#x4E2A;&#x6570;&#x636E;&#x5143;&#x7D20;&#x7684;&#x5B58;&#x50A8;&#x6620;&#x50CF;&#xFF09;&#x7684;&#x5B58;&#x50A8;&#x4F4D;&#x7F6E;&#x3002;&#x540C;&#x65F6;&#xFF0C;&#x7531;&#x4E8E;&#x6700;&#x540E;&#x4E00;&#x4E2A;&#x6570;&#x636E;&#x5143;&#x7D20;&#x6CA1;&#x6709;&#x76F4;&#x63A5;&#x540E;&#x7EE7;&#xFF0C;&#x5219;&#x7EBF;&#x6027;&#x94FE;&#x8868;&#x4E2D;&#x6700;&#x540E;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x4E3A;&#x201C;&#x7A7A;&#x201D;&#xFF08;NULL&#xFF09;&#x3002;](../../../.gitbook/assets/image%20%2818%29.png)

![&#x6709;&#x65F6;&#x5728;&#x5355;&#x94FE;&#x8868;&#x7684;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x4E4B;&#x524D;&#x9644;&#x8BBE;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#xFF0C;&#x79F0;&#x4E4B;&#x4E3A;&#x5934;&#x7ED3;&#x70B9;&#x3002;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6570;&#x636E;&#x57DF;&#x53EF;&#x4EE5;&#x4E0D;&#x5B58;&#x50A8;&#x4EFB;&#x4F55;&#x4FE1;&#x606F;&#xFF0C;&#x4E5F;&#x53EF;&#x4EE5;&#x5B58;&#x50A8;&#x5982;&#x7EBF;&#x6027;&#x8868;&#x957F;&#x5EA6;&#x7B49;&#x7C7B;&#x7684;&#x9644;&#x52A0;&#x4FE1;&#x606F;&#xFF0C;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x57DF;&#x5B58;&#x50A8;&#x6307;&#x5411;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#xFF08;&#x5373;&#x7B2C;&#x4E00;&#x4E2A;&#x5143;&#x7D20;&#x7ED3;&#x70B9;&#x7684;&#x5B58;&#x50A8;&#x4F4D;&#x7F6E;&#xFF09;&#x3002;&#x6B64;&#x65F6;&#xFF0C;&#x5355;&#x94FE;&#x8868;&#x7684;&#x5934;&#x6307;&#x9488;&#x6307;&#x5411;&#x5934;&#x7ED3;&#x70B9;&#x3002;&#x82E5;&#x7EBF;&#x6027;&#x8868;&#x4E3A;&#x7A7A;&#xFF0C;&#x5219;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x57DF;&#x4E3A;&#x201C;&#x7A7A;&#x201D;&#x3002;](../../../.gitbook/assets/image%20%2826%29.png)

### 代码

迭代

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
    public ListNode reverseList(ListNode head) {
        //in-place的做法
        ListNode newHead = null;
        while (head != null) {
            ListNode nextNode = head.next;//先维持住head.next以防丢失
            head.next = newHead;//当前结点的next应该指向上一个结点，初始为null
            newHead = head;//当前结点还是直接赋值过来
            head = nextNode;//继续往后遍历
        }
        return newHead;//最后newHead是头结点
    }
}
```

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
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode nextNode = head.next;
        ListNode newHead = reverseList(nextNode);
        nextNode.next = head;//放置原来的head
        head.next = null;//移除原来的head
        return newHead;
    }
}
```

## 96 - Reverse Linked List II

### 原题概述

### 题意和分析

### 代码

## 25 - Reverse Nodes in k-Group

### 原题概述

### 题意和分析

### 代码

