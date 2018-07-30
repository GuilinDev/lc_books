# LinkedList

链表和数组都是线性结构，但是链表和数组的不同在于数组可以随机的对于数据进行访问。给出索引。可以以O\(1\)的时间复杂度迅速访问到该元素。链表只能从头指针开始。

## 合并有序链表

### 21  Merge Two Sorted Lists

#### 原题概述

Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

**Example:**

```text
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
```

#### 题意和分析

这道题很可能跟[Merge Sorted Array](https://guilindev.gitbook.io/interview/leetcode/ji-chu-shu-ju-jie-gou-zai-suan-fa-zhong-de-ying-yong/array/he-bing-you-xu-de-arrays)一起问。可以用递归方法，判断哪个链表的节点值小就把该节点的下一个节点递归，最后返回最小的那个head；迭代，比较直接，两个链表都不为空的时候按顺序比较就是。

两个方法相同，Time：O\(m+n\)；Space，因为每次递归辅助空间是线性的而不是二分减少，所以应该O\(m + n\)。

#### 代码

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
        ListNode dummy = new ListNode(-1), current = dummy;//维持住头部好返回,-1和0都一样

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
        return dummy.next;
    }
}
```

### 23  Merge k Sorted Lists

#### 原题概述

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

#### 题意和分析

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



2）第二种方法。这种方法用到了堆的数据结构，但是其实原理比较简单。维护一个大小为k的堆，每次取堆顶的最小元素放到最后需要返回的链表中，然后读取该元素的下一个元素放入堆中，重新维护好。因为每个链表是有序的，每次又是取当前k个元素中最小的（k个list），所以当所有链表都读完时就结束了。这个时候所有元素就是按从小到大放入到结果链表中。这个算法每个元素要读取一次，即是_k_n次，然后每次读取元素要把新元素插入堆中要logk的复杂度，所以总时间复杂度是O\(nklogk\)。空间复杂度是堆的大小，即为O\(k\)。

 两种方法有着同样的时间复杂度，都是可以接受的解法，但是却代表了两种不同的思路，数据结构也不同。两种方法都应该掌握，因为在实际中比较有应用，所以也是比较常考的题目。

> **堆 Heap**
>
> 堆实质上是利用完全二叉树（只有最下面的两层结点度能够小于2，并且最下面一层的结点都集中在该层最左边的若干位置的二叉树，所有操作均为对数**log级别**）来维护一组数据的结果，这个完全二叉树满足这些条件：树中任一非叶结点的关键字均不大于（或不小于）其左右孩子（若存在）结点的关键字。所以堆是完全二叉树，分为小根堆和大根堆，如果任意节点的左右孩子（若有）值都不小于其父亲，这是最小堆（min heap，也叫小根堆），即最小的永远在上面；相反的是最大堆（max heap，也叫大根堆），即父节点大于两个子节点。

> 因为完全二叉树有很好的规律，因此可以只用数据来存储数据而不需要链表。

> **优先队列 Priority Queue**
>
> 队列Queue是一个操作受限的线性表（Java中default的优先队列由min heap来实现），数据只能在一端进入，另一端出来，具有先进先出FIFO的性质。有时在队列中需要处理优先级的情况，即后面进入的数据需要提前出来，这时候就需要优先队列。优先队列是至少能够提供插入和获取最小值（获取数值同时删除）这两种操作的数据结构。对应于队列的操作，插入相当于入队，删除最小值相当于出队。 
>
> 链表Linked List，二分查找树BST，都可以提供插入和取得最小值这两种操作。对于链表的实现，插入只需要O\(1\)，删除最小值需要遍历链表\(无论单向还是双向链表都一样，遍历两次\)，故需要O\(N\)。对于二叉查找树，这两种操作都需要O\(logN\)；而且随着不停的删除最小的操作，二叉查找树会变得非常不平衡；同时使用二叉查找树有些浪费，因此很多操作根本不需要。



#### 代码

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

## O\(1\)时间删除链表结点

### 237 - Delete Note in a Linked List

#### 原题概述

Write a function to delete a node \(except the tail\) in a singly linked list, given only access to that node.

Given linked list -- head = \[4,5,1,9\], which looks like following:

```text
    4 -> 5 -> 1 -> 9
```

**Example 1:**

```text
Input: head = [4,5,1,9], node = 5
Output: [4,1,9]
Explanation: You are given the second node with value 5, the linked list
             should become 4 -> 1 -> 9 after calling your function.
```

**Example 2:**

```text
Input: head = [4,5,1,9], node = 1
Output: [4,5,9]
Explanation: You are given the third node with value 1, the linked list
             should become 4 -> 5 -> 9 after calling your function.
```

**Note:**

* The linked list will have at least two elements.
* All of the nodes' values will be unique.
* The given node will not be the tail and it will always be a valid node of the linked list.
* Do not return anything from your function.

#### 题意和分析

这道题要删除链表的一个结点，但是并没有给链表的起点。正常的删除过程是把待删除的结点的前一个结点的next指向待删除节点的下一个结点。但这道题没有起点，不知道这个结点的上一个结点，所以做法是将这个结点的下一个结点的值复制到这个结点，然后删除下一个结点。

时间空间都是O\(1\)。

#### 代码

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
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

## 判断单链表是否有环

### 141 - Linked List Cycle

#### 原题概述

Given a linked list, determine if it has a cycle in it.

Follow up:  
Can you solve it without using extra space?

#### 题意和分析

题目给的链表的class是单链表，判断一个单链表是否有环是经典的快慢指针的应用，只需要设置两个指针，一个每次走一步另一个每次走两步，如果有环的话两个指针迟早会相遇，否则到结尾指向null。

Time：O\(n\); Space：O\(1\);

#### 代码

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

### 142 - Linked List Cycle II

#### 原题概述

Given a linked list, return the node where the cycle begins. If there is no cycle, return `null`.

**Note:** Do not modify the linked list.

**Follow up**:  
Can you solve it without using extra space?

#### 题意和分析

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

#### 代码

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

## 单链表的翻转

### 206 - Reverse Linked List

#### 原题概述

Reverse a singly linked list.

**Example:**

```text
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
```

**Follow up:**

A linked list can be reversed either iteratively or recursively. Could you implement both?

#### 题意和分析

翻转一个单向链表，用递归和迭代的办法各实现一下。递归的办法；迭代的办法就是遍历每个结点然后把该节点的赋值为之前链表结点的next，然后把该结点的next赋值为之前链表的结点即可。

Time：都是O\(n\)；Space：递归是O\(n\)，迭代是O\(1\)。

![&#x8FD9;&#x662F;&#x7EBF;&#x6027;&#x8868;&#xFF08;ZHAO, QIAN, SUN, LI, ZHOU, WU, ZHENG, WANG&#xFF09;&#x7684;&#x903B;&#x8F91;&#x72B6;&#x6001;&#x3002;&#x5934;&#x6307;&#x9488;&#x6307;&#x793A;&#x94FE;&#x8868;&#x4E2D;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#xFF08;&#x5373;&#x7B2C;&#x4E00;&#x4E2A;&#x6570;&#x636E;&#x5143;&#x7D20;&#x7684;&#x5B58;&#x50A8;&#x6620;&#x50CF;&#xFF09;&#x7684;&#x5B58;&#x50A8;&#x4F4D;&#x7F6E;&#x3002;&#x540C;&#x65F6;&#xFF0C;&#x7531;&#x4E8E;&#x6700;&#x540E;&#x4E00;&#x4E2A;&#x6570;&#x636E;&#x5143;&#x7D20;&#x6CA1;&#x6709;&#x76F4;&#x63A5;&#x540E;&#x7EE7;&#xFF0C;&#x5219;&#x7EBF;&#x6027;&#x94FE;&#x8868;&#x4E2D;&#x6700;&#x540E;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x4E3A;&#x201C;&#x7A7A;&#x201D;&#xFF08;NULL&#xFF09;&#x3002;](../../../.gitbook/assets/image%20%2822%29.png)

![&#x6709;&#x65F6;&#x5728;&#x5355;&#x94FE;&#x8868;&#x7684;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x4E4B;&#x524D;&#x9644;&#x8BBE;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#xFF0C;&#x79F0;&#x4E4B;&#x4E3A;&#x5934;&#x7ED3;&#x70B9;&#x3002;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6570;&#x636E;&#x57DF;&#x53EF;&#x4EE5;&#x4E0D;&#x5B58;&#x50A8;&#x4EFB;&#x4F55;&#x4FE1;&#x606F;&#xFF0C;&#x4E5F;&#x53EF;&#x4EE5;&#x5B58;&#x50A8;&#x5982;&#x7EBF;&#x6027;&#x8868;&#x957F;&#x5EA6;&#x7B49;&#x7C7B;&#x7684;&#x9644;&#x52A0;&#x4FE1;&#x606F;&#xFF0C;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x57DF;&#x5B58;&#x50A8;&#x6307;&#x5411;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#xFF08;&#x5373;&#x7B2C;&#x4E00;&#x4E2A;&#x5143;&#x7D20;&#x7ED3;&#x70B9;&#x7684;&#x5B58;&#x50A8;&#x4F4D;&#x7F6E;&#xFF09;&#x3002;&#x6B64;&#x65F6;&#xFF0C;&#x5355;&#x94FE;&#x8868;&#x7684;&#x5934;&#x6307;&#x9488;&#x6307;&#x5411;&#x5934;&#x7ED3;&#x70B9;&#x3002;&#x82E5;&#x7EBF;&#x6027;&#x8868;&#x4E3A;&#x7A7A;&#xFF0C;&#x5219;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x57DF;&#x4E3A;&#x201C;&#x7A7A;&#x201D;&#x3002;](../../../.gitbook/assets/image%20%2834%29.png)

迭代的过程：

1. Initialize three pointers prev as NULL, curr as head and next as NULL.
2. Iterate trough the linked list. In loop, do following.  
   // Before changing next of current,  
   // store next node  
   next = curr-&gt;next

   // Now change next of current  
   // This is where actual reversing happens  
   curr-&gt;next = prev

   // Move prev and curr one step forward  
   prev = curr  
   curr = next

![](https://www.geeksforgeeks.org/wp-content/uploads/RGIF2.gif)

递归的过程

1\) Divide the list in two parts - first node and rest of the linked list. 

2\) Call reverse for the rest of the linked list. 

3\) Link rest to first. 

4\) Fix head pointer

![](../../../.gitbook/assets/image%20%282%29.png)

#### 代码

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
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null;
        ListNode current = head;
        ListNode next = null;
        while (current != null) {
            next = current.next;//记录当前结点的下一位
            current.next = prev;
            prev = current; //挪动前一个结点到现在的结点
            current = next; //指向刚才记录的下一位
        }
        head = prev;//prev会停在最后一个结点处，current会停在最后一个结点后面的null处
        return head;
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

### 92 - Reverse Linked List II

#### 原题概述

Reverse a linked list from position _m_ to _n_. Do it in one-pass.

**Note:** 1 ≤ _m_ ≤ _n_ ≤ length of list.

**Example:**

```text
Input: 1->2->3->4->5->NULL, m = 2, n = 4
Output: 1->4->3->2->5->NULL
```

#### 题意和分析

相对于上一道题，这道题是要求对链表从m到n的区间进行翻转（从位置1开始计算）。方法还是一样的，可以维护指针来标记遍历到哪里了，到m位置处再开始做翻转处理，然后到n位置结束翻转即可。

#### 代码

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
    public ListNode reverseBetween(ListNode head, int m, int n) {
        if (head == null) {
            return null;
        }
        ListNode dummy = new ListNode(-1);
        dummy.next = head;

        ListNode pre = dummy;//记录翻转前的结点
        for (int i = 0, i < m - 1; i++) {//遍历到翻转前的结点
            pre = pre.next;
        }

        ListNode start = pre.next;//开始翻转的第一个结点
        ListNode reversing = start.next;//即将要被翻转的结点

        // 1 - 2 - 3 - 4 - 5 ; m = 2; n = 4 ---> pre = 1, start = 2, reversing = 3
        // dummy-> 1 -> 2 -> 3 -> 4 -> 5

        for (int i = 0; i < n - m; i++) {//翻转的区间
            start.next = reversing.next;
            reversing.next = pre.next;
            pre.next = reversing;
            reversing = start.next;
        }

        // 翻转1: dummy->1 - 3 - 2 - 4 - 5; pre = 1, start = 2, reversing = 4
        // 翻转2: dummy->1 - 4 - 3 - 2 - 5; pre = 1, start = 2, reversing = 5 (finish)

        return dummy.next;
    }
}
```

### 25 - Reverse Nodes in k-Group

#### 原题概述

Given a linked list, reverse the nodes of a linked list _k_ at a time and return its modified list.

_k_ is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of _k_ then left-out nodes in the end should remain as it is.

**Example:**

Given this linked list: `1->2->3->4->5`

For _k_ = 2, you should return: `2->1->4->3->5`

For _k_ = 3, you should return: `3->2->1->4->5`

**Note:**

* Only constant extra memory is allowed.
* You may not alter the values in the list's nodes, only nodes itself may be changed.

#### 题意和分析

这道题要求以k个结点为一组进行翻转，实际上是把原链表分成若干小段，然后分别对其进行翻转，以题目中给的例子来看，对于给定链表1-&gt;2-&gt;3-&gt;4-&gt;5，一般在处理链表问题时，我们大多时候都会在开头再加一个dummy node，因为翻转链表时头结点可能会变化，为了记录当前最新的头结点的位置而引入的dummy node，那么我们加入dummy node后的链表变为-1-&gt;1-&gt;2-&gt;3-&gt;4-&gt;5，如果k为3的话，我们的目标是将1,2,3翻转一下，那么我们需要一些指针，pre和next分别指向要翻转的链表的前后的位置，然后翻转后pre的位置更新到如下新的位置：[![&#x590D;&#x5236;&#x4EE3;&#x7801;](https://common.cnblogs.com/images/copycode.gif)](javascript:void%280%29;)

```text
-1->1->2->3->4->5
 |           |
pre         next

-1->3->2->1->4->5
          |  |
         pre next
```

[![&#x590D;&#x5236;&#x4EE3;&#x7801;](https://common.cnblogs.com/images/copycode.gif)](javascript:void%280%29;)

以此类推，只要next走过k个节点，就可以进行局部翻转了。

也可以使用递归来做，我们用head记录每段的开始位置，current记录结束位置的下一个节点，然后我们调用reverse函数来将这段翻转，然后得到一个newHead，原来的head就变成了末尾，这时候后面接上递归调用下一段得到的新节点，返回newHead即可。

#### 代码

Iterative的办法

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
    public ListNode reverseKGroup(ListNode head, int k) {//1->2->3->4->5 : Say k is 3
        if(k==1) return head;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode ptr1 = dummy;//boundary before the 1st node
        ListNode ptr2 = dummy;//boundary after the kth node
        int count = 0;
        boolean delete = false;//For deleting the last dummy node
        while(count<k){//loop till the kth element : Till 3 in this case
            count++;
            ptr2=ptr2.next;
            if(ptr2==null) break;//if before reaching kth element ptr2 is null, break out of the loop. Example 1->2
            if(count==k){
                if(ptr2.next==null){
                    ptr2.next = new ListNode(-1);//dummy2
                    delete = true;
                }
                ptr2 = ptr2.next;
                count = 0;//Make count 0 to again start with the next group
                ptr1 = reverseK(ptr1,ptr2);
                ptr2 = ptr1;
            }
        }
        if(delete) ptr1.next = null;
        return dummy.next;
    }
    private ListNode reverseK(ListNode ptr1, ListNode ptr2){//ptr1->|1->2->3|->4->5 : ptr1 is dummy and ptr2 is 4 here
        ListNode prev = ptr1;
        ListNode temp1 = ptr1.next;
        ListNode temp2 = ptr1.next.next;
        while(temp1!=ptr2){
            temp1.next = prev;//In the first iteration 1 points to ptr1, Last iteration 3->2
            prev = temp1;//last iteration prev becomes 3
            temp1 = temp2;
            temp2 = temp2.next;
        }
        ptr1.next = prev;//Now a reversed circular list is created, because 3<-ptr1<-1<-2<-3
        while(prev.next!=ptr1){
            prev=prev.next;//till prev points to 1
        }
        //1 must point to 4
        prev.next = ptr2;//K List now is reversed: ptr1->3->2->1->4->5
        return prev;
    }
}
```

递归的办法

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
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode curr = head;
        int count = 0;
        while (curr != null && count != k) { // find the k+1 node
            curr = curr.next;
            count++;
        }
        if (count == k) { // if k+1 node is found
            curr = reverseKGroup(curr, k); // reverse list with k+1 node as head
            // head - head-pointer to direct part, 
            // curr - head-pointer to reversed part;
            while (count-- > 0) { // reverse current k-group: 
                ListNode tmp = head.next; // tmp - next head in direct part
                head.next = curr; // preappending "direct" head to the reversed list 
                curr = head; // move head of reversed part to a new node
                head = tmp; // move "direct" head to the next node in direct part
            }
            head = curr;
        }
        return head;
    }
}
```

