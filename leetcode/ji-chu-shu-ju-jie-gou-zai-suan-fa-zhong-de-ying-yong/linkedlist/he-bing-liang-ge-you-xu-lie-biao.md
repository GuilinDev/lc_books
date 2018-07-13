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

这道题很可能跟[Merge Sorted Array](https://guilindev.gitbook.io/interview/leetcode/ji-chu-shu-ju-jie-gou-zai-suan-fa-zhong-de-ying-yong/array/he-bing-you-xu-de-arrays)一起问。可以用递归方法，判断哪个链表的节点值小就把该节点的下一个节点递归，最后返回最小的那个head；迭代，比较直接，两个链表都不为空的时候按顺序比较就是。

两个方法相同，Time：O\(m+n\)；Space，因为每次递归辅助空间是线性的而不是二分减少，所以应该O\(m + n\)。

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
        if(l1 == null || l2 == null){
            return l1 == null ? l2 : l1;
        }
        ListNode head = new ListNode(-1), current = head;//维持住头部好返回,-1和0都一样

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

1）第一种做法比较容易想到，就是有点类似于Merge Sort的思路，就是分治法 - 很重要的经典的O\(nlogn\)的排序算法。思路是先分成两个子任务，然后递归求子任务，最后回溯回来。这个题目也是这样，先把这k个list分成两半，然后继续划分，直到只剩下两个list的时候就合并起来，合并的方法就用上一道Merge Two Sorted Lists的方式。复杂度分析：时间复杂度-假设总共有k个list，假设每个list的最大长度是n，那么运行时间满足递推式T\(k\) = 2T\(k/2\)+O\(n_k\)。_根据主定理，可以算出算法的总复杂度是O\(nklogk\)。空间复杂度的同样是递归栈的大小O\(logk + n\)。 

_这里来复习一下Merge Sort\(对于数组操作），归并操作\(merge\)，也叫归并算法，指的是将两个已经排序的序列合并成一个序列的操作。归并排序算法依赖归并操作。_

_归并操作的过程如下：_

1. _申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列_
2. _设定两个指针，最初位置分别为两个已经排序序列的起始位置_
3. _比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置_
4. _重复步骤3直到某一指针到达序列尾_
5. _将另一序列剩下的所有元素直接复制到合并序列尾_

| _最差時間复杂度_ | ![\Theta\(n\log n\)](http://upload.wikimedia.org/math/f/2/9/f296a521bff060cd02c3ef6ee7931dd7.png) |
| --- | --- | --- | --- |
| _最优時間复杂度_ | ![\Theta\(n\)](http://upload.wikimedia.org/math/5/c/1/5c151486ce3904339687c2bac3cccd8d.png) |
| _平均时间复杂度_ | ![\Theta\(n\log n\)](http://upload.wikimedia.org/math/f/2/9/f296a521bff060cd02c3ef6ee7931dd7.png) |
| _最差空间复杂度_ | ![\Theta\(n\)](http://upload.wikimedia.org/math/5/c/1/5c151486ce3904339687c2bac3cccd8d.png) |



2）第二种方法。这种方法用到了堆的数据结构，思路比较难想到，但是其实原理比较简单。维护一个大小为k的堆，每次取堆顶的最小元素放到最后需要返回的链表中，然后读取该元素的下一个元素放入堆中，重新维护好。因为每个链表是有序的，每次又是取当前k个元素中最小的（k个list），所以当所有链表都读完时就结束了。这个时候所有元素就是按从小到大放入到结果链表中。这个算法每个元素要读取一次，即是_k_n次，然后每次读取元素要把新元素插入堆中要logk的复杂度，所以总时间复杂度是O\(nklogk\)。空间复杂度是堆的大小，即为O\(k\)。

 两种方法有着同样的时间复杂度，都是可以接受的解法，但是却代表了两种不同的思路，数据结构也不同。两种方法都应该掌握，因为在实际中比较有应用，所以也是比较常考的题目。

> **堆 Heap**
>
> 堆实质上是利用完全二叉树（只有最下面的两层结点度能够小于2，并且最下面一层的结点都集中在该层最左边的若干位置的二叉树，所有操作均为对数**log级别**）来维护一组数据的结果，这个完全二叉树满足这些条件：树中任一非叶结点的关键字均不大于（或不小于）其左右孩子（若存在）结点的关键字。所以堆是完全二叉树，分为小根堆和大根堆，如果任意节点的左右孩子（若有）值都不小于其父亲，这是最小堆（min heap，也叫小根堆），即最小的永远在上面；相反的是最大堆（max heap，也叫大根堆），即父节点大于两个子节点。

> 因为完全二叉树有很好的规律，因此可以只用数据来存储数据而不需要链表。

> **优先队列 Priority Queue**
>
> 队列Queue是一个操作受限的线性表（Java中default的优先队列由min heap来实现），数据只能在一端进入，另一端出来，具有先进先出FIFO的性质。有时在队列中需要处理优先级的情况，即后面进入的数据需要提前出来，这时候就需要优先队列。优先队列是至少能够提供插入和获取最小值（获取数值同时删除）这两种操作的数据结构。对应于队列的操作，插入相当于入队，删除最小值相当于出队。 
>
> 链表Linked List，二分查找树BST，都可以提供插入和删除最小这两种操作。对于链表的实现，插入只需要O\(1\)，删除最小值需要遍历链表\(无论单向还是双向链表都一样，遍历两次\)，故需要O\(N\)。对于二叉查找树，这两种操作都需要O\(logN\)；而且随着不停的删除最小的操作，二叉查找树会变得非常不平衡；同时使用二叉查找树有些浪费，因此很多操作根本不需要。一种较好的实现优先队列的方式是二叉堆（下面简称堆）。



### 代码

分治法

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
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) {
            return null;
        }
        return divideAndConquer(lists, 0, lists.length - 1);
    }

    private ListNode divideAndConquer(ListNode[] lists, int left, int right) {//分治算法
        if (left == right) {//两个指针相遇了，就停止
            return lists[left];//在lists一半的位置,return lists[right]
        }
        if (left < right) {
            int mid = left + (right - left) / 2;
            ListNode list1 = divideAndConquer(lists, left, mid);
            ListNode list2 = divideAndConquer(lists, mid + 1, right);//这里记得是mid+1
            return mergeTwoSortedLists(list1, list2);//在递归的过程中从底到上传入两个lists进行合并
        } else {
            return null;
        }
    }

    private ListNode mergeTwoSortedLists(ListNode list1, ListNode list2) {//用迭代的办法合并两个有序链表，返回一个合并后的链表
        if (list1 == null || list2 == null) {
            return list1 == null ? list2 : list1;
        }

        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;

        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                current.next = list1;
                list1 = list1.next;
            } else {
                current.next = list2;
                list2 = list2.next;
            }
            current = current.next;
        }
        current.next = list1 == null ? list2 : list1;

        return dummy.next;
    }
}
```

堆的办法

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
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) {
            return null;
        }

        //
        PriorityQueue<ListNode> priorityQueue = new PriorityQueue<ListNode>(lists.length, new Comparator<ListNode>() {//把lists里面所有的第一个结点排好序
            @Override
            public int compare(ListNode l1, ListNode l2) {
                if (l1.val < l2.val) {
                    return -1;
                } else if (l1.val == l2.val) {
                    return 0;
                } else {
                    return 1;
                }
            }
        });

        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;

        for (ListNode node : lists) {//遍历所有链表的第一结点，并加入到优先队列，维护好
            if (node != null) {
                priorityQueue.add(node);
            }
        }

        while (!priorityQueue.isEmpty()) {//堆里面还有元素就继续
            current.next = priorityQueue.poll();//获取并移除此队列的头（最小值），放入到结果链表的next，poll方法如果队列为空，则返回 null。
            current = current.next;//结果链表加入后往后移动一位

            if (current.next != null) {//只要还有链表没有遍历完，current.next肯定还不是null
                priorityQueue.add(current.next);
            }
        }
        return dummy.next;
    }
}
```

