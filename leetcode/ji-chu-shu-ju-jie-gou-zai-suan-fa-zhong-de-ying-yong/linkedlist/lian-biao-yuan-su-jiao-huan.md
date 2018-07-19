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

这道题还是链表的操作，给一个链表，这个链表在正常链表的基础上还带有一个random pointer，这个random pointer可以指向别的结点，也可以指向null，然后问题是如何deep copy这个链表。

假设原始链表如下，细线表示next指针，粗线表示random指针，没有画出的指针均指向NULL：

![](https://images0.cnblogs.com/blog/517264/201310/24223337-cf2f97805d17474bb9c0568d36db9af9.jpg)

**算法1**：先按照复制一个正常链表的方式复制，复制的时候把复制的结点做一个HashMap，以旧结点为key，新节点为value。这么做的目的是为了第二遍扫描的时候按照这个哈希表把结点的随机指针接上。下图蓝色为原始链表节点，红色为新链表节点：

![](https://images0.cnblogs.com/blog/517264/201310/24224126-c879cdb9952f447587c976713b5dce38.jpg)

然后在上图的基础上进行如下两步

1、构建新链表的random指针：比如new1.random = new1.random.random.next, new2.random = NULL, new3.random = NULL, new4.random = new4.random.random.next

2、恢复原始链表：根据最开始保存的原始链表next指针映射关系恢复原始链表

该算法总共要进行两次扫描，所以时间复杂度是O\(2\*n\)=O\(n\)，空间复杂度为需要哈希表来做映射，所以也是O\(N\)。

**算法2**：该算法更为巧妙，不用保存原始链表的映射关系，构建新节点时，指针做如下变化，即把新节点插入到相应的旧节点后面：

![](https://images0.cnblogs.com/blog/517264/201310/24225610-b49aa4b472734a1785fad23a2156cc14.jpg)

同理分两步

1、构建新节点random指针：new1-&gt;random = old1-&gt;random-&gt;next, new2-random = NULL, new3-random = NULL, new4-&gt;random = old4-&gt;random-&gt;next

2、恢复原始链表以及构建新链表：例如old1-&gt;next = old1-&gt;next-&gt;next,  new1-&gt;next = new1-&gt;next-&gt;next

该算法时间复杂度O\(N\)，空间复杂度O\(1\)。

### 代码

HashMap的做法

```java
/**
 * Definition for singly-linked list with a random pointer.
 * class RandomListNode {
 *     int label;
 *     RandomListNode next, random;
 *     RandomListNode(int x) { this.label = x; }
 * };
 */
public class Solution {
    public RandomListNode copyRandomList(RandomListNode head) {
        Map<RandomListNode, RandomListNode> map = new HashMap<>();

        //第一个循环，将所有nodes copy到hashmap中，旧结点为key，新结点为value
        RandomListNode node = head;
        while (node != null) {
            map.put(node, new RandomListNode(node.label));
            node = node.next;
        }

        node = head;//重新将node指向第一个结点
        //第二个循环，assign随机指针和next
        while (node != null) {
            map.get(node).next = map.get(node.next);//随机找
            map.get(node).random = map.get(node.random);
            node = node.next;
        }
        return map.get(head);//返回第一结点
    }
}
```



