# Divide and Conquer

## 241 Different Ways to Add Parentheses

### 原题概述

Given a string of numbers and operators, return all possible results from computing all the different possible ways to group numbers and operators. The valid operators are `+`, `-` and `*`.

**Example 1:**

```text
Input: "2-1-1"
Output: [0, 2]
Explanation: 
((2-1)-1) = 0 
(2-(1-1)) = 2
```

**Example 2:**

```text
Input: "2*3-4*5"
Output: [-34, -14, -10, -10, 10]
Explanation: 
(2*(3-(4*5))) = -34 
((2*3)-(4*5)) = -14 
((2*(3-4))*5) = -10 
(2*((3-4)*5)) = -10 
(((2*3)-4)*5) = 10
```

### 题意和分析

给一个String，是计算式，求出各种组合得出的不同结果，只有加减乘，返回结果list。解法是用递归，将operator为界建立左右子树；也就是说当遇到是operator时，将这个operator的左右分别递归，一直递归到两边只有一个数，然后进行计算，将左右子树的所有结果存入结果list中。

### 代码

```java
class Solution {
    public List<Integer> diffWaysToCompute(String input) {
        List<Integer> result = new ArrayList<>();
        if (input == null || input.length() == 0) {
            return result;
        }

        //遍历每一个字符
        for (int i = 0; i < input.length(); i++) {
            if (isOperator(input.charAt(i))) {
                char operator = input.charAt(i);
                List<Integer> left = diffWaysToCompute(input.substring(0, i));
                List<Integer> right = diffWaysToCompute(input.substring(i + 1));//不包括i位置本身

                for (int num1 : left) {//遍历左边的数
                    for (int num2 : right) {//遍历右边的数
                        result.add(calculate(num1, num2, operator));//处理两个数
                    }
                }
            }
        }

        //没有operator，input只是一个数的情况
        if (result.size() == 0) {
            result.add(Integer.valueOf(input));
        }
        return result;
    }

    private boolean isOperator(char c) {
        if (c == '+' || c == '-' || c == '*') {
            return true;
        }
        return false;
    }

    private int calculate(int num1, int num2, char operator) {
        int result = 0;
        switch(operator) {
            case '+':
                result = num1 + num2;
                break;
            case '-':
                result = num1 - num2;
                break;
            case '*':
                result = num1 * num2;
                break;
        }
        return result;
    }
}
```

## 218 The Skyline Problem

### 原题概述

A city's skyline is the outer contour of the silhouette formed by all the buildings in that city when viewed from a distance. Now suppose you are **given the locations and height of all the buildings** as shown on a cityscape photo \(Figure A\), write a program to **output the skyline** formed by these buildings collectively \(Figure B\).[![Buildings](https://leetcode.com/static/images/problemset/skyline1.jpg) ](https://leetcode.com/static/images/problemset/skyline1.jpg)[![Skyline Contour](https://leetcode.com/static/images/problemset/skyline2.jpg)](https://leetcode.com/static/images/problemset/skyline2.jpg)

The geometric information of each building is represented by a triplet of integers `[Li, Ri, Hi]`, where `Li` and `Ri` are the x coordinates of the left and right edge of the ith building, respectively, and `Hi` is its height. It is guaranteed that `0 ≤ Li, Ri ≤ INT_MAX`, `0 < Hi ≤ INT_MAX`, and `Ri - Li > 0`. You may assume all buildings are perfect rectangles grounded on an absolutely flat surface at height 0.

For instance, the dimensions of all buildings in Figure A are recorded as: `[ [2 9 10], [3 7 15], [5 12 12], [15 20 10], [19 24 8] ]`.

The output is a list of "**key points**" \(red dots in Figure B\) in the format of `[ [x1,y1], [x2, y2], [x3, y3], ... ]` that uniquely defines a skyline. **A key point is the left endpoint of a horizontal line segment**. Note that the last key point, where the rightmost building ends, is merely used to mark the termination of the skyline, and always has zero height. Also, the ground in between any two adjacent buildings should be considered part of the skyline contour.

For instance, the skyline in Figure B should be represented as:`[ [2 10], [3 15], [7 12], [12 0], [15 10], [20 8], [24, 0] ]`.

**Notes:**

* The number of buildings in any input list is guaranteed to be in the range `[0, 10000]`.
* The input list is already sorted in ascending order by the left x position `Li`.
* The output list must be sorted by the x position.
* There must be no consecutive horizontal lines of equal height in the output skyline. For instance, `[...[2 3], [4 5], [7 5], [11 5], [12 7]...]` is not acceptable; the three lines of height 5 should be merged into one in the final output as such: `[...[2 3], [4 5], [12 7], ...]`

### 题意和分析

这里有大牛的解释（[https://briangordon.github.io/2014/08/the-skyline-problem.html](https://briangordon.github.io/2014/08/the-skyline-problem.html)），使用了Sweep Line扫描线算法，该算法在meeting room的题目也会遇到，首先从左到右扫描就会知道每个点的位置，其次判断需要知道的点起始点-即为最高点，终止点即为第二高点。

### 代码

```java
class Solution {
    public List<int[]> getSkyline(int[][] buildings) {
        List<int[]> result = new ArrayList<>();
        List<int[]> heights = new ArrayList<>();
        for (int[] building : buildings) {
            heights.add(new int[]{building[0], -building[2]});
            heights.add(new int[]{building[1], building[2]});
        }

        Collections.sort(heights, (a, b) -> {
            if (a[0] !=b[0]) {
                return a[0] - b[0];
            }
            return a[1] - b[1];
        });

        Queue<Integer> pq = new PriorityQueue<>((a, b) -> (b - a));

        pq.offer(0);
        int prev = 0;
        for (int[] height : heights) {
            if (height[1] < 0) {
                pq.offer(-height[1]);
            } else {
                pq.remove(height[1]);
            }
            int current = pq.peek();
            if (prev != current) {
                result.add(new int[]{height[0], current});
                prev = current;
            }
        }
        return result;
    }
}
```

## 215 Kth Largest Element in an Array

### 原题概述

Find the **k**th largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Example 1:**

```text
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

**Example 2:**

```text
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 4
```

**Note:**   
You may assume k is always valid, 1 ≤ k ≤ array's length.

### 题意和分析

给一个未排序的数组，找出第k大的值，元素可以有重复。[这里](https://mp.weixin.qq.com/s?__biz=MjM5ODYxMDA5OQ==&mid=2651961587&idx=1&sn=54bf39db7043cc638315caf70f24d94b&chksm=bd2d0d2f8a5a84395246be4522d10fbfc1f744658047d5fb3fad8e9f3c3d76baab3a2ce84867&mpshare=1&scene=1&srcid=1013ubGhakvFDhaPBctqTtWl#rd)有总结的topk的办法，总共有四个逐渐优化的办法：

1）全局排序，将整个数组排序后取第k-1个数，O\(nlogn\)；

![](../.gitbook/assets/image%20%2815%29.png)

2）局部排序，只排最大的k个数，利用冒泡排序的办法（因为能知道kth的位置），冒泡一次，找出一个最大值，冒k次就找到第kth的最大值了，O\(n\*k\)；

![](../.gitbook/assets/image%20%2818%29.png)

3）利用heap，创建一个最小堆用来存储当前最大的k个元素

![](../.gitbook/assets/image%20%289%29.png)

接着，从第k+1个元素开始扫描，和堆顶（堆中最小的元素）比较，如果被扫描的元素大于堆顶，则替换堆顶的元素，并调整堆，以保证堆内的k个元素，总是当前最大的k个元素。

![](https://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOzzA7pbponFmibHaMYQ5Vkk9xBxbWpznlYmMsItZ7Vd3B4MA0TzALhulicsNWrQ1icjaacibL19XOmicVg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

直到，扫描完所有n-k个元素，最终堆中的k个元素，就是猥琐求的TopK。复杂度 O\(n\*lg\(k\)\)。

4）随机选择，利用快排中partition的思想，**这里以找到topk的所有数举例来说明**

首先看看快排的伪代码

```text
void quick_sort(int[]arr, int low, inthigh){

    if(low== high) return;

    int i = partition(arr, low, high);

    quick_sort(arr, low, i-1);

    quick_sort(arr, i+1, high);

}
```

其核心算法思想是，分治法。

**分治法**（Divide & Conquer），把一个大的问题，转化为若干个子问题（Divide），每个子问题“**都**”解决，大的问题便随之解决（Conquer）。这里的关键词是**“都”**。从伪代码里可以看到，快速排序递归时，先通过partition把数组分隔为两个部分，两个部分“都”要再次递归。

分治法有一个特例，叫减治法。

**减治法**（Reduce & Conquer），把一个大的问题，转化为若干个子问题（Reduce），这些子问题中“**只**”解决一个，大的问题便随之解决（Conquer）。这里的关键词是**“只”**。

 **二分查找binary\_search**，是一个典型的运用减治法思想的算法，其伪代码是：

```text
int BS(int[]arr, int low, inthigh, int target){

    if(low> high) return -1;

    mid= low +(high - low) / 2;

    if(arr[mid]== target) return mid;

    if(arr[mid]> target)

        return BS(arr, low, mid-1, target);

    else

        return BS(arr, mid+1, high, target);

}
```

 二分查找，一个大的问题，可以用一个mid元素，分成左半区，右半区两个子问题。而左右两个子问题，只需要解决其中一个，递归一次，就能够解决二分查找全局的问题。

通过分治法与减治法的描述，可以发现，分治法的复杂度一般来说是大于减治法的：

快速排序：O\(n\*lg\(n\)\)

二分查找：O\(lg\(n\)\)

回到**快速排序，它的**核心是：

`i = partition(arr, low, high);`

**这个partition是干嘛的呢？**

顾名思义，partition会把整体分为两个部分。

更具体的，会用数组arr中的一个元素（默认是第一个元素t=arr\[low\]）为划分依据，将数据arr\[low, high\]划分成左右两个子数组：

* 左半部分，都比t大
* 右半部分，都比t小
* 中间位置i是划分元素

![](https://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOzzA7pbponFmibHaMYQ5Vkk9ic4TjkicnCpTAVJJqq4WgbE6tQhl5XORGiaJnZj3UboKBpUhTq1kad5Ng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

以上述TopK的数组为例，先用第一个元素t=arr\[low\]为划分依据，扫描一遍数组，把数组分成了两个半区：

* 左半区比t大
* 右半区比t小
* 中间是t

partition返回的是t最终的位置i。

很容易知道，partition的时间复杂度是O\(n\)，也就是把整个数组扫一遍，比t大的放左边，比t小的放右边，最后t放在中间N\[i\]。

**partition和TopK问题有什么关系呢？**

TopK是希望求出arr\[1,n\]中最大的k个数，那如果找到了**第k大**的数，做一次partition，不就一次性找到最大的k个数了么？（ 本例即partition后左半区的k个数）

问题变成了arr\[1, n\]中找到第k大的数。

再回过头来看看**第一次**partition，划分之后：

i = partition\(arr, 1, n\);

* 如果i大于k，则说明arr\[i\]左边的元素都大于k，于是只递归arr\[1, i-1\]里第k大的元素即可；
* 如果i小于k，则说明说明第k大的元素在arr\[i\]的右边，于是只递归arr\[i+1, n\]里第k-i大的元素即可；

 这就是**随机选择**算法randomized\_select，属于上面说的**减治法**，伪代码如下：

```java
int RS(arr, low, high, k){

    if(low== high) return arr[low];

    i= partition(arr, low, high);

    temp= i-low; //数组前半部分元素个数

    if(temp>=k)

        return RS(arr, low, i-1, k); //求前半部分第k大

    else

        return RS(arr, i+1, high, k-i); //求后半部分第k-i大

}
```

![](https://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOzzA7pbponFmibHaMYQ5Vkk9kb1JqQdt0av8kK59VvicibDNOrbIXsZicaHAFhk8u1A7BQW1aYIr84pQg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这是一个典型的减治算法，递归内的两个分支，最终只会执行一个，它的时间复杂度是O\(n\)。

再次强调一下：

* **分治法**，大问题分解为小问题，小问题都要递归各个分支，例如：快速排序
* **减治法**，大问题分解为小问题，小问题只要递归一个分支，例如：二分查找，随机选择

通过随机选择（randomized\_select），就能找到arr\[1, n\]中第k大的数；

如果再进行一次partition，就能得到所有TopK的结果。

### 代码

利用冒泡排序，进行局部排序

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        for (int i = 0; i < k; i++) {//冒泡排序外循环控制排序的趟数，排好k个数
            for (int j = nums.length - 1; j > i; j--) {//冒泡排序内循环控制每次排序涉及的元素的个数
                if (nums[j] > nums[j - 1]) {//先放大的在前面，小的放后面去
                    int temp = nums[j];
                    nums[j] = nums[j - 1];
                    nums[j - 1] = temp;
                }
            }
        }
        return nums[k-1];//第k-1个元素是第k大的数
    }
}
```

利用Heap，只选择topK，不排序topK

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        //建立一个k个元素的最小堆，来存储
        PriorityQueue<Integer> heap = new PriorityQueue<Integer>(k);

        //先把前k个元素存入，作为初始值
        for (int i = 0; i < k; i++) {
            heap.add(nums[i]);
        }

        for (int i = k; i < nums.length; i++) {
            if (nums[i] > heap.peek()) {//如果新来的元素大于堆顶的元素
                heap.add(nums[i]);
                heap.poll();//移除并返回队列的头部
            }
        }
        return heap.peek();//返回队列头部
    }
}
```

快速选择

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        //传入的参数nums.length - k表示找到nums.length - k最小的
        return findK(nums, nums.length - k, 0, nums.length - 1);
    }

    private int findK(int[] nums, int k, int left, int right) {
        if (left >= right) {//递归的终止条件，表示已经递归找完整个数组
            return nums[left];
        }

        int i = partition(nums, left, right);
        if (i == k) {//恰好是第k大的数
            return nums[i];
        } else if (i < k) {//在选定的pivot右边
            return findK(nums, k, i + 1, right);
        } else {//左边
            return findK(nums, k, left, i - 1);
        }
    }

    //只partition一次
    private int partition(int[] nums, int start, int end) {
        int pivot = nums[start];//基准
        int slow = start;//开始
        int fast = start + 1;//从start位置后面一位元素准备开始swap

        while (fast <= end) {//到最后一个元素前
            if (nums[fast] < pivot) {//将小的元素挪到pivot前面
                slow++;//这里需要先让指向pivot索引后移一位
                swap (nums, slow, fast);
            }
            fast++;//可能的交换结束后，fast继续右移
        }
        swap (nums, start, slow);//最后需要交换一下初始位置元素和pivot索引的元素
        return slow;//pivot所处的位置
    }

    private void swap (int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

## 973 K Closest Points from Origin

### 原题

We have a list of `points` on the plane.  Find the `K` closest points to the origin `(0, 0)`.

\(Here, the distance between two points on a plane is the Euclidean distance.\)

You may return the answer in any order.  The answer is guaranteed to be unique \(except for the order that it is in.\)

**Example 1:**

```text
Input: points = [[1,3],[-2,2]], K = 1
Output: [[-2,2]]
Explanation: 
The distance between (1, 3) and the origin is sqrt(10).
The distance between (-2, 2) and the origin is sqrt(8).
Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
We only want the closest K = 1 points from the origin, so the answer is just [[-2,2]].
```

**Example 2:**

```text
Input: points = [[3,3],[5,-1],[-2,4]], K = 2
Output: [[3,3],[-2,4]]
(The answer [[-2,4],[3,3]] would also be accepted.)
```

**Note:**

1. `1 <= K <= points.length <= 10000`
2. `-10000 < points[i][0] < 10000`
3. `-10000 < points[i][1] < 10000`

### 分析

计算哪个点离原点最近，可以利用众所周知的距离公式来计算，但是大可不必用外面的那一层根号，因为仅仅内部平方和就可以用来判断了。

实则是可以转化成了topK问题，所以topK问题的解法都可以用，

1）全排序， time: **O\(n\) = N log\(N\)**，space: **O\(N\)**。

2）堆排序，用maxHeap，大顶堆堆顶元素最大，因为我们要找的是smallest one，我们需要不断判断堆顶的元素与新元素的大小关系。 先加入k个元素（计算后的）到maxheap中。 进行逻辑判断，堆顶和新元素，实施必要的替换，保证heap的size。 简单循环，取出heap中的值到array中，输出。 time: **O\(n\) = Nlog\(k\)**，space: **O\(k\)**。

3）BFPRT算法，快排的思想，time: **O\(n\) = N**，space: **O\(N\)**。

### 代码

全排序

```java
class Solution {
    public int[][] kClosest(int[][] points, int K) {
        int[][] ans=new int[K][2];
        Arrays.sort(points,(int[] o1,int[] o2)->(o1[0]*o1[0]+o1[1]*o1[1]-o2[0]*o2[0]-o2[1]*o2[1]));
        System.arraycopy(points,0,ans,0,K);
        return ans;
    }
}
```

堆排序

```java
class Solution {
    public int[][] kClosest(int[][] points, int K) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> (cal(b) - cal(a))); // 倒序排列
        // Queue<int[]> priorityQueue = new PriorityQueue<>(K, (o1, o2) -> o1[0] * o1[0] + o1[1] * o1[1] - (o2[0] * o2[0] + o2[1] * o2[1]));

        int[][] result = new int[K][2];
        for (int i = 0; i < K; i++) { // 先加入K个元素
            maxHeap.offer(points[i]);
        }
        for (int i = K; i < points.length; i++) {
            if (cal(maxHeap.peek()) > cal(points[i])) { // 堆顶最大距离要比新来的点大，移出
                maxHeap.poll();
                maxHeap.offer(points[i]);
            }
        }
        
        for (int i = 0; i < K; i++) {
            result[i] = maxHeap.poll();
        }
        return result;
    }
    
    private int cal(int[] a) { // 函数编程计算坐标距离
        return a[0] * a[0] + a[1] * a[1];
    }
}
```

快排

```java
class Solution {
     public int[][] kClosest(int[][] points, int K) {
        int start = 0;
        int end = points.length - 1;
        while (start < end) {
            // 计算欧几里得距离
            int index = patition(points, start, end);
            if (index == K) {
                break;
            } else if (index < K) {
                start = index + 1;
            } else {
                end = index - 1;
            }
        }

        return Arrays.copyOf(points, K);
    }

    private int patition(int[][] points, int start, int end) {
        int i = start;
        int j = end + 1;
        int mid = distance(points[i][0], points[i][1]);
        while (true) {
            while (distance(points[++i][0], points[i][1]) < mid && i < end);
            while (distance(points[--j][0], points[j][1]) > mid && j > start);
            if (i >= j) {
                break;
            }
            swap(points, i, j);
        }
        swap(points, start, j);
        return j;
    }

    private int distance(int a, int b) {
        return a * a + b * b;
    }

    private void swap(int[][] points, int a, int b) {
        int[] temp = points[a];
        points[a] = points[b];
        points[b] = temp;
    }
}
```

