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



### 题意和分析



### 代码



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

![](../../.gitbook/assets/image%20%2815%29.png)

2）局部排序，只排最大的k个数，利用冒泡排序的办法（因为能知道kth的位置），冒泡一次，找出一个最大值，冒k次就找到第kth的最大值了，O\(n\*k\)；

![](../../.gitbook/assets/image%20%2818%29.png)

3）利用heap，创建一个最小堆用来存储当前最大的k个元素

![](../../.gitbook/assets/image%20%289%29.png)

接着，从第k+1个元素开始扫描，和堆顶（堆中最小的元素）比较，如果被扫描的元素大于堆顶，则替换堆顶的元素，并调整堆，以保证堆内的k个元素，总是当前最大的k个元素。

![](https://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOzzA7pbponFmibHaMYQ5Vkk9xBxbWpznlYmMsItZ7Vd3B4MA0TzALhulicsNWrQ1icjaacibL19XOmicVg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

直到，扫描完所有n-k个元素，最终堆中的k个元素，就是猥琐求的TopK。复杂度 O\(n\*lg\(k\)\)。

4）随机选择

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

利用Heap



随机选择



