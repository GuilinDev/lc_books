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



