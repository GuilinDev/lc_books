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
        
        if (nums == null || k <= 0) {
            return new int[0];
        }
        int len = nums.length;

        //如果原本元素个数少于k，直接查一遍
        if (len <= k) {
            int max = Integer.MIN_VALUE;
            for (int num : nums) {
                //max = num > max ? num : max;
                if (num > max) {
                    max = num;
                }
            }
            return new int[]{max};
        }
        
        int[] result = new int[len - k + 1];
        
        Deque<Integer> window 
            = new ArrayDeque<Integer>(k);//队列中存的是元素的下标
        
        //刚开始窗口为空的时候，将第一个元素的下标default放到队列中去
            window.offer(0);

        //依次将每一个元素循环，放入到窗口中去
        for (int i = 0; i < len; i++) {
            
            //判断窗口中头部的元素的下标，从而知道该元素是否应该继续在本轮存在于窗口，或者根据窗口滑动应该被移除
            if (window.peek() < i - k + 1) {
                window.poll();
            }

            //新的元素nums[i]准备放入队列中，这时候要先和原本队列中的元素的下标
            Iterator<Integer> it = window.iterator();
            while(it.hasNext()) {
                //原先窗口中的元素只要比新来的元素小，就通通移除，反正没有这些较小元素的机会
                if (nums[it.next()] < nums[i]) {
                    it.remove();
                }
            }

            //每次循环将新元素的下标添加到窗口中
            if (i != 0) {
                window.offer(i);
            }            

            //i >= k - 1表示至少遍历了k个元素了（可以开始查找最大数了），这时候把最头部（左边）的老大加入到结果中
            if (i >= k - 1) {
                result[i - k + 1] = nums[window.peek()];
            }
        }
        return result;
    }
}
```

