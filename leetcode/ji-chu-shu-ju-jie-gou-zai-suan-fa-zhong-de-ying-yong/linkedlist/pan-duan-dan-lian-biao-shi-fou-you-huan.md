# 判断单链表是否有环

## 141 - Linked List Cycle

### 原题概述

Given a linked list, determine if it has a cycle in it.

Follow up:  
Can you solve it without using extra space?

### 题意和分析

题目给的链表的class是单链表，判断一个单链表是否有环是经典的快慢指针的应用，只需要设置两个指针，一个每次走一步另一个每次走两步，如果有环的话两个指针迟早会相遇，否则到结尾指向null。

Time：O\(n\); Space：O\(1\);

### 代码

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {//fast每次走两步，所以本身判断是否为null，下一个也判断下，免得一下走两步就null pointer了
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {//相遇说明有环，直接返回
                return true;
            }
        }
        return false;
    }
}
```

