# 合并有序链表

## 21  Merge Two Sorted Lists

### 原题概述

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

**Example:**

```text
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```

### 题意和分析

 这道题很可能跟Merge Sorted Array一起问。

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

Merge _k_ sorted linked lists and return it as one sorted list. Analyze and describe its complexity.

**Example:**

```text
Input:
[
  1->4->5,
  1->3->4,
  2->6
]
Output: 1->1->2->3->4->4->5->6
```

### 题意和分析

这道题是多个有序链表合并成一个，在分布式系统中比较常见，比如来自不同客户端的sorted lists数据要在central server上面merge起来。

这个题目一般有两种做法: 

1\)第一种做法比较容易想到，就是有点类似于Merge Sort的思路，就是分治法 - 很重要的经典的O\(nlogn\)的排序算法。思路是先分成两个子任务，然后递归求子任务，最后回溯回来。这个题目也是这样，先把这k个list分成两半，然后继续划分，直到只剩下两个list的时候就合并起来，合并的方法就用上一道Merge Two Sorted Lists的方式。复杂度分析：时间复杂度-假设总共有k个list，每个list的最大长度是n，那么运行时间满足递推式T\(k\) = 2T\(k/2\)+O\(n_k\)。_根据主定理，可以算出算法的总复杂度是O\(nklogk\)。空间复杂度的同样是递归栈的大小O\(logk\)。 

2）第二种方法。这种方法用到了堆的数据结构，思路比较难想到，但是其实原理比较简单。维护一个大小为k的堆，每次取堆顶的最小元素放到结果中，然后读取该元素的下一个元素放入堆中，重新维护好。因为每个链表是有序的，每次又是去当前k个元素中最小的，所以当所有链表都读完时结束，这个时候所有元素按从小到大放在结果链表中。这个算法每个元素要读取一次，即是_k_n次，然后每次读取元素要把新元素插入堆中要logk的复杂度，所以总时间复杂度是O\(nklogk\)。空间复杂度是堆的大小，即为O\(k\)。 两种方法有着同样的时间复杂度，都是可以接受的解法，但是却代表了两种不同的思路，数据结构也不用。两种方法都应该掌握，因为在实际中比较有应用，所以也是比较常考的题目。

### 代码

