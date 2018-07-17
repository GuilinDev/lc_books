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

这道题做法是分为三部：1）找到链表的中间结点；2）将后半部分的链表倒转；3）将倒转后的后半部分链表插入到前半部分。时间复杂度O\(n\)，空间O\(n\)。

也可以用stack的办法来做，先把所有结点存入到一个stack并计算长度，然后重新遍历链表，同时把所有结点弹出，超过长度一半的时候弹出的那些结点挨个插入，复杂度一样。

### 代码

后半截reverse的做法

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
    public void reorderList(ListNode head) {
        if (head == null) {
            return;
        }

        // Find the middle node
        ListNode slow = head, fast = head.next;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        //前半部分包含了中间结点slow，将后半部分倒转
        ListNode head2 = reverse(slow.next);
        slow.next = null;

        // Link the two halves together
        while (head != null && head2 != null) {
            ListNode tmp1 = head.next;
            ListNode tmp2 = head2.next;
            head2.next = head.next;
            head.next = head2;
            head = tmp1;
            head2 = tmp2;
        }
    }

    private ListNode reverse(ListNode n) {
        ListNode prev = null;
        ListNode cur = n;
        while (cur != null) {
            ListNode tmp = cur.next;
            cur.next = prev;
            prev = cur;
            cur = tmp;
        }
        return prev;
    }
}
```

用Stack的做法

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
    public void reorderList(ListNode head) {
        if(head != null && head.next != null){
            int len = 0;
            Stack<ListNode> stack = new Stack<>();
            ListNode curNode = head;

            while(curNode != null) {//将全部结点存入到stack中，并计算链表长度
                stack.push(curNode);
                curNode = curNode.next;
                len++;
            }

            curNode = head;//重新指向头部
            while(len >= len / 2){//遍历过一半的时候开始将stack中剩下的一半结点插入
                ListNode tmp = curNode.next;
                curNode.next = stack.pop();
                curNode.next.next = tmp;

                curNode = curNode.next.next;
                len -= 2;
            }

            if(len > 1) {
                curNode.next = stack.pop();
                curNode = curNode.next;
            }

            curNode.next = null;//删除原来链表中的已被插入的结点
        }
    }
}
```

## 138 - Copy List with Random Pointer

### 原题概述

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.

### 题意和分析



### 代码

```java

```

