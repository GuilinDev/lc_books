# 链表元素相加

## 2 - Add Two Numbers

### 原题概述

You are given two **non-empty** linked lists representing two non-negative integers. The digits are stored in **reverse order** and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Example**

```text
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```

### 题意和分析

给两个链表，个位十位百位从左到右存储，两个链表相加返回一个新链表。这道题也是链表的基本操作，创建一个dummy，原先的两个链表挨个结点相加，然后注意下进位就行了。时间O\(m+n\)，空间创建了一个新的链表O\(m\)，m是长度较长的那个链表。

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
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if (l1 == null || l2 == null) {
            return l1 == null ? l2 : l1;
        }
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;
        int carry = 0;
        while (l1 != null || l2 != null) {
            int sum = carry;
            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }
            current.next = new ListNode(sum % 10);
            current = current.next;
            carry = sum / 10;
        }
        if (carry != 0) {//循环结束后最后判断下是否还有进位
            current.next = new ListNode(carry);
        }
        return dummy.next;
    }
}
```

## 445 - Add Two Numbers II

### 原题概述

You are given two **non-empty** linked lists representing two non-negative integers. The most significant digit comes first and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

**Follow up:**  
What if you cannot modify the input lists? In other words, reversing the lists is not allowed.

**Example:**

```text
Input: (7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 8 -> 0 -> 7
```

### 题意和分析

这道题跟前面一道题比，significant digit在前面了，所以个位就在最后。用stack的办法来让最后的位数先相加即可。时间O\(2m + 2n\) = O\(m + n\)；空间创建了两个新的stack，O\(m+n\)。

### 代码

```java
 * Definition for singly-linked list.
         * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if (l1 == null || l2 == null) {
            return l1 == null ? l2 : l1;
        }
        Stack<Integer> stack1 = new Stack<>();
        Stack<Integer> stack2 = new Stack<>();

        while (l1 != null) {
            stack1.push(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            stack2.push(l2.val);
            l2 = l2.next;
        }

        ListNode list = new ListNode(-1);
        int carry = 0;
        while (!stack1.isEmpty() || !stack2.isEmpty()) {
            int sum = carry;
            if (!stack1.isEmpty()) {
                sum += stack1.pop();
            }
            if (!stack2.isEmpty()) {
                sum += stack2.pop();
            }
            list.val = sum % 10;//当前结点的val
            carry = sum / 10;
            ListNode head = new ListNode(carry);//new一个head结点，注意这里的val值需要是carry，两个stack都加完循环跳出后，有进位的话需要返回有正确的val的第一个结点
            head.next = list;//将上一步new出来的head结点插入到当前结点的前面
            list = head;//将当前结点的更新为前面一个结点

        }
        if (carry != 0) {//两个stack中的数都加完了，如果还有进位的话就返回当前结点
            return list;
        }
        return list.next;//没有进位的话，当前结点list的val为上一轮的carry==0，这时候返回list.next
    }
}
```

