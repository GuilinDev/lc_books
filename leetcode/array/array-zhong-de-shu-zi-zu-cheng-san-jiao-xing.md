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

尾递归

```java
class Solution {
    public List<List<Integer>> generate(int numRows) {
        return generateHelper(new ArrayList<List<Integer>>(), numRows);
    }
    private List<List<Integer>> generateHelper(List<List<Integer>> triangle, int numRows) {
        if (numRows == 0) { // 基线条件，需要创建的三角形行数
            return triangle;
        }
        
        List<Integer> newList = new ArrayList<>(); // 新的一行
        newList.add(1); // 每一行的第一个位置是1
        if (triangle.size() != 0) { // 第一行加入的时候不用计算中间的值
            List<Integer> lastList = triangle.get(triangle.size() - 1);
            for (int i = 0; i < lastList.size() - 1; i++) {//没有包括最后一列
                int current = lastList.get(i);
                int next = lastList.get(i + 1);
                newList.add(current + next); // 计算三角形的值
            }
            newList.add(1); //最后一列
        }
        triangle.add(newList); // 加入当前一行
        
        return generateHelper(triangle, numRows - 1);
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

给一个直方图，用一系列非负数字表示，每个bar的宽度一样都为1，找到直方图中最大的矩形区域。

1\) 暴力解法

就是以每个点为起点，向两边扩散，计算以当前高度为矩形的最大区域值，为此，需要：

1. 左边看一下，看最多能向左延伸多长，找到大于等于当前柱形高度的最左边元素的下标；
2. 右边看一下，看最多能向右延伸多长；找到大于等于当前柱形高度的最右边元素的下标。

对于每一个位置，都这样操作，得到一个矩形面积，然后取所有值中最大的那个，时间O\(n^2\)，空间O\(1\)。

![](../../.gitbook/assets/image%20%2884%29.png)

2）优化解法

时间换空间，暴力解法是每个柱子都两边扩散去找大于等于自己的柱子，有没有可能把柱子高度的信息先存储下来，直接用O\(1\)的方式来找呢？先思考一个问题：

> 对于i左边的两根柱子 j0以及 j1，如果索引 j0 在 j1右侧，并且 heights\[j0\]≥heights\[j1\]，那么对于任意的在j0和j1之后出现的柱子 i \(j1 &lt; i\)，j0一定不会是位于 i 左侧的最近的小于其高度的柱子。

以题目中的\[2,1,5,6,2,3\]为例：

 1、一开始看到的柱形高度为 `2` ，这个时候以这个 `2` 为高度的最大面积的矩形还不能确定，我们需要继续向右遍历，如下图

![](../../.gitbook/assets/image%20%2887%29.png)

2、然后看到到高度为 1 的柱形，这个时候以这个柱形为高度的矩形的最大面积还是不知道的。但是它之前的以 2 为高度的最大面积的矩形是可以确定的，这是因为这个 1 比 2 小 ，因为这个 1 卡在了这里 2 不能再向右边扩展了，如下图。

![](../../.gitbook/assets/image%20%2878%29.png)

计算一下以 2 为高度的最大矩形的面积是 2。如果已经确定了一个柱形的高度，接下来的计算这个矩形可以不管了，将它以虚框表示，如下图。

![](../../.gitbook/assets/image%20%2883%29.png)

3、遍历到高度为 5 的柱形，同样的以当前看到柱形为高度的矩形的最大面积也是不知道的，因为还要看右边高度的情况。那么它的左右有没有可以确定的柱形呢？没有，这是因为 5 比 1 大，我们看后面马上就出现了 6，不管是 1 这个柱形还是 5 这个柱形，都还可以向右边扩展。

![](../../.gitbook/assets/image%20%2881%29.png)

 4、接下来，遍历到高度为 `6` 的柱形，同样的，以柱形 `1`、`5`、`6` 为高度的最大矩形面积还是不能确定下来；

![](../../.gitbook/assets/image%20%2877%29.png)

 5、再接下来，遍历到高度为 `2` 的柱形。

![](../../.gitbook/assets/image%20%2872%29.png)

发现了一件很神奇的事情，高度为 6 的柱形对应的最大矩形的面积的宽度可以确定下来，它就是夹在高度为 5 的柱形和高度为 2 的柱形之间的距离，它的高度是 6，宽度是 1。

![](../../.gitbook/assets/image%20%2873%29.png)

将可以确定的柱形设置为虚线

![](../../.gitbook/assets/image%20%2890%29.png)

 接下来柱形 `5` 对应的最大面积的矩形的宽度也可以确定下来，它是夹在高度为 `1` 和高度为 `2` 的两个柱形之间的距离；

![](../../.gitbook/assets/image%20%2888%29.png)

标成虚线

![](../../.gitbook/assets/image%20%2889%29.png)

> 我们发现了，只要是遇到了当前柱形的高度比它上一个柱形的高度严格小的时候，一定可以确定它之前的**某些**柱形的最大宽度，并且确定的柱形宽度的顺序是从右边向左边。 这个现象告诉我们，在遍历的时候需要记录的信息就是遍历到的柱形的下标，它一左一右的两个柱形的下标的差就是这个面积最大的矩形对应的最大宽度。

这个时候，还需要考虑的一个细节是，在确定一个柱形的面积的时候，除了右边要比当前严格小，其实还蕴含了一个条件，那就是左边也要比当前高度严格小。

那如果是左边的高度和自己相等怎么办呢？之前是只要比当前严格小，才可以确定一些柱形的最大宽度。只要是大于或者等于之前看到的那一个柱形的高度的时候，其实都不能确定。因此确定当前柱形对应的宽度的左边界的时候，往回头看的时候，一定要找到第一个**严格小于**我们要确定的那个柱形的高度的下标。这个时候中间那些相等的柱形其实就可以当做不存在一样。因为它对应的最大矩形和它对应的最大矩形其实是一样的。

我们在遍历的时候，需要记录的是下标，如果当前的高度比它之前的高度严格小于的时候，就可以直接确定之前的那个高的柱形的最大矩形的面积，而为了确定这个最大矩形的左边界，还要找到第一个严格小于它的高度的矩形，向左回退的时候，其实就可以当中间这些柱形不存在。

这是因为我们就是想确定 6 的宽度，6 的宽度确定完了，其实我们就不需要它了，这个 5 的高度和这个 5 的高度确定完了，也不需要它了。**在缓存数据的时候，是从左向右缓存的，我们计算出一个矩形的面积的顺序则是从右向左的，并且计算完成以后就不再需要了，符合后进先出的特点。**因此，我们需要的这个作为缓存的数据结构就是栈。当确定了一个柱形的高度的时候，就将它从栈顶移出，所有的柱形在栈中进栈一次，出栈一次，一开始栈为空，最后也一定要让栈为空，表示这个高度数组里所有的元素都考虑完了。

6、最后遍历到最后一个柱形，即高度为 3 的柱形。

![](../../.gitbook/assets/image%20%2880%29.png)

（一次遍历完成以后。接下来考虑栈里的元素全部出栈。）

接下来就要依次考虑还在栈里的柱形的高度。和刚才的方法一样，只不过这个时候右边没有比它高度还小的柱形了，这个时候计算宽度应该假设最右边还有一个下标为 len （这里index等于 6） 的高度为 0 （或者 0.5，只要比 1 小）的柱形，作为哨兵柱子。

![](../../.gitbook/assets/image%20%2879%29.png)

![](../../.gitbook/assets/image%20%2876%29.png)

7、下标为 5 ，即高度为 3 的柱形，左边的下标是 4 ，右边的下标是 6 ，因此宽度是 6 - 4 - 1 = 1（两边都不算，只算中间的距离，所以减 1）；算完以后，将它标为虚线。

![](../../.gitbook/assets/image%20%2871%29.png)

8、下标为 4 ，高度为 2 的柱形，左边的下标是 1 ，右边的下标是 6 ，因此宽度是 6 - 1 - 1 = 4；算完以后，将它标为虚线。

![](../../.gitbook/assets/image%20%2891%29.png)

9、最后看下标为 1，高度为 1 的矩形，它的左边和右边其实都没有元素了，它就是整个柱形数组里高度最低的柱形，没有谁能挡住它，所以计算它的宽度，就是整个柱形数组的长度。

![](../../.gitbook/assets/image%20%2874%29.png)

![](../../.gitbook/assets/image%20%2875%29.png)

到此为止，所有的柱形高度对应的最大矩形的面积就都计算出来了。

    

![](../../.gitbook/assets/image%20%2886%29.png)



Time：O\(n\)；Space：新建一个Stack，O\(n\)；

为什么这个方法是正确的呢？[参考这里](http://www.cnblogs.com/lichen782/p/leetcode_Largest_Rectangle_in_Histogram.html)。

![](../../.gitbook/assets/image%20%2821%29.png)

> 例如我们遇到最后遇到一个递减的bar（红色）。高度位于红线上方的（也就是算法中栈里面大于最右bar的）元素，他们是不可能和最右边的较小高度bar围成一个比大于在弹栈过程中的矩形面积了（黄色面积），因为红色的bar对他们来说是一个短板，和红色bar能围成的最大面积也就是红色的高度乘以这些“上流社会”所跨越的索引范围。但是“上流社会”的高度个个都比红色bar大，他们完全只计算彼此之间围成的面积就远远大于和红色bar围成的任意面积了。所以红色bar是不可能参与“上流社会”的bar的围城的（好悲哀）。
>
> 但是屌丝也不用泄气哦。因为虽然长度不占优势，但是团结的力量是无穷的。它还可以参与“比较远的”比它还要屌丝的bar的围城。他们的面积是有可能超过上流社会的面积的，因为距离啊！所以弹栈到比红色bar小就停止了。
>
> 另外一个细节需要注意的是，弹栈过程中面积的计算。
>
> **h\[t\] \* \(stack.isEmpty\(\) ? i : i - stack.peek\(\) - 1\)**
>
> **h\[t\]**是刚刚弹出的栈顶端元素。此时的面积计算是h\[t\]和前面的“上流社会”能围成的最大面积。这时候要注意，栈内索引指向的元素都是比h\[t\]小的，如果h\[t\]是目前最小的，那么栈内就是空！而在目前栈顶元素和h\[t\]之间（不包括h\[t\]和栈顶元素），都是大于他们两者的。如下图所示：
>
> ![](https://images0.cnblogs.com/blog/466943/201307/18095649-645e12c5653440f2a9e2ca7b505a3082.png)
>
> 那h\[t\]无疑就是Stack.Peek和t之间那些上流社会的短板啦，而它们的跨越就是i - Stack.Peek - 1。
>
> 所以说，这个弹栈的过程也是维持程序不变量的方法啊：**栈内元素一定是要比当前i指向的元素小的。**
>
> >

![](../../.gitbook/assets/image%20%2810%29.png)

### 代码

暴力解法

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }
        int len = heights.length;
        int result = 0;
        
        for (int i = 0; i < len; i++) {
            int curHeight = heights[i];
            
            // 找左边最后 1 个大于等于 heights[i] 的索引
            int left = i;
            while (left > 0 && heights[left - 1] >= curHeight) {
                left--;
            }
            
            // 找右边最后 1 个大于等于 heights[i] 的索引
            int right = i;
            while (right < len - 1 && heights[right + 1] >= curHeight) {
                right++;
            }
            
            result = Math.max(result, (right - left + 1) * curHeight);
        }
        return result;
    }
}
```

Stack + 哨兵解法

```java
class Solution {
    public int largestRectangleArea(int[] heights) {
        if (heights == null || heights.length == 0) {
            return 0;
        }
        int len = heights.length;
        Stack<Integer> stack = new Stack<>();
        
        int result = 0;
        
        for (int i = 0; i <= len; i++) { // 末尾会多一个哨兵节点
            int height = (i == len) ? 0 : heights[i];
            
            if (stack.isEmpty() || height >= heights[stack.peek()]) { // 单调递增，入栈
                stack.push(i);
            } else { // 当前柱子比前面的柱子矮了
                int preIndex = stack.pop();
                int currArea = 0;
                if (stack.isEmpty()) { // pop出哨兵节点了，计算最矮的柱子乘以所有柱子数目的面积
                    currArea = heights[preIndex] * i;
                } else { // 正常计算
                    currArea = heights[preIndex] * (i - 1 - stack.peek());
                }
                
                result = Math.max(result, currArea);
                i--; // 继续退回前面高的柱子，计算面积
            }
        }
        return result;
    }
}
```

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

The DP solution proceeds row by row, starting from the first row. Let the maximal rectangle area at row i and column j be computed by \[right\(i,j\) - left\(i,j\)\]\*height\(i,j\).

All the 3 variables left, right, and height can be determined by the information from previous row, and also information from the current row. So it can be regarded as a DP solution. The transition equations are:

> left\(i,j\) = max\(left\(i-1,j\), cur\_left\), cur\_left can be determined from the current row

> right\(i,j\) = min\(right\(i-1,j\), cur\_right\), cur\_right can be determined from the current row

> height\(i,j\) = height\(i-1,j\) + 1, if matrix\[i\]\[j\]=='1';

> height\(i,j\) = 0, if matrix\[i\]\[j\]=='0'

### 代码

```java
class Solution {
    public int maximalRectangle(char[][] matrix) {
        if (matrix == null || matrix.length == 0 || matrix[0] == null || matrix[0].length == 0) return 0;
        int m = matrix.length, n = matrix[0].length, maxArea = 0;
        int[] left = new int[n];
        int[] right = new int[n];
        int[] height = new int[n];
        Arrays.fill(right, n - 1);
        for (int i = 0; i < m; i++) {
            int rB = n - 1;
            for (int j = n - 1; j >= 0; j--) {
                if (matrix[i][j] == '1') {
                    right[j] = Math.min(right[j], rB);
                } else {
                    right[j] = n - 1;
                    rB = j - 1;
                }
            }
            int lB = 0;
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == '1') {
                    left[j] = Math.max(left[j], lB);
                    height[j]++;
                    maxArea = Math.max(maxArea, height[j] * (right[j] - left[j] + 1));
                } else {
                    height[j] = 0;
                    left[j] = 0;
                    lB = j + 1;
                }
            }
        }
        return maxArea;
    }
}
```

