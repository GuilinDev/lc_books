# Heap

## 295 Find Median from Data Stream

### 题目

Median is the middle value in an ordered integer list. If the size of the list is even, there is no middle value. So the median is the mean of the two middle value.For example,

`[2,3,4]`, the median is `3`

`[2,3]`, the median is `(2 + 3) / 2 = 2.5`

Design a data structure that supports the following two operations:

* void addNum\(int num\) - Add a integer number from the data stream to the data structure.
* double findMedian\(\) - Return the median of all elements so far.

**Example:**

```text
addNum(1)
addNum(2)
findMedian() -> 1.5
addNum(3) 
findMedian() -> 2
```

**Follow up:**

1. If all integer numbers from the stream are between 0 and 100, how would you optimize it?
2. If 99% of all integer numbers from the stream are between 0 and 100, how would you optimize it?

### 分析

最直接的的思路是，数据流新进来一个数，都把它与已经读出来的数进行一次排序，这样中位数就可以很容易得到。这样做“不好”的地方是：

> 排序的时间复杂度为 O\(NlogN\)，但事实上，我们对除了中位数以外的其它位置的元素并不关心。

我们只需关心在中间的那两个数（或者一个数），其它数没有必要自己进行 “比较” 和 “交换” 的操作。

在我们学习过的数据结构里，堆就有类似的性质，每次都从自平衡的二叉堆里得到一个 “最值” 而其它元素无需排序，这样就可以以 O\(logN\) 的复杂度每次都从堆中取出最值。

为了找到添加新数据以后，数据流的中位数，维护两个堆，一个大顶堆和一个小顶堆，让数据流中进来的这个新数据在大顶堆和小顶堆中都走了一遍。

![&#x6CE8;&#x610F;&#x5806;&#x662F;&#x5B50;&#x8282;&#x70B9;&#x4E0D;&#x5927;&#x4E8E;&#x6216;&#x4E0D;&#x5C0F;&#x4E8E;&#x7236;&#x8282;&#x70B9;&#x7684;&#x5B8C;&#x5168;&#x4E8C;&#x53C9;&#x6811;&#xFF0C;&#x5E76;&#x975E;BST](../.gitbook/assets/image%20%2866%29.png)

而为了让大顶堆的元素多 1 个，我们需要从小顶堆中又拿出一个元素“送回”给大顶堆，任何时候两个堆满足以下条件：

![](../.gitbook/assets/image%20%2867%29.png)

可以如下操作：

![](../.gitbook/assets/image%20%2865%29.png)

### 代码

```java
class MedianFinder {

    private int count;
    private PriorityQueue<Integer> minHeap; // 小顶堆hold右半截较大的数
    private PriorityQueue<Integer> maxHeap; // 大顶堆hold左半截较小的数
    
    /** initialize your data structure here. */
    public MedianFinder() {
        count = 0;
        minHeap = new PriorityQueue<>();
        maxHeap = new PriorityQueue<>((x, y) -> (y - x));
    }
    
    public void addNum(int num) {
        count++;
        maxHeap.offer(num);
        minHeap.offer(maxHeap.poll());
        // 如果两个堆合起来的元素个数只要是奇数，小顶堆要拿出堆顶元素给大顶堆（每隔一轮把堆顶的最小元素给大顶堆）
        if ((count & 1) != 0) {
            maxHeap.offer(minHeap.poll());
        }
    }
    
    public double findMedian() {
        // 如果两个堆合起来的元素个数是偶数，数据流的中位数就是各自堆顶元素的平均值
        if ((count & 1) == 0) {
            return (double)(maxHeap.peek() + minHeap.peek()) / 2;
        } else { // 如果两个堆合起来的元素个数是奇数，数据流的中位数大顶堆的堆顶元素
            return (double) maxHeap.peek();
        }
    }
}

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder obj = new MedianFinder();
 * obj.addNum(num);
 * double param_2 = obj.findMedian();
 */
```

## 239 Sliding Window Maximum

### 题目

Given an array _nums_, there is a sliding window of size _k_ which is moving from the very left of the array to the very right. You can only see the _k_ numbers in the window. Each time the sliding window moves right by one position. Return the max sliding window.

**Follow up:**  
Could you solve it in linear time?

**Example:**

```text
Input: nums = [1,3,-1,-3,5,3,6,7], and k = 3
Output: [3,3,5,5,6,7] 
Explanation: 

Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**Constraints:**

* `1 <= nums.length <= 10^5`
* `-10^4 <= nums[i] <= 10^4`
* `1 <= k <= nums.length`

### 分析

返回给定size子数组里面最大值组成的数组。

遍历数组，将数存放在双向队列中，并用L,R来标记窗口的左边界和右边界。队列中保存的并不是真的数，而是该数值对应的数组下标位置，并且数组中的数要从大到小排序。如果当前遍历的数比队尾的值大，则需要弹出队尾值，直到队列重新满足从大到小的要求。刚开始遍历时，L和R都为0，有一个形成窗口的过程，此过程没有最大值，L不动，R向右移。当窗口大小形成时，L和R一起向右移，每次移动时，判断队首的值的数组下标是否在\[L,R\]中，如果不在则需要弹出队首的值，当前窗口的最大值即为队首的数。

示例

```text
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7]

解释过程中队列中都是具体的值，方便理解，具体见代码。
初始状态：L=R=0,队列:{}
i=0,nums[0]=1。队列为空,直接加入。队列：{1}
i=1,nums[1]=3。队尾值为1，3>1，弹出队尾值，加入3。队列：{3}
i=2,nums[2]=-1。队尾值为3，-1<3，直接加入。队列：{3,-1}。此时窗口已经形成，L=0,R=2，result=[3]
i=3,nums[3]=-3。队尾值为-1，-3<-1，直接加入。队列：{3,-1,-3}。队首3对应的下标为1，L=1,R=3，有效。result=[3,3]
i=4,nums[4]=5。队尾值为-3，5>-3，依次弹出后加入。队列：{5}。此时L=2,R=4，有效。result=[3,3,5]
i=5,nums[5]=3。队尾值为5，3<5，直接加入。队列：{5,3}。此时L=3,R=5，有效。result=[3,3,5,5]
i=6,nums[6]=6。队尾值为3，6>3，依次弹出后加入。队列：{6}。此时L=4,R=6，有效。result=[3,3,5,5,6]
i=7,nums[7]=7。队尾值为6，7>6，弹出队尾值后加入。队列：{7}。此时L=5,R=7，有效。result=[3,3,5,5,6,7]

```

### 代码

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return new int[0];
        }
        
        // 双向队列 保存当前窗口最大值的数组位置 保证队列中数组位置的数值按从大到小排序
        LinkedList<Integer> queue = new LinkedList();
        
        // 结果数组
        int[] result = new int[nums.length - k + 1];
        
        // 遍历nums数组
        for(int i = 0;i < nums.length;i++){
            // 保证从大到小 如果前面数小于等于新来的数的的话，前面的数则需要依次弹出，直至满足要求
            while(!queue.isEmpty() && nums[queue.peekLast()] <= nums[i]){
                queue.pollLast();
            }
            
            // 添加当前值对应的数组下标
            queue.offerLast(i);
            
            // 判断当前队列中队首的值是否有效
            if(queue.peek() <= i - k){
                queue.poll();   
            } 
            // 当窗口长度为k时 保存当前窗口中最大值
            if(i + 1 >= k){
                result[i + 1 - k] = nums[queue.peek()];
            }
        }
        return result;
        
    }
}
```

Deque的做法

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0) {
            return new int[0];
        }
        int len = nums.length;
        int[] result = new int[len - k + 1];
        
        Deque<Integer> window = new ArrayDeque<>(); // 记录窗口内元素的indices，也可以linklist
        int index = 0;
        
        for (int i = 0; i <= len - 1; i++) {
            if (!window.isEmpty() && window.peek() < i - k + 1) { //队列头顶元素的index在过了做窗口后需要删除
                window.poll();
            }
            while (!window.isEmpty() && nums[window.peekLast()] <= nums[i]) { //队列中比新进来元素小的元素用不到
                window.pollLast();
            }
            window.offer(i); // 将当前元素的index加入到队列中
            
            if (i >= k - 1) { // 前k-1个元素暂时没有最大值
                result[index++] = nums[window.peek()];
            }            
        }
        return result;
    }
}
```

## 373 Find K Pairs with Smallest Sums

### 题目

You are given two integer arrays **nums1** and **nums2** sorted in ascending order and an integer **k**.

Define a pair **\(u,v\)** which consists of one element from the first array and one element from the second array.

Find the k pairs **\(u1,v1\),\(u2,v2\) ...\(uk,vk\)** with the smallest sums.

**Example 1:**

```text
Input: nums1 = [1,7,11], nums2 = [2,4,6], k = 3
Output: [[1,2],[1,4],[1,6]] 
Explanation: The first 3 pairs are returned from the sequence: 
             [1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]
```

**Example 2:**

```text
Input: nums1 = [1,1,2], nums2 = [1,2,3], k = 2
Output: [1,1],[1,1]
Explanation: The first 2 pairs are returned from the sequence: 
             [1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]
```

**Example 3:**

```text
Input: nums1 = [1,2], nums2 = [3], k = 3
Output: [1,3],[2,3]
Explanation: All possible pairs are returned from the sequence: [1,3],[2,3]
```

### 分析

1\)  暴力的想法是，维护一个大小为k的大顶堆把所有可能的结果加入堆即可，这没有利用到两个数组是有序的这一条件。  
时间复杂度 O\(m_n_logk\)

2\) 类似Ugly Number的做法，记录nums1\[i\]在nums2中当前的候选元素 

时间复杂度O\(k\*len\(nums1\)\)

3\) 利用小顶堆堆进一步优化上面方法

 时间复杂度包括堆的初始化nlogn 和res的更新klogn 总O\(\(k+n\)longn\)

### 代码

暴力

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        k = Math.min(k, nums1.length * nums2.length); //注意k的取值
        if (k == 0) {
            return result;
        }

        Queue<int[]> pq = new PriorityQueue<>(k, (o1, o2) -> o2[0] + o2[1] - o1[0] - o1[1]);
        for (int e1 : nums1)
            for (int e2 : nums2) {
                if (pq.size() < k) {
                    pq.offer(new int[]{e1, e2});
                } else if (e1 + e2 <= pq.peek()[0] + pq.peek()[1]) {
                    pq.poll();
                    pq.offer(new int[]{e1, e2});
                }
            }
        while (k-- > 0) {
            int[] e = pq.poll();
            result.add(0, Arrays.asList(e[0], e[1]));
        }

        return result;

    }
}
```

记录一个数组的数在另一个数组位置的方法

```java
class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        k = Math.min(k, nums1.length * nums2.length);

        int[] ptrs = new int[nums1.length]; //ptrs[i]=j表示nums1[i]此时候选元素是nums2[j]
        while (k-- > 0) {
            int minIndex = -1, min = 0x7fffffff;
            for (int i = 0; i < nums1.length; i++) { //在nums[i]的每个候选中挑选最小的【此处可以利用小顶堆快速找到，从而实现优化】
                if (ptrs[i] < nums2.length && nums1[i] + nums2[ptrs[i]] < min) {
                    minIndex = i;
                    min = nums1[i] + nums2[ptrs[i]];
                }
            }
            result.add(Arrays.asList(nums1[minIndex], nums2[ptrs[minIndex]]));
            ptrs[minIndex]++;
        }
        return result;
    }
}
```

最小堆

```java
class Node {
    int v; //nums1的元素值
    int idx; //v对应的在nums2中的候选值索引

    public Node(int _v, int _idx) {
        v = _v;
        idx = _idx;
    }
}


class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> res = new ArrayList<>();
        k = Math.min(k, nums1.length * nums2.length);
        if (k == 0) return res;

        Queue<Node> pq = new PriorityQueue<>(nums1.length,
                (o1, o2) -> o1.v + nums2[o1.idx] - o2.v - nums2[o2.idx]);
        for (int i = 0; i < nums1.length; i++) {
            pq.offer(new Node(nums1[i], 0));
        }

        while (k-- > 0) {
            res.add(Arrays.asList(pq.peek().v, nums2[pq.peek().idx]));
            pq.peek().idx++;
            if (pq.peek().idx == nums2.length)
                pq.poll();
            else
                pq.offer(pq.poll()); //因为pq.peek().idx更新了，需要把堆顶Node取出后再重新放入
        }

        return res;
    }
}
```

## 703 Kth Largest Element in a Stream

### 题目

Design a class to find the **k**th largest element in a stream. Note that it is the kth largest element in the sorted order, not the kth distinct element.

Your `KthLargest` class will have a constructor which accepts an integer `k` and an integer array `nums`, which contains initial elements from the stream. For each call to the method `KthLargest.add`, return the element representing the kth largest element in the stream.

**Example:**

```text
int k = 3;
int[] arr = [4,5,8,2];
KthLargest kthLargest = new KthLargest(3, arr);
kthLargest.add(3);   // returns 4
kthLargest.add(5);   // returns 5
kthLargest.add(10);  // returns 5
kthLargest.add(9);   // returns 8
kthLargest.add(4);   // returns 8
```

**Note:**  
You may assume that `nums`' length ≥ `k-1` and `k` ≥ 1.

### 分析

用优先队列实现一个最小堆，记录第k个，比较简单直接。

### 代码

```java
class KthLargest {
    PriorityQueue<Integer> pq;
    int k;
    public KthLargest(int k, int[] nums) {
        pq = new PriorityQueue<>(k);
        this.k = k;
        for (int num : nums) {
            add(num);
        }
    }
    
    public int add(int val) {
        if (pq.size() < k) {
            pq.offer(val);
        }else if (pq.peek() < val) {
            pq.poll();
            pq.offer(val);
        }
        return pq.peek();
    }
}

/**
 * Your KthLargest object will be instantiated and called as such:
 * KthLargest obj = new KthLargest(k, nums);
 * int param_1 = obj.add(val);
 */
```

