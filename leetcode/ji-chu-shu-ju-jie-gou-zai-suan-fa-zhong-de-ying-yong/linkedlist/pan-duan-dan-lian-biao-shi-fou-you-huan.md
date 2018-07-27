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

## 142 - Linked List Cycle II

### 原题概述

Given a linked list, return the node where the cycle begins. If there is no cycle, return `null`.

**Note:** Do not modify the linked list.

**Follow up**:  
Can you solve it without using extra space?

### 题意和分析

这道题是上一道的延伸，如果有环，判断环的入口，如果没有环就返回null，不要改变原先的链表。下面三种情况，链表无环，链表头尾相接，链表一部分有环。

![](../../../.gitbook/assets/image%20%2832%29.png)

![](../../../.gitbook/assets/image%20%287%29.png)

![](../../../.gitbook/assets/image%20%2828%29.png)

这道题也是先用快慢指针判断该链表是否有环，如果没有环，直接返回null，如果有环\(这时候slow和fast是在一个node上\)，将fast重新指向head（将slow重新指向head也可以），然后继续走，不过这时候fast每次只走一步，若干步和slow和fast相遇的node就是环的入口；

为什么呢？

假定起点到环入口点的距离为 a，slow 和 fast 的相交的node为M，M与环入口点的距离为b，环路的周长为L，当 slow 和 fast 第一次相遇的时候，假定 slow 走了 n 步。那么有：

slow走的路径： a+b ＝ n； fast走的路径： a+b+k_L = 2_n，fast 比 slow 多走了k圈环路，总路程是slow的2倍。

根据上述公式可以得到 k\*L=a+b=n显然（因为是快指针路程是慢指针的2倍），如果从相遇点M开始，slow再走 n 步的话，还可以再回到相遇点，同时fast从头开始走的话，经过n（a+b）步，也会达到相遇点M。

显然在这个步骤当中 slow和 fast 只有前 a 步走的路径不同（b是一样的，所以二者前半截也是一样的长度），所以当 slow 和 fast 再次重合的时候，必然是在链表的环路入口点上。

循环变成两个，但是时间空间复杂度还是一样的。

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
    public ListNode detectCycle(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {//先判断是否有环
            slow = slow.next;
            fast = fast.next.next;

            if (slow == fast) {//有环
                break;
            }
        }
        if (fast == null || fast.next == null) {//没有环
            return null;
        }

        fast = head;//重置fast为head，重置slow也可以
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }

        return fast;//return slow;
    }
}
```

