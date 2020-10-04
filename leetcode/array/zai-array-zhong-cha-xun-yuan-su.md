---
description: 搜索过程通常与Binary Search有关
---

# 在Array中搜索元素

## 153 - Find Minimum in Rotated Sorted Array

### 原题概述

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

\(i.e.,  `[0,1,2,4,5,6,7]` might become  `[4,5,6,7,0,1,2]`\).

Find the minimum element.

You may assume no duplicate exists in the array.

**Example 1:**

```text
Input: [3,4,5,1,2] 
Output: 1
```

**Example 2:**

```text
Input: [4,5,6,7,0,1,2]
Output: 0
```

### 题意与分析

一个已经升序排好的数组以某个轴rotated调转左右的元素，然后在调转后的array里寻找到最小的元素，数组里的元素是没有重复的。这道题凭空想的话不容易想到，拿原题中的例子  \[0,1,2,4,5,6,7\]转变成\[4,5,6,7,0,1,2\]来观察规律，如果最小值如果没有rotated，为nums\[0\]，如果rotated了，满足nums\[min\]&lt;nums\[min-1\]，也就是说最小值比它之前的数值下（index大）；用Binary Search的找到中间的值nums\[mid\]，如果找到的值小于它左边的值，则该值就是最小值 - 因为原本是升序，只有在最小值处它才会小于左边的值，直接返回；如果nums\[mid\]大于左边的值，那么nums\[mid\]有可能是\[4,5,6,7,0,1,2\]或\[6,7,0,1,2,4,5\]这样的情况，1）nums\[mid\]大于左边的值也大于右边的值，说明最小值在该nums\[mid\]右边；2）nums\[mid\]大于左边的值但小于右边的值，说明最小值在该nums\[mid\]的左边。以此类推，直到找到一个nums\[mid\]小于它左边的值为止。

因为数组的元素没有重复，所以跟下一道相比是简单了很多。

Time： O\(logn\)； Space：O\(1\).

### 代码

```java
class Solution {
    public int findMin(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }
        int left = 0, right = nums.length - 1;//Binary Search
        while (left < right) {
            int mid = left + (right - left)/2;
            if (mid > 0 && nums[mid] < nums[mid - 1]) {//mid>0为了不溢出,注意Array只有两个元素的情况
                return nums[mid]; //Binary Search的终止条件，找到了
            }
            if (nums[mid] >= nums[left] && nums[mid] > nums[right]) {//在最后只剩两个元素的时候，向下取整mid等于left，所以这里数组虽然没有重复值，但是需要>=来确定“nums[mid]比它左右两边都‘大’”
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
​
        //如果到这里了还没有找到，那最小值一定就是剩下的那个left到达大于right的值
        return nums[left];
    }
}
```

另外一种写法，优化下代码，复杂度一样

```java
class Solution {
    public int findMin(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }
        int left = 0, right = nums.length - 1;
        if (nums[left] < nums[right]) {
            return nums[left];
        }
        while(right - left > 1) {//两个指针差<1的时候终止
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[left]) {
                left = mid;
            } else {
                right = mid;
            }
        }
        return nums[right];
    }
}
```

## 154 - Find Minimum in Rotated Sorted Array II

### 原题概述

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

\(i.e.,  `[0,1,2,4,5,6,7]` might become  `[4,5,6,7,0,1,2]`\).

Find the minimum element.

The array may contain duplicates.

**Example 1:**

```text
Input: [1,3,5]
Output: 1
```

**Example 2:**

```text
Input: [2,2,2,0,1]
Output: 0
```

**Note:**

* This is a follow up problem to [Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/).
* Would allow duplicates affect the run-time complexity? How and why?

### 题意与分析

这道题跟上一道题基本一样，区别就是数组本身里面可以有重复元素了。依然用Binary Search，只比较右边的指针即可， 例如原本的数组是\[0,1,2,2,2,3\]，如果rotate后的数组是\[2,2,3,0,1,2\]，nums\[mid\]大于nums\[right\]，可以确定最小的数在右边；如果是rotate后的数组是\[3,0,1,2,2,2\]，nums\[mid\]小于nums\[right\]，确定最小数在左边或就是nums\[mid\]自己；如果二者相等，无法确定最小数的位置，这时候可以把右标-1，缩小范围。最后判断条件结束left&gt;=right的时候，这个时候条件停止，left总是指向最后一个数也就是最小数。

Time：这个不是常规的Binary Search，如果一直中间数等于右指针的数，那么最坏情况是O\(n\)； Space：O\(1\)。

### 代码

```java
class Solution {
    public int findMin(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        if (nums.length == 1) {
            return nums[0];
        }

        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left)/2;
            if (nums[mid] > nums[right]) {//中间数大于右指针的数，说明最小数在右边
                left = mid + 1;
            } else if (nums[mid] < nums[right]) {//中间数小于右边的数，最小数在左边，注意这里中间数有可能是最小数，所以不要mid-1
                right = mid;
            } else {//如果中间数等于右边的数，则无法知道最小数在哪边，但这时候可以把右标往左移进行比较
                right--;
            }
        }
        return nums[left];
    }
}
```

## \* 35 - Search Insertion Position

### 原题概述

Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

**Example 1:**

```text
Input: [1,3,5,6], 5
Output: 2
```

**Example 2:**

```text
Input: [1,3,5,6], 2
Output: 1
```

**Example 3:**

```text
Input: [1,3,5,6], 7
Output: 4
```

**Example 4:**

```text
Input: [1,3,5,6], 0
Output: 0
```

### 题意和分析

给一个升序数组，和一个目标值，寻找该目标值是否在数组里面，返回数组出现的index；如果没有，那么返回该target值升序应该出现的index。

1\) 第一种方法是常规的Binary Search的方法，没什么好说的，Time：O\(logn\)， Space\(1\)；

2\) 第二种办法从头往后找（当然从后往前找也是一样），因为是升序排好的数组，遇到相等的值返回当前index，如果大于了还没找到那也返回当前的index，后面当然是找不到了；Time：O\(n\)， Space\(1\)；

### 代码

常规的Binary Search

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else if (nums[mid] > target) {
                right = mid - 1;
            } else {
                return mid;
            }
        }
        return left;//如果没有找到，则最后left超过right值的地方是target应该处的位置，因为是“超过”，在当前的后一位
    }
}
```

递归

```java
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        return self.searchInsertHelper(nums, target, 0, len(nums) - 1)
    def searchInsertHelper(self, nums: List[int], target: int, left: int, right: int) -> int:
        if left > right:
            return left
        mid = left + (right - left) // 2
        if target == nums[mid]:
            return mid
        elif target < nums[mid]:
            return self.searchInsertHelper(nums, target, left, mid - 1)
        else:
            return self.searchInsertHelper(nums, target, mid + 1, right)
```

顺序找一遍

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] >= target) {
                return i;
            }
        }
        return nums.length;//target比所有数都大，放在最后一位
    }
}
```

## 33 - Search in Rotated Sorted Array

### 原题概述

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

\(i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`\).

You are given a target value to search. If found in the array return its index, otherwise return `-1`.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of _O_\(log _n_\).

**Example 1:**

```text
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

**Example 2:**

```text
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```

### 题意与分析

一个已经升序排好的数组以某个轴rotated调转左右的元素，然后在调转后的array里寻找到指定的元素，找到就返回index，没找到则返回-1，数组里的元素是没有重复的。

这道题是上一道题35 - Search Insert Position的变体，看似有点麻烦，其实理清一下还是比较简单的。 因为rotate的缘故，当切取一半的时候要做进一步的判断。具体来说，假设数组是nums，每次左边缘为left，右边缘为right，还有中间位置是 mid。在每次迭代中，分三种情况： 

（1）如果nums\[mid\] == target，那么mid就是我们要的结果，直接返回； 

（2）如果nums\[mid\] &lt; nums\[right\]，那么说明从mid到right一定是有序的（没有受到rotate的影响），那么这时候判断target是不是在mid到right之间，如果是则把左边缘移到mid+1，如果不是就右边缘移到mid-1。 

（3）如果nums\[mid\] &gt;= nums\[right\]，那么说明mid到right收到了rotate的影响，而从left到mid一定是有序的，这时候同样判断target在哪个范围，然后相应的移动边缘即可。

所以先根据nums\[mid\]的值跟nums\[left\]或者nums\[right\]比较，可以先知道mid左右两边哪边是有序的，然后再先判断target是否在有序的部分，so on and so forth，每次都可以去掉一半的数据，Time是O\(logn\)，Space是O\(1\)。

### 代码

```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null | nums.length == 0) {
            return -1;
        }
        int left = 0;
        int right = nums.length - 1;
        
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] > nums[right]) { // 这里和nums[right]比较，左半边有序，如题目中例子
                // 先检查target是否在左半边有序的部分
                if (target >= nums[left] && target < nums[mid]) { // 需要两个条件确定（仅是target < nums[mid]可能在右边乱序部分）
                    right = mid - 1;                  
                } else { // 继续去右边乱序部分找
                    left = mid + 1;
                }
                
            } else { // 右半边有序
                // 先检查target是否在右半边有序的部分
                if (target > nums[mid] && target <= nums[right]) { // 同样两个条件
                    left = mid + 1;
                } else { // 继续去左边乱序部分找
                    right = mid - 1;
                }
            }
        }
        return -1;
    }
}
   
```

## 81 - Search in Rotated Sorted Array II

### 原题概述

Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

\(i.e., `[0,0,1,2,2,5,6]` might become `[2,5,6,0,0,1,2]`\).

You are given a target value to search. If found in the array return `true`, otherwise return `false`.

**Example 1:**

```text
Input: nums = [2,5,6,0,0,1,2], target = 0
Output: true
```

**Example 2:**

```text
Input: nums = [2,5,6,0,0,1,2], target = 3
Output: false
```

**Follow up:**

* This is a follow up problem to [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/description/), where `nums` may contain duplicates.
* Would this affect the run-time complexity? How and why?

### 题意与分析

和上一道题33 - Search in Rotated Sorted Array区别是这道题目中元素会有重复的情况出现，找到了就返回true，没找到返回false。题目中问到了是否影响复杂度，因为重复元素这个条件的出现，出现了比较复杂的cases，影响了算法的时间复杂度。前一道题是依靠中间和边缘元素的大小关系，来判断哪一半是不受rotate影响仍然有序的；而现在因为重复元素的出现，如果我们遇到中间和边缘相等的情况，我们就无法判断哪边有序，因为两边都有可能是有序的结果。假设原数组是{1,2,3,3,3,3,3}，那么旋转之后有可能是{3,3,3,3,3,1,2}，或者{3,1,2,3,3,3,3}，这样的我们判断左边缘和中心的时候都是3，如果我们要寻找1或者2，我们并不知道应该跳向哪一半。

解决的办法只能是对边缘移动一步（跟154 - Find Minimum in Rotated Array II，当中间等于右边的数时，无法判断最小数在左边还是右边，只能移动一步的情况，有异曲同工的地方），直到边缘和中间不再相等或者相遇，这就导致了会有不能切去一半的可能。所以最坏情况（比如全部都是一个元素，或者只有一个元素不同于其他元素，而它就在最后一个）就会出现每次移动一步，总共是n步，算法的时间复杂度变成O\(n\)。

### 代码

```java
class Solution {
    public boolean search(int[] nums, int target) {
        if (nums == null || nums.length == 0) {
            return false;
        }

        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return true;
            }
            if (nums[mid] > nums[left]) {//现在无法判断target在哪一边
                if (target < nums[mid] && target >= nums[left]) {//target在左半边，注意这里有重复的数，应该是>=
                    right = mid - 1;
                } else {//target在右半边
                    left = mid + 1;
                }
            } else if (nums[mid] < nums[left]) {//现在无法判断target在哪一边
                if (target > nums[mid] && target <= nums[right]) {//target在右半边，注意这里有重复的数，应该是>=
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            } else {//中间数和左边数（前面两个条件和右边数比也是一样的）相等的情况下，只能挪一步
                left++;
            }
        }
        return false;
    }
}
```

不过既然时间复杂度反正已经是O\(n\)了，那我们也可以不用Binary Search，直接从左到右扫一遍了事，当然面试中应该先分析上面的情况再说这个办法，如果没有交流直接就这样写的话，估计下一道题面试官直接难题伺候了。

```java
class Solution {
    public boolean search(int[] nums, int target) {
        for(int i=0;i<nums.length;i++){
            if(nums[i]==target){
                return true;
            }
        }
        return false;
    }
}
```

## 34 - Find First and Last Position of Element in Sorted Array \(Search for a Range\)

### 原题概述

Given an array of integers `nums` sorted in ascending order, find the starting and ending position of a given `target` value.

Your algorithm's runtime complexity must be in the order of _O_\(log _n_\).

If the target is not found in the array, return `[-1, -1]`.

**Example 1:**

```text
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

**Example 2:**

```text
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

### 题意和分析

已经升序排好的数组里面可能有重复的数字，找到给定的target值的起始下标和结束下标。没有就返回\[-1， -1\]，如果只有一个则返回两个同样的下标。要求复杂度为O\(logn\)。

1）暴力法直接遍历：直接用for循环从左到右遍历，时间复杂度为O\(n\)，所以会超时。先找记录第一次nums\[i\] = target，result\[0\] = i，之后继续遍历，直至nums\[i\]  !=  target，result\[1\] = i。 

2）二分查找法：做两次二分查找分别找左边界和右边界，分别特殊处理当nums\[mid\] == target的情况；对于查找左边界的二分查找，当nums\[mid\]== target的时候，移动右下标right为mid - 1；对于查找右边界的二分查找，当nums\[mid\] == target的时候，移动做下标left为mid + 1；注意在两个二分查找过程中，每次nums\[mid\] == target时都需要更新result，

两个二分查找，Time：2 × O\(logn\) = O\(logn\)，Space：O\(1\)。 

### 代码

```java
public int[] searchRange(int[] nums, int target) {
        int[] result = {-1, -1};
        if (nums == null || nums.length == 0) {
            return result;
        }
        int left = 0;
        int right = nums.length - 1;
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
             if (nums[mid] >= target) { // 找左边界，找到了一个target，还需要继续往左边找
                right = mid;
            } else {
                left = mid;
            }
        }
        // 这里left和right都可能是target，所以先判断右边界再判断左边界
        if (nums[right] == target) {
            result[0] = right;
        }
        if (nums[left] == target) {
            result[0] = left;
        } 
        
        
        left = 0;
        right = nums.length - 1;
        
        while (left + 1 < right) {
            int mid = left + (right - left) / 2;
             if (nums[mid] <= target) { // 找有边界，找到了一个target，还需要继续往右边找
                left = mid;
            } else {
                right = mid;
            }
        }
        
        // 这里left和right都可能是target，所以先判断左边界再判断右边界
        if (nums[left] == target) {
            result[1] = left;
        }
        if (nums[right] == target) {
            result[1] = right;
        }
        
        return result;
    }
```



## 74 - Search a 2D Matrix

### 原题概述

Write an efficient algorithm that searches for a value in an _m_ x _n_ matrix. This matrix has the following properties:

* Integers in each row are sorted from left to right.
* The first integer of each row is greater than the last integer of the previous row.

**Example 1:**

```text
Input:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 3
Output: true
```

**Example 2:**

```text
Input:
matrix = [
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
target = 13
Output: false
```

### 题意和分析

在_m_ x _n_矩阵中寻找一个target，矩阵左右和上下都是升序排好的，并且上一行最后一个数不会大于下一行的第一个数，找到就返回true，否则返回false。这个题的解法可以把二维矩阵转换成一维矩阵，然后查找时注意一下二维矩阵下标的定位即可。

跟Binary Search 一样，Time：O\(log\(m\*n\)\)m和n分别是行数和列数；也可以说成O\(logn\)，n为二维矩阵的原书个数； Space：O\(1\)。

### 代码

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0) {
            return false;
        }

        int start = 0;
        int rows = matrix.length, cols = matrix[0].length;//注意这里没有-1
        int end = rows * cols - 1; //处理成一维数组的总长

        while (start <= end) {
            int mid = start + (end - start) / 2;
            if (matrix[mid / cols][mid % cols] == target) {//中间值的下标值除以列数值就等于中间值所位于的行数（总长==rows*cols-1），取余列数值则等于中间值所处的列数，从而可以定位到二维矩阵的两个下标
                return true;
            } else if (matrix[mid / cols][mid % cols] < target) {//target在“右边”
                start = mid + 1;
            } else {//target在“左边”
                end = mid - 1;
            }
        }
        return false;
    }
}
```

## 240 - Search a 2D Matrix II

### 原题概述

Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:

* Integers in each row are sorted in ascending from left to right.
* Integers in each column are sorted in ascending from top to bottom.

**Example:**

Consider the following matrix:

```text
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

Given target = `5`, return `true`.

Given target = `20`, return `false`.

### 题意和分析

和上一道题相比，矩阵左右和上下也都是升序排好的，但是上一行最后一个数或多个数不一定小于于下一行的第一个数或多个数。这道题的解法可以从右上角开始\(从左下角开始也是可以的\)，比较target 和 matrix\[row\]\[column\]的值。 如果小于target，则该行不可能有此数，所以row++；如果大于target；则该列不可能有此数，所以column--；遇到边界则表明该矩阵不含target。

Time：O\(m+n\)；Space：O\(1\)。

### 代码

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
            return false;
        }
        int row = 0, col = matrix[0].length - 1;//初始化从第一行最右边开始
        while (row < matrix.length && col >= 0) {
            if (target == matrix[row][col]) {//找到了，返回true
                return true;
            } else if (target < matrix[row][col]) {//如果target小于初始右上角的值matrix[row][col]，那么target不会在这一列上，这时候将列的index左移
                col--;
            } else {//反之如果target大于初始右上角的值matrix[row][col]，那么target不会在这一行上，这时候将row下移
                row++;
            }
        }
        return false;
    }
}
```

## 162 - Find Peak Element

### 原题概述

A peak element is an element that is greater than its neighbors.

Given an input array `nums`, where `nums[i] ≠ nums[i+1]`, find a peak element and return its index.

The array may contain multiple peaks, in that case return the index to any one of the peaks is fine.

You may imagine that `nums[-1] = nums[n] = -∞`.

**Example 1:**

```text
Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index number 2.
```

**Example 2:**

```text
Input: nums = [1,2,1,3,5,6,4]
Output: 1 or 5 
Explanation: Your function can return either index number 1 where the peak element is 2, 
             or index number 5 where the peak element is 6.
```

### 题意和分析

这道题是求数组的一个峰值，这个峰值可以是局部的最大值，这里用遍历整个数组找最大值肯定会出现Time Limit Exceeded，比O\(n\)更加优化的自然就是O\(logn\)了，所以考虑使用类似于二分查找法来缩短时间，由于只是需要找到任意一个峰值，那么在确定二分查找折半后中间那个元素后，和紧跟的那个元素（右边）比较下大小，如果大于右边的数，则说明峰值在左边，如果小于则在右边。这样就可以至少找到一个峰值了，Time：O\(logn\), Space：O\(1\)。。 

另外一种更简单的解法：由于题目中说明了局部峰值是存在的，那么实际上可以从第二个数字开始往后遍历，如果第二个数字比第一个数字小，说明此时第一个数字就是一个局部峰值；否则就往后继续遍历，现在是个递增趋势，如果此时某个数字小于前面那个数字，说明前面数字就是一个局部峰值，返回位置即可。如果循环结束了，说明原数组是个递增数组，返回最后一个位置即可，这是最坏结果，Time：O\(n\), Space：O\(1\)。

### 代码

二分法

```java
class Solution {
    public int findPeakElement(int[] nums) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        int left = 0, right = nums.length - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[mid + 1]) {//先比较右边不会越界
                right = mid;//在左边，有可能是mid自己
            } else {
                left = mid + 1;//如果是小于，不会是mid自己，所以mid+1
            }
        }
        return right;//从0开始计算index，最后一轮是right < left跳出
    }
}
```

从第二个数字开始找

```java
class Solution {
    public int findPeakElement(int[] nums) {
        if (nums == null || nums.length == 0) {
            return -1;
        }
        for (int index = 1; index < nums.length; index++) {
            if (nums[index] < nums[index - 1]) {
                return index - 1;
            }
        }
        return nums.length - 1;//递增序列是最坏情况
    }
}
```

