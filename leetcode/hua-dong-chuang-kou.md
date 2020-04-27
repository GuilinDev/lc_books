# 滑动窗口

## 209 Minimize Size SubArray Sum

### 原题

Given an array of **n** positive integers and a positive integer **s**, find the minimal length of a **contiguous** subarray of which the sum ≥ **s**. If there isn't one, return 0 instead.

**Example:** 

```text
Input: s = 7, nums = [2,3,1,2,4,3]
Output: 2
Explanation: the subarray [4,3] has the minimal length under the problem constraint.
```

**Follow up:**If you have figured out the O\(n\) solution, try coding another solution of which the time complexity is O\(n log n\). 

### 题意

求满足大于等于一个target值的最短的子数组，注意这里是连续子数组，暴力解法就是两层循环套上+计算子数组的和，所以是O\(n^3\)；最优的解法是O\(n\)，利用滑动窗口的两个索引，如果sum比target小，挪动右索引使sum增加；如果sum比target大于等于，就把左索引的值从sum中减去并挪动左索引，然后两个索引相减得出滑动过程中最短的窗口。

这道题还应该锻炼下O\(n^2\)和O\(nlogn\)的写法，是个非常不错的练习。

### 代码

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        //[left...right]
        int left = 0;
        int right = -1; //刚开始不希望滑动窗口里面不要有任何元素
        
        int result = nums.length + 1;
        int sum = 0;
        while (left < nums.length) {
            if (right + 1 < nums.length && sum < s) { // right不能越界这点不能忘记
                right++;
                sum += nums[right];
            } else {
                sum -= nums[left];
                left++;
            }
            
            //sum计算后再判断
            if (sum >= s) {
                result = Math.min(result, right - left + 1);
            }
        }
        
        if (result == nums.length + 1) {
            return 0;
        }
        return result;
    }
}
```

