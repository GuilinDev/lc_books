# Array中移除元素

## 27 - Remove Element

### 原题概述

> Given an array _nums_ and a value _val_, remove all instances of that value [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) and return the new length.
>
> Do not allocate extra space for another array, you must do this by **modifying the input array** [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) with O\(1\) extra memory.
>
> The order of elements can be changed. It doesn't matter what you leave beyond the new length.
>
> **Example 1:**
>
> ```text
> Given nums = [3,2,2,3], val = 3,
>
> Your function should return length = 2, with the first two elements of nums being 2.
>
> It doesn't matter what you leave beyond the returned length.
> ```
>
> **Example 2:**
>
> ```text
> Given nums = [0,1,2,2,3,0,4,2], val = 2,
>
> Your function should return length = 5, with the first five elements of nums containing 0, 1, 3, 0, and 4.
>
> Note that the order of those five elements can be arbitrary.
>
> It doesn't matter what values are set beyond the returned length.
> ```
>
> **Clarification:**
>
> Confused why the returned value is an integer but your answer is an array?
>
> Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.
>
> Internally you can think of this:
>
> ```text
> // nums is passed in by reference. (i.e., without making a copy)
> int len = removeElement(nums, val);
>
> // any modification to nums in your function would be known by the caller.
> // using the length returned by your function, it prints the first len elements.
> for (int i = 0; i < len; i++) {
>     print(nums[i]);
> }
> ```

### 题意和分析

这道题很简单，in place的做法就是把非val的值找出来全部排到原Array的前面，用一个index来记录位置，最后返回index的位置就是除去val的数组的长度。

整个数组扫一遍，Time：O\(n\)

**注意：按照题意，这样的处理方式改变了数组里面的元素顺序。**

### 代码

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        int index= 0;//记录位置，从0开始
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != val) {//找到不同等于val的值就排到nums的左边去，然后递增准备存储下一个非val的元素
                nums[index] = nums[i];
                index++;
            }
        }
        return index;
    }
}
```

## 26 - Remove Duplicates from Sorted Array

### 原题概述

> Given a sorted array _nums_, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each element appear only _once_ and return the new length.
>
> Do not allocate extra space for another array, you must do this by **modifying the input array** [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) with O\(1\) extra memory.
>
> **Example 1:**
>
> ```text
> Given nums = [1,1,2],
>
> Your function should return length = 2, with the first two elements of nums being 1 and 2 respectively.
>
> It doesn't matter what you leave beyond the returned length.
> ```
>
> **Example 2:**
>
> ```text
> Given nums = [0,0,1,1,1,2,2,3,3,4],
>
> Your function should return length = 5, with the first five elements of nums being modified to 0, 1, 2, 3, and 4 respectively.
>
> It doesn't matter what values are set beyond the returned length.
> ```
>
> **Clarification:**
>
> Confused why the returned value is an integer but your answer is an array?
>
> Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.
>
> Internally you can think of this:
>
> ```text
> // nums is passed in by reference. (i.e., without making a copy)
> int len = removeDuplicates(nums);
>
> // any modification to nums in your function would be known by the caller.
> // using the length returned by your function, it prints the first len elements.
> for (int i = 0; i < len; i++) {
>     print(nums[i]);
> }
> ```

### 题意和分析

与第27题的解法基本相同，这道题是在一个排好序的Array中，找到有多少个非duplicate的值然后存储在index里，index则在in place的位置上。

Time：O\(n\)

**注意：按照题意，这样的处理方式改变了数组里面的元素顺序，如果尾数有重复值的时候，无法处理。**

### 代码

```java
class Solution {
    public int removeDuplicates(int[] nums) {
        if (nums == null || nums.length == 0) {
            return 0;
        }
        
        int index = 1; //从第1位与第0位开始比较,遇到不同的值则增加一位
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != nums[i - 1]) {
                nums[index] = nums[i];
                index++;
            }
        }
        return index;
        
    }
}
```

## 80 - Remove Duplicates from Sorted Array II

### 原题概述

> Given a sorted array _nums_, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that duplicates appeared at most _twice_ and return the new length.
>
> Do not allocate extra space for another array, you must do this by **modifying the input array** [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) with O\(1\) extra memory.
>
> **Example 1:**
>
> ```text
> Given nums = [1,1,1,2,2,3],
>
> Your function should return length = 5, with the first five elements of nums being 1, 1, 2, 2 and 3 respectively.
>
> It doesn't matter what you leave beyond the returned length.
> ```
>
> **Example 2:**
>
> ```text
> Given nums = [0,0,1,1,1,1,2,3,3],
>
> Your function should return length = 7, with the first seven elements of nums being modified to 0, 0, 1, 1, 2, 3 and 3 respectively.
>
> It doesn't matter what values are set beyond the returned length.
> ```
>
> **Clarification:**
>
> Confused why the returned value is an integer but your answer is an array?
>
> Note that the input array is passed in by **reference**, which means modification to the input array will be known to the caller as well.
>
> Internally you can think of this:
>
> ```text
> // nums is passed in by reference. (i.e., without making a copy)
> int len = removeDuplicates(nums);
>
> // any modification to nums in your function would be known by the caller.
> // using the length returned by your function, it prints the first len elements.
> for (int i = 0; i < len; i++) {
>     print(nums[i]);
> }
> ```

### 题意和分析

与第26题相比，也是在排好序的Array里面移除重复元素，但是允许元素最多出现两次，思路跟26题也是类似，就是需要额外维护一个计数器counter，同样的元素如果如果counter等于2，直接跳过；遇到新元素时重置counter。

整个数组扫一遍，Time：O\(n\)；空间上就维护一个index表示最左边可以被取代的位置，然后较快的指针i与index-2位的元素对比（因为index-2的位置可以保证这个元素和index位置的这个元素不一样-大）。

### 代码

```java
class Solution {
  public int removeDuplicates(int[] nums) {
    if (nums == null || nums.length == 0) {
      return 0;
    }

    int index = 0; //index的意思是最左边的可以被替代的位置
    for (int i = 0; i <= nums.length - 1; i++) {
      if (i < 2 || nums[i] != nums[index - 2]) {
        if (i != index) { //这个条件是优化下自己与自己进行赋值,比如[1,2,3,4,5]这种情况
          nums[index] = nums[i];
        }
        index++;
      }
    }
    return index;
  }
}
```

