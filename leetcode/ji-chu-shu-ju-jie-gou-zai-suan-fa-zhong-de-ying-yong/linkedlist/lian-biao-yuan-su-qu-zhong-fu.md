# 链表元素去重复

## 83 - Remove Duplicates from Sorted List

### 原题概述

Given a sorted linked list, delete all duplicates such that each element appear only _once_.

**Example 1:**

```text
Input: 1->1->2
Output: 1->2
```

**Example 2:**

```text
Input: 1->1->2->3->3
Output: 1->2->3
```

### 题意和分析

给一个有序链表，检查里面的元素，去重，让每个元素只出现一次。

方法比较清晰，维护两个指针，一个指向当前不重复的最后一个元素，一个进行依次扫描，遇到不重复的则更新第一个指针，继续扫描，否则就把前面指针指向当前元素的下一个（即把当前元素从链表中删除）。

时间上只需要一次扫描，所以是O\(n\)，空间上两个额外指针，O\(1\)。

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
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return head;
        }
        ListNode previous = head;
        ListNode current = head.next;

        while (current != null) {
            if (previous.val == current.val) {//有重复，跳过（删掉）current的元素
                previous.next = current.next; //current.next如果为null，刚好让尾部不要丢，所以while条件不能是current.next != null
            } else {//没有重复，移动previous指针
                previous = current;
            }
            current = current.next;//每次移动current指针
        }
        return head;
    }
}
```

## 82 - Remove Duplicates from Sorted List II

### 原题概述

Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only _distinct_ numbers from the original list.

**Example 1:**

```text
Input: 1->2->3->3->4->4->5
Output: 1->2->5
```

**Example 2:**

```text
Input: 1->1->1->2->3
Output: 2->3
```

### 题意和分析

给一个链表，检查里面的元素，移除所有有重复的元素，只要重复就一个不留，不重复的保留。这道题与83题的区别就是要把所有重复的node删除。因此，还是利用I中去重的方法，引用双指针，遍历链表，注意这里建立一个dummy head，让dummy head的next指向head，这样最后返回就是dummy.next就行。

同样，时间上只需要一次扫描，所以是O\(n\)，空间上两个额外指针，O\(1\)。

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
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode previous = dummy;
        ListNode current = head;

        while (current != null) {//还没到尾部
            while (current.next != null && current.val == current.next.val) {//有重复，一直将current前移直到没有重复，current会停在最后一个元素的位置（到头了）或者停留在最后一个重复元素的位置
                current = current.next;
            }
            if (previous.next == current) {//没有重复，current就在previous前面一位，直接移动previous
                previous = previous.next;
            } else {//如果current在previous前面很多位，说明有重复，直接将previous.next指向current.next，跳过所有重复的元素
                previous.next = current.next;
            }
            current = current.next;
        }
        return dummy.next;
    }
}
```

也可以用递归的办法，每次判断重复或者不重复，进行相应地递归调用。Recursive调用n层，时间为O\(n\)，空间为O\(n\)。

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
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        if (head.val != head.next.val) {//head.next和head没有重复就一直递归传入head.next
            head.next = deleteDuplicates(head.next);
            return head;
        } else {//有重复，删掉当前的元素
            while (head.next != null && head.val == head.next.val) {
                head = head.next;
            }
            return deleteDuplicates(head.next);//删掉当前元素后再递归调用
        }
    }
}
```

