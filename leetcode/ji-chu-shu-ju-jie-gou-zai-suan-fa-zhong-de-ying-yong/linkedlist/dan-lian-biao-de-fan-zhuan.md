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

* 
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

