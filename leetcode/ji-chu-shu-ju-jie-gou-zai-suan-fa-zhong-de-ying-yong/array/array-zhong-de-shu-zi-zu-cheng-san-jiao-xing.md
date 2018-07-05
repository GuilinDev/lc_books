# Array元素与几何图形

## 118 - Pascal's Triangle

### 原题概述

Given a non-negative integer _numRows_, generate the first _numRows_ of Pascal's triangle.

![](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)  
In Pascal's triangle, each number is the sum of the two numbers directly above it.

**Example:**

```text
Input: 5
Output:
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
```

### 题意

这道题输入是一个层数，要求输出帕斯卡三角的所有层，由一个二维数组来表示。思路很直接，每一层都用一个array来保存前一层的指针，当前行的数据由前一层来得到，每一层的每个元素由前一层的两个邻接元素相加；每一层的第一个和最后一个元素都是1。

Time： O\(1+2+3+...+n\)=O\(n^2\)；Space：一个二维数组来存最后的三角形，存前一层的指针和当前层只是一维数组，因此是O\(n^2\)。

### 代码

```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (numRows <= 0) {
            return result;
        }
        ArrayList<Integer> previousRow = new ArrayList<>();
        previousRow.add(1);//第一行只有一个数，先加上
        result.add(previousRow);
        
        for (int i = 1; i <= numRows - 1; i++) {//从第二行开始有“前一行”
            ArrayList<Integer> currentRow = new ArrayList<>();//新建一个array存当前行，当前行比前一行多两个元素
            currentRow.add(1);//当前行的第一个元素为1，接下来从当前行的第二个元素开始
            for (int j = 0; j < previousRow.size() - 1; j++) {//从上一行的第一个元素到最后一个元素都会参与计算
                currentRow.add(previousRow.get(j) + previousRow.get(j+1));//前一行相邻两个元素相加，比如当前行的第2个元素就是上一行第0个和第1个相加，下一道题可以优化这里
            }
            currentRow.add(1);//当前行最后一个元素是1
            result.add(currentRow);
            previousRow = currentRow;
        }
        return result;
    }
}
```

另外一种写法是在每一行生成时，在该行index 0的位置加入1，然后根据在2行以后根据上一行的元素推断出当前行的元素的值，更加清晰明了。

```java
class Solution {
    List<List<Integer>> generate(int numRows) {
        List<List<Integer>> result = new ArrayList<List<Integer>>();
        if (numRows <= 0) {
            return result;
        }

        ArrayList<Integer> currentRow = new ArrayList<>();

        for (int i = 0; i < numRows; i++) {
            currentRow.add(0, 1);
            for (int j = 1; j < currentRow.size() - 1; j++) {//“掐头去尾”，头尾两个数都是1，中间的数才需要计算
                currentRow.set(j, currentRow.get(j) + currentRow.get(j+1)); //在当前行从j位置开始更新
            }
            ArrayList<Integer> copyRow = new ArrayList<Integer>(currentRow);//这里直接add currentRow的话，当currentRow下一轮变化时会影响result里面的值
            result.add(copyRow);
        }
        return result;
    }
}
```

## 119 - Pascal's Triangle II

### 原题概述

Given a non-negative index _k_ where _k_ ≤ 33, return the _k_th index row of the Pascal's triangle.

Note that the row index starts from 0.

![](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)  
In Pascal's triangle, each number is the sum of the two numbers directly above it.

**Example:**

```text
Input: 3
Output: [1,3,3,1]
```

**Follow up:**

Could you optimize your algorithm to use only _O_\(_k_\) extra space?

### 题意和分析

跟上一道题相比，这道题只需要知道某一行的结果。上一道题我们需要知道当前行第i个元素的值的时候，是前一行的result\[i\] + result\[i+1\]，这是从前往后扫，这里可以看到i和i+1数据是往前看的（得知i+1，需要先知道i，再加上1），而如果我们通过前一行result\[i\] + result\[i-1\]来得到当前行第i个元素的值，那就是数据从后往前扫，result\[i\] 只在当前步用到，下一步就用不到了，可以被覆盖，因此只需要一行的空间。

 这个方法类似动态规划省空间，主要就是看我们需要的数据是之前的数据还是新的数据，从而来决定我们遍历的方向。这个考点适合于电面的时候。

时间复杂度还是O\(n^2\)，而空间这里是O\(k\)来存储结果，仍然不需要额外空间。

### 代码

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> result = new ArrayList<>();
        if (rowIndex < 0) {//rowIndex可以等于0
            return result;
        }
        
        result.add(1);//先初始化为一行的值
        for (int i = 1; i <= rowIndex; i++) {//注意rowIndex是从0开始数，到rowIndex结束；上一题的numRows是共多少行，从1开始数
            //这里loop到rowIndex之前不用创建一个array来保存当前的行了
            for (int j = result.size() - 2; j >= 0; j--) {//从前一行的倒数第二个开始计算，而前一行的倒数第一个数由倒数第二个数可以知道
                result.set(j+1, result.get(j) + result.get(j+1));//将j+1位置上(通过j得知)的元素替换成前一行j+1相对应的两个值相加
            }
            result.add(1);//加上当前行的最后一个元素
        }
        return result;
    }
}
```

同样也有另外一种写法，每一轮在index 0的位置添加一个1，然后在前一行的基础上得到当前行的值即可。

```java
class Solution {
    public List<Integer> getRow(int rowIndex) {
        List<Integer> result = new ArrayList<>();
        if (rowIndex < 0) {
            return result;
        }

        for (int i = 0; i <= rowIndex; i++) {
            result.add(0, 1);//每一轮在index 0的位置插入一个1
            for (int j = 1; j < result.size() - 1; j++) {//
                result.set(j, result.get(j) + result.get(j+1));//在j的位置从左往右更新，因为已经插入了1，所以是j和j+1，而不是j-1和j，左边更新的数可以得出右边的数，刚好
            }
        }
        return result;
    }
}
```

## 611 - Valid Triangle Number

### 原题概述

Given an array consists of non-negative integers, your task is to count the number of triplets chosen from the array that can make triangles if we take them as side lengths of a triangle.

**Example 1:**  


```text
Input: [2,2,3,4]
Output: 3
Explanation:
Valid combinations are: 
2,3,4 (using the first 2)
2,3,4 (using the second 2)
2,2,3
```

**Note:**  


1. The length of the given array won't exceed 1000.
2. The integers in the given array are in the range of \[0, 1000\].

### 题意和分析

找到Array中所有可以组成三角形的triplets，重复数字算不同的triplets（不用去重）。这道题跟[3sum指针解法](https://guilindev.gitbook.io/interview/leetcode/ji-chu-shu-ju-jie-gou-zai-suan-fa-zhong-de-ying-yong/array/ksum)类似，先排序，然后从右向左遍历，两个指针向右和向左移动，满足nums\[left\]+nums\[right\] &lt; nums\[i\]就计数，最后返回。

Time：Arrays.sort\(\)的O\(nlogn\) + 后面的循环O\(n^2\) = O\(n^2\)；Space：每次需要重新创建两个指针，O\(n\)。

### 代码

```java
class Solution {
    public int triangleNumber(int[] nums) {
        int result = 0;
        if (nums.length < 3) {
            return result;
        }
        Arrays.sort(nums);
        for (int i = nums.length - 1; i >= 2; i--) {//i>=2，左边至少得留两个数
            int left = 0, right = i - 1;
            while (left < right) {
                if (nums[left] + nums[right] > nums[i]) {
                    result += right - left;//因为排过序了，所以left到right中间的数字都可以和nums[i]组成三角形
                    right--;//从右向左，让数变小看是否继续符合条件
                } else {//两个数加起来小于或等于，所以应该增大
                    left++;
                }
            }
        }
        return result;
    }
}
```

## 84 - Largest Rectangle in Histogram

### 原题概述

Given _n_ non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.

![](https://leetcode.com/static/images/problemset/histogram.png)  
Above is a histogram where width of each bar is 1, given height = `[2,1,5,6,2,3]`.

![](https://leetcode.com/static/images/problemset/histogram_area.png)  
The largest rectangle is shown in the shaded area, which has area = `10` unit.

**Example:**

```text
Input: [2,1,5,6,2,3]
Output: 10
```

### 题意和分析

给一个直方图，用一系列非负数字表示，每个bar的宽度一样都为1，找到直方图中最大的矩形区域。暴力解法就是以每个点为起点计算该点的最大区域值，然后取所有值中最大的那个，O\(n^2\)。比较流行的优化解法是用一个Stack来保存元素的indices，遇到比栈中下标对应的值更大或等于的高度时，将这个更大或等于的高度对应的下标压入栈中；遇到比栈中下标对应的值更小的高度时，将之前所有比该值大的出栈，分别计算面积，与准备返回的area进行比较，取较大值。所以Stack中保存的是**单调递增的索引**。另外在最后时需要保持不上升，所以在最后压入一个0。

以题目中的\[2,1,5,6,2,3\]为例：

1. 刚开始的时候栈为空，压入第一个元素2对应的index = 0；然后第二个元素的下标为1，它的高度值是1，比前一个值小，所以这时候index = 1不入栈，并且index = 0出栈，栈变为空，计算此时面积area = 2；

![](../../../.gitbook/assets/image%20%282%29.png)

    2. 高度1，5，6依次递增，所以依次压入栈中，然后当index = 4的时候，高度值为2，小于之前index = 3时候的高度值6，所以此时index = 4不入栈，但此时把index = 4对应的高度值2记下来；index = 3出栈，计算此时面积area = 6；

![](../../../.gitbook/assets/image%20%2810%29.png)

    3. 此时栈顶元素为index = 2，index = 2对应的值5 &gt; index = 4对应的值2；所以index = 2出栈，计算此时的面积area = 2 x 5 = 10；

![](../../../.gitbook/assets/image%20%2814%29.png)

    4. 此时栈顶元素为index = 1，它对应的高度1 &lt; index = 4对应的高度2，所以可以压入index = 4，同样之后index = 5也是上升可以压入，到最后结束的时候index = 6的时候人工压入一个0，此时栈的有效下标元素为{1,4,5}；栈顶元素为index = 6对应高度为0，此时只要高度比0大都需要出栈，所以index = 5先出栈，计算此时的面积area = 3，上面area最大值为10，所以不取；

![](../../../.gitbook/assets/image%20%285%29.png)

    5. 此时栈顶元素index = 4，高度值2 &gt; index = 6对应的高度值0，继续出栈，计算面积area = 2 x 2 = 4，不取；

![](../../../.gitbook/assets/image%20%287%29.png)

    6. 此时栈顶元素为index = 1，对应的值1 &gt; index = 6对应的高度值0，继续出栈，此时index = 1出栈后栈变为空，并且当前元素已经到末尾了，index = 6，所以index = 1对应的高度值是最小的，这时候把所有的栈的数目与index = 1的高度相乘，得到area = 6；

![](../../../.gitbook/assets/image%20%283%29.png)



所以可以看出，Stack中总是保持递增的序列，遇到较小的元素的时候，依次出栈并计算Stack中bar能围成的面积，直到Stack中的元素小于当前的元素。

Time：O\(n\)；Space：新建一个Stack，O\(n\)；

为什么这个方法是正确的呢？[参考这里](http://www.cnblogs.com/lichen782/p/leetcode_Largest_Rectangle_in_Histogram.html)。

![](../../../.gitbook/assets/image%20%284%29.png)

> 例如我们遇到最后遇到一个递减的bar（红色）。高度位于红线上方的（也就是算法中栈里面大于最右bar的）元素，他们是不可能和最右边的较小高度bar围成一个比大于在弹栈过程中的矩形面积了（黄色面积），因为红色的bar对他们来说是一个短板，和红色bar能围成的最大面积也就是红色的高度乘以这些“上流社会”所跨越的索引范围。但是“上流社会”的高度个个都比红色bar大，他们完全只计算彼此之间围成的面积就远远大于和红色bar围成的任意面积了。所以红色bar是不可能参与“上流社会”的bar的围城的（好悲哀）。
>
> 但是屌丝也不用泄气哦。因为虽然长度不占优势，但是团结的力量是无穷的。它还可以参与“比较远的”比它还要屌丝的bar的围城。他们的面积是有可能超过上流社会的面积的，因为距离啊！所以弹栈到比红色bar小就停止了。
>
> 另外一个细节需要注意的是，弹栈过程中面积的计算。
>
> **h\[t\] \* \(stack.isEmpty\(\) ? i : i - stack.peek\(\) - 1\)**
>
> **h\[t\]**是刚刚弹出的栈顶端元素。此时的面积计算是h\[t\]和前面的“上流社会”能围成的最大面积。这时候要注意哦，栈内索引指向的元素都是比h\[t\]小的，如果h\[t\]是目前最小的，那么栈内就是空哦。而在目前栈顶元素和h\[t\]之间（不包括h\[t\]和栈顶元素），都是大于他们两者的。如下图所示：
>
> ![](https://images0.cnblogs.com/blog/466943/201307/18095649-645e12c5653440f2a9e2ca7b505a3082.png)
>
> 那h\[t\]无疑就是Stack.Peek和t之间那些上流社会的短板啦，而它们的跨越就是i - Stack.Peek - 1。
>
> 所以说，这个弹栈的过程也是维持程序不变量的方法啊：**栈内元素一定是要比当前i指向的元素小的。**
>
> >

![](../../../.gitbook/assets/image%20%281%29.png)

### 代码



## 85 - Maximum Rectangle

### 原题概述

Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.

**Example:**

```text
Input:
[
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6
```

### 题意和分析

### 代码



