# LinkedList

链表和数组都是线性结构，但是链表和数组的不同在于数组可以随机的对于数据进行访问。给出索引（或指针）。数组可以以O\(1\)的时间复杂度迅速访问到该元素。链表只能从头指针开始。

链表是最简单的动态数据结构，不需要处理固定容量的问题，同时具有天生的递归性质。理解好链表会帮助我们更好地理解高级动态数据结构，深入理解指针，更好地理解递归，以及如何学习由链表组成的更高级的数据结构（包括栈和队列等基础结构）。

## 链表元素相加

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
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        }
        
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy; // 用一个索引决定遍历哪个链表
        int carry = 0;
        while (l1 != null || l2 != null) { // 把两个链表遍历到最后
            int sum = carry;
            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }
            current.next = new ListNode(sum % 10); // 新建一个加后的node
            current = current.next;
            carry = sum / 10;
        }
        
        // 两个数加完后还要检查一下carry是否有剩
        if (carry != 0) {
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

        ListNode current = new ListNode(-1);
        int carry = 0;
        while (!stack1.isEmpty() || !stack2.isEmpty()) {
            int sum = carry;
            if (!stack1.isEmpty()) {
                sum += stack1.pop();
            }
            if (!stack2.isEmpty()) {
                sum += stack2.pop();
            }
            current.val = sum % 10;//当前结点的val
            carry = sum / 10;
            ListNode newHead = new ListNode(carry);//每次new一个head结点，将newHead的next指向已经创建好的list的头部
            newHead.next = current;
            current = newHead;//将当前结点的更新为前面一个结点

        }
        if (carry != 0) {//每次的newHead是用carry创建的
            return current;
        }
        //current最后在head位置，没有进位的话，之前创建的newHead里面的值为0，直接丢掉
        return current.next;
    }
}
```

用递归来模拟stack的过程

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
        int len1 = getLength(l1);
        int len2 = getLength(l2);
        ListNode current = new ListNode(1);
         
        // Make sure l1.length >= l2.length
        current.next = len1 < len2 ? helper(l2, l1, len2 - len1) : helper(l1, l2, len1 - len2);
         
        // Handle the first digit，优化
        if (current.next.val > 9) {
            current.next.val = current.next.val % 10;
            return current;
        }
        return current.next;
    }
    
    // get length of the linked list
    public int getLength(ListNode l) {
        int count = 0;
        while(l != null) {
            l = l.next;
            count++;
        }
        return count;
    }
    
    // offset is the difference of length between l1 and l2
    public ListNode helper(ListNode l1, ListNode l2, int offset) {
        if (l1 == null) return null;
        // check whether l1 becomes the same length as l2
        ListNode newHead = (offset == 0) ? new ListNode(l1.val + l2.val) : new ListNode(l1.val);
        ListNode post = (offset == 0) ? helper(l1.next, l2.next, 0) : helper(l1.next, l2, offset - 1);
        // handle carry 
        if (post != null && post.val > 9) {
            newHead.val += 1;
            post.val = post.val % 10;
        }
        // combine nodes
        newHead.next = post;
        return newHead;
    }
}
```

## 369 Plus One Linked List

### 原题概述

Given a non-negative integer represented as **non-empty** a singly linked list of digits, plus one to the integer.

You may assume the integer do not contain any leading zero, except the number 0 itself.

The digits are stored such that the most significant digit is at the head of the list.

**Example :**

```text
Input: [1,2,3]
Output: [1,2,4]
```

### 题意和分析

在链表尾部+1，要考虑进位问题，有四种解法：

1）先翻转链表，然后在头部元素+1，处理好进位，最后再翻转过来；

2）递归做法，因为递归是先递到最后一位，这时候+1，然后在回溯的途中处理进位，最后发现还有进位就在表头加一个node；

3）上面递归解法的迭代写法，这个需要额外的空间，利用栈先进后出的特征来+1；

4）遍历链表，找到右起第一个不为9的数字，如果找不到这样的数字，说明所有数字均为9，那么在表头新建一个值为0的新节点，进行加1处理，然后把右边所有的数字都置为0即可。举例来说：

比如1-&gt;2-&gt;3，那么第一个不为9的数字为3，对3进行加1，变成4，右边没有节点了，所以不做处理，返回1-&gt;2-&gt;4。

再比如说8-&gt;9-&gt;9，找第一个不为9的数字为8，进行加1处理变成了9，然后把后面的数字都置0，得到结果9-&gt;0-&gt;0。

再来看9-&gt;9-&gt;9的情况，找不到不为9的数字，那么再前面新建一个值为0的节点，进行加1处理变成了1，把后面的数字都置0，得到1-&gt;0-&gt;0-&gt;0。

### 代码

解法1，翻转链表

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
   public ListNode plusOne(ListNode head) {

      ListNode dummy = new ListNode(-1);
      dummy.next = head;

      ListNode reveredHead = revereNodes(dummy.next);

      ListNode cur = reveredHead;
      ListNode pre = cur;//这个指针用来记录可能需要添加的最后一位
      int carry = 1;
      while (cur != null) {
         pre = cur;
         int temp = cur.val + carry;
         cur.val = temp % 10;
         carry = temp / 10;
         if (carry == 0) {//没有进位，不用往后加了
            break;
         }
         cur = cur.next;
      }
      if (carry != 0) {
         pre.next = new ListNode(carry);
      }
      return revereNodes(reveredHead);
   }
   private ListNode revereNodes(ListNode head) {
      ListNode pre = null;
      ListNode next = null;
      while (head != null) {
         next = head.next;
         head.next = pre;
         pre = head;
         head = next;
      }
      return pre;
   }
}
```

解法2，递归

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
   public ListNode plusOne(ListNode head) {
      if (head == null) {
         return head;
      }
      int carry = recursion(head);
      if (carry == 1) {
         ListNode result = new ListNode(1);
         result.next = head;
         return result;
      }
      return head;
   }
   private int recursion(ListNode node) {
      if (node == null) {//递归到最后一层，返回1，相当于plus one的作用
         return 1;
      }
      int localCarry = recursion(node.next);
      int sum = node.val + localCarry;
      node.val = sum % 10;
      return sum / 10;//最后一次计算的进位
   }
}
```

解法3，利用栈

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
   public ListNode plusOne(ListNode head) {
      if (head == null) {
         return head;
      }
      Stack<ListNode> stack = new Stack<>();
      ListNode cur = head;
      int carry = 1;
      while (cur != null) {
         stack.push(cur);
         cur = cur.next;
      }
      while (!stack.isEmpty() && carry != 0) {
         ListNode temp = stack.pop();
         int sum = temp.val + carry;
         temp.val = sum % 10;
         carry = sum / 10;
      }
      if (carry != 0) {
         ListNode newHead = new ListNode(carry);
         newHead.next = head;
         return newHead;
      }
      return head;
   }
}
```

解法4，寻找最右边的9

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
   public ListNode plusOne(ListNode head) {
      if (head == null) {
         return head;
      }
      ListNode cur = head;
      ListNode right = null;

      while (cur != null) {
         if (cur.val != 9) {
            right = cur;
         }
         cur = cur.next;
      }
      if (right == null) {//全是9的情况
         right = new ListNode(0);
         right.next = head;
         head = right;
      }
      right.val++;
      cur = right.next;//从最右边不为9的node的右边的node开始处理
      while (cur != null) {
         cur.val = 0;
         cur = cur.next;
      }
      return head;
   }
}
```

## 合并有序链表

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

利用虚拟节点（dummy node），虚拟节点是当我们合并两个链表时不知道哪个节点是新链表节点的头节点时会用到。对于我们之前提到的这个问题，我们会用到两个指针p1（指向L1头节点）、p2（指向L2头节点），前面提到的虚拟节点和当前节点（current node，用于循环，被初始化指向虚拟节点）。通过循环访问所有节点来构成新的链表。在每次循环中，会比较两个链表中p1和p2指向的节点的值，使当前节点一直指向较小值节点的下一节点。如下图：

![](../../.gitbook/assets/image%20%2831%29.png)

在第一步中，p1指向的节点值为2，p2指向节点值为6,2小于6，所以curr指向p1，p1指向下一节点，即指向节点值为5的节。

第二步中，p1指向的节点的值仍然小于p2指向的节点的值，所以继续向后移动。

第三步中，p1指向的节点的值大于p2指向的节点的值，curr指向p2当前指向的节点，p2向后移动。

以此类推，直到p1或者p2指针移动到链表的末端，因为我们在每次比较中都选择较小的值，所以当p1或者p2中任何一个指针指向链表末端，代表另外一个没有还没到达末端的链表的后序的值都比已到达末端的链表中最大的值要大，所以只需要将后续的节点追加到新的链表后即可。

也可以用递归的方式解决这个问题。递归（recursion）是一种在计算机科学中非常基本且普遍的思考问题的方式。典型的递归思路就是可以把问题变为更小的问题，并且这个更小的问题可以继续递归成更小的问题，知道这个问题小到到达一个边界条件（boundary condition），以至于可以很轻松地解决。

在这个问题中，我们的边界条件很简单，就是当L2指向了null，返回L1或者L1指向了null，返回L2。如果L1或者L2没有指向null，就需要比较L1和L2对应的值哪个更小，当有结果以后，这个新的链表就更长了，而需要比较的链表就更短了，显然，这样这个问题就比较之前的简单了，如此反复递归即可。

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
| :--- | :--- |
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



### 代码

分治法

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) {
            return null;
        }
        return divideAndConquer(lists, 0, lists.length - 1);
    }
    
    // 分治
    private ListNode divideAndConquer(ListNode[] lists, int left, int right) {
        if (left >= right) { // 需要包含等于（大于条件不会达到），表示自己和自己不用merge，直接返回当前链表
            return lists[left];
        }
        int mid = left + (right - left) / 2;
        ListNode leftList = divideAndConquer(lists, left, mid);
        ListNode rightList = divideAndConquer(lists, mid + 1, right);

        return mergeTwoSortedLists(leftList, rightList); //在递归栈中从底到上lists进行两两合并
        
    }
    
    // "治"，合并两个有序链表
    private ListNode mergeTwoSortedLists(ListNode list0, ListNode list1) {
        if (list0 == null) {
            return list1;
        }
        if (list1 == null) {
            return list0;
        }
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;
        while (list0 != null && list1 != null) {
            if (list0.val <= list1.val) {
                current.next = list0;
                list0 = list0.next;
            } else {
                current.next = list1;
                list1 = list1.next;
            }
            current = current.next;
        }
        current.next = (list0 == null) ? list1 : list0;
        
        return dummy.next;
    }
}
```

堆的办法，用优先队列实现

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) {
            return null;
        }
        
        // default升序，size为lists的大小
        PriorityQueue<ListNode> minHeap = new PriorityQueue<>(lists.length, Comparator.comparingInt(list -> list.val));
        
        // 类似合并两个有序链表的非递归办法，需要创建dummy（递归解法不用创建）
        ListNode dummy = new ListNode(-1);
        ListNode current = dummy;
        
        // 首先遍历一次所有list的头节点，加入到最小堆中
        for (ListNode node : lists) {
            if (node != null) { // 这个条件很重要，排除空链表
                minHeap.offer(node);
            }
        }
        
        while (!minHeap.isEmpty()) {
            current.next = minHeap.poll();
            current = current.next; // 赶紧顺杆往上爬，在有当前最小值的链表上往后检查一步
            
            if (current.next != null) { // 非空才加入进行自平衡
                minHeap.offer(current.next);
            }
        }
        return dummy.next;
    }
}
```

## 删除链表结点

## 237 - Delete Note in a Linked List

### 原题概述

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

### 题意和分析

这道题要删除链表的一个结点，但是并没有给链表的起点。正常的删除过程是把待删除的结点的前一个结点的next指向待删除节点的下一个结点。但这道题没有起点，不知道这个结点的上一个结点，所以做法是将这个结点的下一个结点的值复制到这个结点，然后删除下一个结点。

时间空间都是O\(1\)。

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
    public void deleteNode(ListNode node) {
        node.val = node.next.val;
        node.next = node.next.next;
    }
}
```

## 19 - Remove Nth Node From End of List

### 原题概述

Given a linked list, remove the _n_-th node from the end of list and return its head.

**Example:**

```text
Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.
```

**Note:**

Given _n_ will always be valid.

**Follow up:**

Could you do this in one pass?

### 题意和分析

要求移除链表倒数的第N个节点，n不会大于链表的元素总数，如果两次遍历就简单了，先找到链表长度，然后移除len-N+1的的元素，但是要求一次遍历解决问题，所以就要求遍历到这个结点就应该删除了。应用双指针，一个dummy头防止第一个被删了，双指针slow和fast；首先fast指针向前走N步，然后slow和fast同时走，直到fast走到最后一个元素，此时slow指向的就是要移除元素的前一个元素，将下一个元素移除即可。

### 代码

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(-1);
        ListNode slow = dummy, fast = dummy;
        dummy.next = head;
        
        int steps = n;
        while (steps > 0) { // fast先走n步
            fast = fast.next;
            steps--;
        }
        
        while (fast.next != null) { // 同时走直到fast走到最后一个
            slow = slow.next;
            fast = fast.next;
        }
        
        slow.next = slow.next.next; //此时slow在待移除节点的前一个，移除
        
        return dummy.next;
    }
}
```

## 203 - Remove Linked List Elements

### 原题概述

Remove all elements from a linked list of integers that have value **val**.

**Example:**

```text
Input:  1->2->6->3->4->5->6, val = 6
Output: 1->2->3->4->5
```

### 题意和分析

考察链表的基本操作，因为第一结点有可能也会被删除，所以使用dummy假头，然后返回dummy.next。

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
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(-1);
        ListNode pre = dummy;
        dummy.next = head;
        while (pre.next != null) {
            if (pre.next.val == val) {
                ListNode node = pre.next;
                pre.next = node.next;
                node.next = null;
            } else {
                pre = pre.next;
            }
        }
        return dummy.next;
    }
}
```

当然，Linked List是天然的递归结构，可以用递归来做

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
    public ListNode removeElements(ListNode head, int val) {
        if (head == null) {
            return null;
        }
        head.next = removeElements(head.next, val);
        return head.val == val ? head.next : head;//等于val就跳过现在结点（删除）
    }
}
```

## 判断链表是否是回文

## 234 - Palindrome Linked List

### 原题概述

Given a singly linked list, determine if it is a palindrome.

**Example 1:**

```text
Input: 1->2
Output: false
```

**Example 2:**

```text
Input: 1->2->2->1
Output: true
```

**Follow up:**  
Could you do it in O\(n\) time and O\(1\) space?

### 题意和分析

这道题要求判断链表是否是回文的，跟Array相比链表没办法通过索引来读取后面的元素。首先可以用快慢指针来找到中点，在寻找重点过程中慢指针每走一步就把链表元素存入到一个stack中，当慢指针到达中点的时候就将存入到栈内的前半截元素和后半截进行比较；时间和空间都是O\(n\)，另外用递归的办法也是同样的复杂度。

其次如果想用O\(n\)的时间和O\(1\)的空间，那就在快慢指针找到中点的时候，将后半截链表元素进行翻转后再进行比较。

### 代码

用Stack，先找到中点，把后半截的元素放入到stack中，根据stack后进先出的特点，从头开始比较，时空都是线性的

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
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }
        Stack<Integer> stack = new Stack<>();
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        // 如果是奇数个，slow这时候在正中间，如果是偶数个，slow这时候在中间偏左一个位置
        slow = slow.next;
        
        while (slow != null) {
            stack.push(slow.val);
            slow = slow.next;
        }
        
        slow = head;
        while (!stack.isEmpty()) { //注意这里用iterator或者stream的forEach循环的话就是先进先出了，所以要用pop()
            if (stack.pop() != slow.val) {
                return false;
            }
            slow = slow.next;
        }
        return true;
    }
}
```

用递归，这样就不用翻转链表后半截从而改变结构，做法是用一个索引index记录头结点，然后头递归到最后end，比较index和end的val，然后递归往上一层的同时挪动index到下一步，再进行比较，以此类推。这个做法比较简洁和锻炼递归思维，但空间是常数级的。

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
    ListNode ref; //类变量保证两个methods都可以用同一个变量
    public boolean isPalindrome(ListNode head) {
        ref = head;
        return checkPalindrome(head);
    }
    private boolean checkPalindrome(ListNode node) {
        if (node == null) { //基线条件，null是回文
            return true;
        }
        boolean dcResult = checkPalindrome(node.next); //头递归一直递归到最后一个结点后面的null，返回后到最后一个结点
        if (ref.val != node.val) {//对比第一个结点和最后一个结点，每次递归将ref往后移动与递归本身同步
            return false;
        } else { // 当前对比相等，继续比较下一个元素
            ref = ref.next;
        }
        
        return dcResult; //如果没有返回false，就看子递归返回的boolean值
    }
}
```

翻转后半截链表进行比较

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
    public boolean isPalindrome(ListNode head) {
        if (head == null || head.next == null) {
            return true;
        }

        //先找到中点
        ListNode slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        //倒转后半部分链表
        ListNode last = slow.next;//如果是奇数个元素，slow会停在正中间那个元素处；如果是偶数个元素，slow会停在对半分前半截的最后一个位置；所以检查后半截链表的时候，从slow.next开始进行倒转    
        while (last.next != null) {
            ListNode temp = last.next;
            last.next = temp.next;
            temp.next = slow.next;
            slow.next = temp;
        }

        ListNode pre = head;//将一个指针重新指向前半截链表头部

        //挨个比较
        while (slow.next != null) {
            slow = slow.next;
            if (pre.val != slow.val) {
                return false;
            }
            pre = pre.next;
        }
        return true;
    }
}
```

## 链表中的结点

## 328 - Odd Even Linked List

### 原题概述

Given a singly linked list, group all odd nodes together followed by the even nodes. Please note here we are talking about the node number and not the value in the nodes.

You should try to do it in place. The program should run in O\(1\) space complexity and O\(nodes\) time complexity.

**Example 1:**

```text
Input: 1->2->3->4->5->NULL
Output: 1->3->5->2->4->NULL
```

**Example 2:**

```text
Input: 2->1->3->5->6->4->7->NULL
Output: 2->3->6->7->1->5->4->NULL
```

**Note:**

* The relative order inside both the even and odd groups should remain as it was in the input.
* The first node is considered odd, the second node even and so on ...

### 题意和分析

这道题给一个链表，对奇偶结点进行分组，所有的奇结点在前面，所有偶结点在后面。利用双指针，第一个是奇结点，第二个是偶结点，首先pre指向奇结点，cur指向偶结点，然后把cur后面的奇结点插入到pre的后面，再然后pre和cur同时前进一步，这个时候pre又指向了奇结点，cur又指向了偶结点；如此类推，就把所有的奇结点提到前面来了。

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
    public ListNode oddEvenList(ListNode head) {
        if (head == null || head.next == null || head.next.next == null) {
            return head;
        }

        ListNode pre = head, cur = head.next;
        while (cur != null && cur.next != null) {
            ListNode temp = pre.next;//1->2->3->4->5->NULL，首先pre在1处，cur在2处
            pre.next = cur.next;//X掉1后面的箭头，将1指向3，1->3->4->5->NULL/2->3->4->5->NULL
            cur.next = cur.next.next;//1->3->4->5->NULL/2->4->5->NULL
            pre.next.next = temp;//1->3->2->4->5->NULL

            //两个指针各自前进一位
            pre = pre.next;
            cur = cur.next;
        }
        return head;
    }
}
```

另外一种解法是，奇偶在指针分别指向奇偶结点的起始位置，另外创建一个起始结点even来保存偶结点的起始位置，然后把奇结点指向偶结点的下一个奇结点，然后奇结点后移一步，再把偶结点指向下一个奇结点的下一个（是偶结点），然后再把这个偶结点移一步，以此类推到末尾。

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
    public ListNode oddEvenList(ListNode head) {
        if (head == null || head.next == null || head.next.next == null) {
            return head;
        }

        ListNode odd = head, even = head.next, even_head = even;
        while (even != null && even.next != null) {
            odd = odd.next = even.next;//Java是自右向左逐一赋值
            even = even.next = odd.next;
        }
        odd.next = even_head;

        return head;
    }
}
```

## 判断单链表是否有环

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

![](../../.gitbook/assets/image%20%2842%29.png)

![](../../.gitbook/assets/image%20%287%29.png)

![](../../.gitbook/assets/image%20%2837%29.png)

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

## 链表相交

## 160 - Intersection of Two Linked Lists

### 原题概述

Write a program to find the node at which the intersection of two singly linked lists begins.

For example, the following two linked lists:[![](https://assets.leetcode.com/uploads/2018/12/13/160_statement.png)](https://assets.leetcode.com/uploads/2018/12/13/160_statement.png)

begin to intersect at node c1.

**Example 1:**[![](https://assets.leetcode.com/uploads/2020/06/29/160_example_1_1.png)](https://assets.leetcode.com/uploads/2020/06/29/160_example_1_1.png)

```text
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
Output: Reference of the node with value = 8
Input Explanation: The intersected node's value is 8 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [4,1,8,4,5]. From the head of B, it reads as [5,6,1,8,4,5]. There are 2 nodes before the intersected node in A; There are 3 nodes before the intersected node in B.
```

**Example 2:**[![](https://assets.leetcode.com/uploads/2020/06/29/160_example_2.png)](https://assets.leetcode.com/uploads/2020/06/29/160_example_2.png)

```text
Input: intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
Output: Reference of the node with value = 2
Input Explanation: The intersected node's value is 2 (note that this must not be 0 if the two lists intersect). From the head of A, it reads as [1,9,1,2,4]. From the head of B, it reads as [3,2,4]. There are 3 nodes before the intersected node in A; There are 1 node before the intersected node in B.
```

**Example 3:**[![](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

```text
Input: intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
Output: null
Input Explanation: From the head of A, it reads as [2,6,4]. From the head of B, it reads as [1,5]. Since the two lists do not intersect, intersectVal must be 0, while skipA and skipB can be arbitrary values.
Explanation: The two lists do not intersect, so return null.
```

**Notes:**

* If the two linked lists have no intersection at all, return `null`.
* The linked lists must retain their original structure after the function returns.
* You may assume there are no cycles anywhere in the entire linked structure.
* Each value on each linked list is in the range `[1, 10^9]`.
* Your code should preferably run in O\(n\) time and use only O\(1\) memory.

### 题意和分析

给两个链表，判断是否相交，如果有相交就返回相交的node，没有则相交返回null；不要改变链表的结构，链表中没有环；要求O\(n\)的时间复杂度和O\(1\)的空间复杂度。

解题思路有如下：

1. 直接循环判断第一个链表的每个节点是否在第二个链表中，如果在第二个中说明相交并返回该node。这种方法的时间复杂度为O\(Length\(h1\) \* Length\(h2\)\)；得找到一种更为有效的方法。
2. 针对第一个链表直接构造hash表，然后查询hash表，判断第二个链表的每个节点是否在hash表出现，如果所有的第二个链表的节点都能在hash表中找到，即说明第二个链表与第一个链表有相同的节点。时间复杂度为为线性：O\(Length\(h1\) + Length\(h2\)\)，同时为了存储第一个链表的所有节点，空间复杂度为O\(Length\(h1\)\)。是否还有更好的方法呢，既能够以线性时间复杂度解决问题，又能减少存储空间？
3. 如果两个链长度相同的话，那么对应的一个个比下去就能找到，所以只需要把长链表变短即可。 具体算法为：分别遍历两个链表，得到分别对应的长度。然后求长度的差值，把较长的那个链表向后移动这个差值的个数 （跳过较长的List前面的nodes，从长度相等的地方开始一个一个的比较，直到找到Intersection或者到末尾都没有Intersection。），然后一一比较。
4. 虽然题目中强调了链表中不存在环，但是我们可以用环的思想来做，我们让两条链表分别从各自的开头开始往后遍历，当其中一条遍历到末尾时，我们跳到另一个条链表的开头继续遍历。两个指针最终会相等，而且只有两种情况，一种情况是在交点处相遇（有交叉），另一种情况是在各自的末尾的空节点（没有交叉）处相等。为什么一定会相等呢，因为两个指针走过的路程相同，是两个链表的长度之和，所以一定会相等。这个方法的时间复杂度是线性的，空间是常数，符合要求。
5. 进一步考虑“如果两个没有环的链表相交于某一节点，那么在这个节点之后的所有节点都是两个链表共有的”这个特点，我们可以知道，如果它们相交，则最后一个节点一定是共有的。而我们很容易能得到链表的最后一个节点，所以这成了我们简化解法的一个主要突破口。那么，我们只要判断两个链表的尾指针是否相等。相等，则链表相交；否则，链表不相交。 所以，先遍历第一个链表，记住最后一个节点。然后遍历第二个链表，到最后一个节点时和第一个链表的最后一个节点做比较，如果相同，则相交，否则，不相交。这样我们就得到了一个时间复杂度，它为O\(\(Length\(h1\) + Length\(h2\)\)，而且只用了一个额外的指针来存储最后一个节点。这个方法时间复杂度为线性O\(N\)，空间复杂度为O\(1\)，如果只是判断是否相交这个办法很好，但没有办法知道从哪个node开始相交的。

### 代码

用办法3减length来做

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }

        int lenA = getLength(headA), lenB = getLength(headB);

        if (lenA > lenB) { //如果headA长，让headA从长度相当于headB的长度开始
            for (int i = 0; i < lenA - lenB; ++i) {
                headA = headA.next;
            }
        } else {
            for (int i = 0; i < lenB- lenA; ++i) {//反之从另外一个链表开始
                headB = headB.next;
            }
        }

        while (headA != null && headB != null && headA != headB){//挨个比较，看什么时候相同
            headA = headA.next;
            headB = headB.next;
        }

        return (headA != null && headB != null) ? headA : null; //return (headA != null && headB != null) ? headB : null;  
    }
    public int getLength(ListNode head){
        int count = 0;
        while (head != null) {
            count++;
            head = head.next;
        }
        return count;
    }
}
```

用办法4环的思想来做（做的路程一样）

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }

        ListNode a = headA;
        ListNode b = headB;
        //对两个链表分别遍历，直到其中一条链表到结尾；结束条件如果相交则一定会在开始重合的节点，
        //如果不相交则二者都为null从而跳出循环
        while (a != b) {
            a = (a == null) ? headB : a.next;
            b = (b == null) ? headA : b.next;
        }

        return a;//return b;
    }
}
```

## 单链表的翻转

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

![&#x8FD9;&#x662F;&#x7EBF;&#x6027;&#x8868;&#xFF08;ZHAO, QIAN, SUN, LI, ZHOU, WU, ZHENG, WANG&#xFF09;&#x7684;&#x903B;&#x8F91;&#x72B6;&#x6001;&#x3002;&#x5934;&#x6307;&#x9488;&#x6307;&#x793A;&#x94FE;&#x8868;&#x4E2D;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#xFF08;&#x5373;&#x7B2C;&#x4E00;&#x4E2A;&#x6570;&#x636E;&#x5143;&#x7D20;&#x7684;&#x5B58;&#x50A8;&#x6620;&#x50CF;&#xFF09;&#x7684;&#x5B58;&#x50A8;&#x4F4D;&#x7F6E;&#x3002;&#x540C;&#x65F6;&#xFF0C;&#x7531;&#x4E8E;&#x6700;&#x540E;&#x4E00;&#x4E2A;&#x6570;&#x636E;&#x5143;&#x7D20;&#x6CA1;&#x6709;&#x76F4;&#x63A5;&#x540E;&#x7EE7;&#xFF0C;&#x5219;&#x7EBF;&#x6027;&#x94FE;&#x8868;&#x4E2D;&#x6700;&#x540E;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x4E3A;&#x201C;&#x7A7A;&#x201D;&#xFF08;NULL&#xFF09;&#x3002;](../../.gitbook/assets/image%20%2830%29.png)

![&#x6709;&#x65F6;&#x5728;&#x5355;&#x94FE;&#x8868;&#x7684;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x4E4B;&#x524D;&#x9644;&#x8BBE;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#xFF0C;&#x79F0;&#x4E4B;&#x4E3A;&#x5934;&#x7ED3;&#x70B9;&#x3002;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6570;&#x636E;&#x57DF;&#x53EF;&#x4EE5;&#x4E0D;&#x5B58;&#x50A8;&#x4EFB;&#x4F55;&#x4FE1;&#x606F;&#xFF0C;&#x4E5F;&#x53EF;&#x4EE5;&#x5B58;&#x50A8;&#x5982;&#x7EBF;&#x6027;&#x8868;&#x957F;&#x5EA6;&#x7B49;&#x7C7B;&#x7684;&#x9644;&#x52A0;&#x4FE1;&#x606F;&#xFF0C;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x57DF;&#x5B58;&#x50A8;&#x6307;&#x5411;&#x7B2C;&#x4E00;&#x4E2A;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#xFF08;&#x5373;&#x7B2C;&#x4E00;&#x4E2A;&#x5143;&#x7D20;&#x7ED3;&#x70B9;&#x7684;&#x5B58;&#x50A8;&#x4F4D;&#x7F6E;&#xFF09;&#x3002;&#x6B64;&#x65F6;&#xFF0C;&#x5355;&#x94FE;&#x8868;&#x7684;&#x5934;&#x6307;&#x9488;&#x6307;&#x5411;&#x5934;&#x7ED3;&#x70B9;&#x3002;&#x82E5;&#x7EBF;&#x6027;&#x8868;&#x4E3A;&#x7A7A;&#xFF0C;&#x5219;&#x5934;&#x7ED3;&#x70B9;&#x7684;&#x6307;&#x9488;&#x57DF;&#x4E3A;&#x201C;&#x7A7A;&#x201D;&#x3002;](../../.gitbook/assets/image%20%2845%29.png)

\([http://www.cnblogs.com/keeya/p/9218352.html](http://www.cnblogs.com/keeya/p/9218352.html)\)

* * > 遍历法就是在链表遍历的过程中将指针顺序置换  
    > ![enter image description here](https://images2015.cnblogs.com/blog/571584/201707/571584-20170711122621556-142752551.png)  
    > 先上代码：

  ```text
  public static Node reverseList(Node node) {
    Node pre = null;
    Node next = null;
    while (node != null) {
        next = node.next;
        node.next = pre;
        pre = node;
        node = next;
    }
    return pre;
  }
  ```

  依旧是1-&gt;2-&gt;3-&gt;4

* 准备两个空结点 pre用来保存先前结点、next用来做临时变量
* 在头结点node遍历的时候此时为1结点
  * next = 1结点.next\(2结点\)
  * 1结点.next=pre\(null\)
  * pre = 1结点
  * node = 2结点
* 进行下一次循环node=2结点
  * next = 2结点.next\(3结点\)
  * 2结点.next=pre\(1结点\)=&gt;即完成2-&gt;1
  * pre = 2结点
  * node = 3结点
* 进行循环...

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

![](../../.gitbook/assets/image%20%282%29.png)

### 代码

迭代

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null; // 前一个节点从null开始
        ListNode curr = head; // 当前节点从第一个节点开始
        
        while (curr != null) {
            ListNode temp = curr.next; // 第一步，临时变量记住当前节点的下一个
            curr.next = prev; // 第二步，将当前节点的下一个指向前一个
            prev = curr; //第三步，将前一个指针向后移动一步
            curr = temp; // 第四步，将当前指针往后移动一步
        }
        
        return prev; // 最后返回prev在最后一个节点处，此时curr在最后一个节点后面的null处
    }
}
```

递归

```java
class Solution {
	public ListNode reverseList(ListNode head) {
		if (head == null || head.next == null) {
			return head;
		}
		ListNode result = reverseList(head.next);//逐步递归到最后两个结点
				
		//现在开始翻转两个结点，并“归”回去
		head.next.next = head;
		head.next = null;

		return result;
	}
}
```

## 92 - Reverse Linked List II

### 原题概述

Reverse a linked list from position _m_ to _n_. Do it in one-pass.

**Note:** 1 ≤ _m_ ≤ _n_ ≤ length of list.

**Example:**

```text
Input: 1->2->3->4->5->NULL, m = 2, n = 4
Output: 1->4->3->2->5->NULL
```

### 题意和分析

相对于上一道题，这道题是要求对链表从m到n的区间进行翻转（从位置1开始计算）。方法还是一样的，可以维护指针来标记遍历到哪里了，到m位置处再开始做翻转处理，然后到n位置结束翻转即可。

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

## 25 - Reverse Nodes in k-Group

### 原题概述

Given a linked list, reverse the nodes of a linked list _k_ at a time and return its modified list.

_k_ is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of _k_ then left-out nodes in the end should remain as it is.

**Example:**

Given this linked list: `1->2->3->4->5`

For _k_ = 2, you should return: `2->1->4->3->5`

For _k_ = 3, you should return: `3->2->1->4->5`

**Note:**

* Only constant extra memory is allowed.
* You may not alter the values in the list's nodes, only nodes itself may be changed.

### 题意和分析

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

### 代码

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

迭代的办法比较繁琐，知道递归的办法即可，时间和空间都是O\(n\)

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
            while (count > 0) { // reverse current k-group: 
                ListNode tmp = head.next; // tmp - next head in direct part
                head.next = curr; // preappending "direct" head to the reversed list 
                curr = head; // move head of reversed part to a new node
                head = tmp; // move "direct" head to the next node in direct part
                
                count--;
            }
            head = curr;
        }
        return head;
    }
}
```

## 链表元素去重复

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
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
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

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode curr = head;
        while (curr.next != null) {
            if (curr.val == curr.next.val) {
                // 删除下一个节点
                ListNode temp = curr.next;
                curr.next = curr.next.next;
                temp.next = null; //将重复的下一个节点指向null
            } else {
                curr = curr.next;
            }
            
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
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode prev = dummy, curr = head;
        
        while (curr != null) {
            while (curr.next != null && curr.val == curr.next.val) { //当前值和next值重复
                curr = curr.next;
            }
            if (prev.next == curr) { //上面的while循环没有找到重复值
                prev = prev.next;
            } else { // curr继续去找可能的重复值
                prev.next = curr.next; //将上面while循环的重复值都删掉，暂时将prev头接到curr位置
            }
            
            curr = curr.next; // curr = prev.next也可以，从另外一条路过来
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
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        if (head.val != head.next.val) { 
            head.next = deleteDuplicates(head.next); // 没重复，直接调用下一个节点，返回当前head
            return head;
        } else {
            while (head.next != null && head.val == head.next.val) {
                head = head.next; //有重复，直接抛弃
            }
            return deleteDuplicates(head.next); // 抛弃最后一个重复的节点，返回下一个节点
        }
    }
}
```

## 链表的各种操作 

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

## \* 147 - Insertion Sort List

#### 原题概述

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
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode insertionSortList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode dummy = new ListNode(-1);
        
        // insert 每次寻找需要插入的位置
        insert.next = head;
                
        ListNode prev = head; // 已排序部分的最右边节点
        ListNode curr = head.next; // 未排序部分的最左边节点
        
        while (curr != null) {
            ListNode insert = dummy;
            while (curr.val > insert.next.val && insert != prev) {
                insert = insert.next;
            }
            
            if (insert == prev) { // 当前节点的位置是当前有序部分的最右边
                curr = curr.next;
                prev = prev.next;
            } else { // 插入到有序数组的左边或中间
                prev.next = prev.next.next;
                ListNode temp = insert.next;
                insert.next = curr;
                curr.next = temp;
                
                curr = prev.next;
            }
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

也可以把链表弄成一个环来做

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

      ListNode index = head;
      int len = 1;
      while (index.next != null) {
         index = index.next;
         len++;
      }
      index.next = head;//形成环
      for (int i = 1; i < len - k % len; i++) {
         head = head.next;
      }
      ListNode result = head.next;

      head.next = null;//再断开环形成新的单链表

      return result;
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

要求把小于x的元素按顺序放到链表前面。使用链表最常用的双指针，一个指向当前小于x的最后一个元素，一个进行往前扫描。如果元素大于x，那么继续前进，否则，要把元素移到前面，并更新第一个指针。这里有一个小细节，就是如果不需要移动（也就是已经是接在小于x的最后元素的后面了），那么只需要继续前进即可。算法时间复杂度是O\(n\)，空间只需要几个辅助变量，是O\(1\)。

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
    public ListNode partition(ListNode head, int x) {
        if (head == null) {
            return head;
        }
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode slow = dummy, fast = dummy;

        while (fast.next != null) {//相当于从dummy.next开始
            if (fast.next.val < x) {
                if (slow != fast) {//开始把结点插入到前面(slow后面)
                    ListNode next = fast.next.next;//先记住fast.next的下一位
                    fast.next.next = slow.next;
                    slow.next = fast.next;
                    fast.next = next;//最后把新的fast的next指向开始记住的下一位
                } else {//如果slow和fast相同就只需让fast前移
                    fast = fast.next;
                }
                slow = slow.next;
            } else {//结点的值大于等于x
                fast = fast.next;
            }
        }
        return dummy.next;
    }
}
```

另一种写法，新开两条链表分别记录较大和较小得值，然后链接到一起，时间和空间复杂度都是O（n）

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
   public ListNode partition(ListNode head, int x) {
      ListNode smallHead = new ListNode(-1);
      ListNode bigHead = new ListNode(-1);

      ListNode small = smallHead;
      ListNode big = bigHead;

      while (head != null) {
         ListNode temp = new ListNode(head.val);
         if (head.val < x) {
            small.next = temp;
            small = small.next;
         } else {
            big.next = temp;
            big = big.next;
         }
         head = head.next;
      }
      small.next = bigHead.next;

      return smallHead.next;
   }
}
```

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

这道题做法是分为三步：1）找到链表的中间结点；2）将后半部分的链表倒转；3）将倒转后的后半部分链表插入到前半部分。时间复杂度O\(n\)，空间O\(n\)。

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

这道题还是链表的操作，给一个链表，这个链表在正常链表的基础上还带有一个random pointer，这个random pointer可以指向别的结点，也可以指向null，然后问题是如何deep copy这个链表。

假设原始链表如下，细线表示next指针，粗线表示random指针，没有画出的指针均指向NULL：

![](../../.gitbook/assets/image%20%2820%29.png)

**算法1**：先按照复制一个正常链表的方式复制，复制的时候把复制的结点做一个HashMap，以旧结点为key，新节点为value。这么做的目的是为了第二遍扫描的时候按照这个哈希表把结点的随机指针接上。下图蓝色为原始链表节点，红色为新链表节点：

![](../../.gitbook/assets/image%20%2838%29.png)

![](https://images0.cnblogs.com/blog/517264/201310/24224126-c879cdb9952f447587c976713b5dce38.jpg)

然后在上图的基础上进行如下两步

1、构建新链表的random指针：比如

new1.random = new1.random.random.next, 

new2.random = NULL, 

new3.random = NULL, 

new4.random = new4.random.random.next

2、恢复原始链表：根据最开始保存的原始链表next指针映射关系恢复原始链表

该算法总共要进行两次扫描，所以时间复杂度是O\(2\*n\)=O\(n\)，空间复杂度为需要哈希表来做映射，所以也是O\(N\)。

**算法2**：该算法更为巧妙，不用保存原始链表的映射关系，构建新节点时，指针做如下变化，即把新节点插入到相应的旧节点后面：

![](https://images0.cnblogs.com/blog/517264/201310/24225610-b49aa4b472734a1785fad23a2156cc14.jpg)

同理分两步

1、构建新节点random指针：new1-&gt;random = old1-&gt;random-&gt;next, new2-random = NULL, new3-random = NULL, new4-&gt;random = old4-&gt;random-&gt;next

2、恢复原始链表以及构建新链表：例如old1-&gt;next = old1-&gt;next-&gt;next,  new1-&gt;next = new1-&gt;next-&gt;next

该算法时间复杂度O\(N\)，空间复杂度O\(1\)。

### 代码

HashMap的做法，需要一个哈希表的原因是当我们访问一个结点时可能它的随机指针指向的结点还没有访问过，结点还没有创建，所以需要线性的额外空间，时间和空间都是O\(n\)。

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/

class Solution {
    public Node copyRandomList(Node head) {
        HashMap<Node, Node> map = new HashMap<>(); // k-v分别是节点本身和该节点的随机指针
        Node node = head;
        while (node != null) { // 用散列表保存节点和其随机指针
            map.put(node, new Node(node.val)); // 这里需要new对象，才是deep copy
            node = node.next;
        }
        node = head; // 临时指针指回头部，准备开始copy
        while (node != null) {
            map.get(node).next = map.get(node.next); // 当前节点的next指针通过hashmap的key来寻找，O(1)
            map.get(node).random = map.get(node.random); // 当前节点的random通过hashmap的key来寻找，O(1)
            node = node.next;
        }
        return map.get(head); //返回存储在hashmap的头节点，这时候已经是hashmap + linkedlist的结构了
    }
}

```

in-place的做法，想避免使用额外空间，我们只能通过利用链表原来的数据结构来存储结点。基本思路是这样的，对链表进行三次扫描，第一次扫描对每个结点进行复制，然后把复制出来的新节点接在原结点的next，也就是让链表变成一个重复链表，就是新旧更替；第二次扫描中我们把旧结点的random指针赋给新节点的random指针，因为新结点都跟在旧结点的下一个，所以赋值比较简单，就是node.next.random = node.random.next，其中node.next就是新结点，因为第一次扫描就是把新结点接在旧结点后面。现在把结点的随机指针都接好了，最后一次扫描把链表拆成两个，第一个还原原链表，而第二个就是我们要求的复制链表。因为现在链表是旧新更替，只要把每隔两个结点分别相连，对链表进行分割即可。这个方法总共进行三次线性扫描，所以时间复杂度是O\(n\)。而这里并不需要额外空间，所以空间复杂度是O\(1\)。比起上面的方法，这里多做一次线性扫描，但是不需要额外空间，还是比较值的。

```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/

class Solution {
    public Node copyRandomList(Node head) {
        Node node = head; // 用来遍历的索引
        Node copy = null; // 用来复制当前节点，作为不用额外数据结构而用临时的变量
        Node next = null; // 用来记录当前节点的下一个节点以免丢失
        
        // 第一次遍历，复制节点本身，并附着到该节点的后面，使原来的链表变为二倍size
        while (node != null) {
            next = node.next;
            
            copy = new Node(node.val); // deep copy
            node.next = copy;
            copy.next = next;
            
            node = next;
        }
        
        // 第二次遍历， 把random指针assign到上一步附着的各个copy节点上
        node = head;
        while (node != null) {
            // 当前节点（旧结点A1）的下一个节点（新节点A2）的random指针（旧节点B1），等于当前节点（旧节点A1）的random指针指向的节点（旧节点B1）的next（新节点B2）
            if (node.random != null) {
                node.next.random = node.random.next;
            }
            node = node.next.next; //跳两步，跨过后面自己的复制节点
        }
        
        // 第三次遍历，把双倍size的链表分撤，只剩下第一步复制的节点
        Node dummy = new Node(-1);
        copy = dummy;
        Node newNode = dummy;
        node = head;
        while (node != null) {
            next = node.next.next; // 先记住“双子星”的next
            
            copy = node.next;
            newNode.next = copy;
            newNode = copy; // 新的节点指针移动“一步”，其实是两步
            
            // 还原原先的链表 (如果不需还原则可以省区newNode这个变量)
            node.next = next;
            node = next; // 旧的节点指针移动“一步”，其实是两步
        }
        return dummy.next;
    }
}
```

