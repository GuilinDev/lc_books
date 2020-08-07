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

求满足大于等于一个target值的最短的子数组，注意这里是连续子数组，暴力解法就是两层循环套上+计算子数组的和，所以是O\(n^3\)；最优的解法是O\(n\)，

1\) 窗口的做法：利用滑动窗口的两个索引，如果sum比target小，挪动右索引使sum增加；如果sum比target大于等于，就把左索引的值从sum中减去并挪动左索引，然后两个索引相减得出滑动过程中最短的窗口。

2\) 队列的做法：把数组中的元素不停的入队，直到总和大于等于s为止，接着记录下队列中元素的个数，然后再不停的出队，直到队列中元素的和小于s为止（如果不小于s，也要记录下队列中元素的个数，这个个数其实就是不小于s的连续子数组长度，我们要记录最小的即可）。接着再把数组中的元素添加到队列中……重复上面的操作，直到数组中的元素全部使用完为止。

3\)Follow up让练习O\(nlogn\)的办法，想了想快排和归并排序是O\(nlogn\)，但是不好做，可以for循环套一层binary search也是O\(nlogn\)。

 二分搜索的做法：维护一个临时数组sums，记录前缀和，sums\[i\]表示的是原数组nums前i个元素的和，题中说了“给定一个含有 n 个正整数的数组”，既然是正整数，那么相加的和会越来越大，也就是前缀和sums数组中的元素是递增的。只需要找到sums\[right\] - sums\[left\] &gt;= s，那么right - left就是满足的连续子数组，但不一定是最小的，所以要继续找，直到找到最小的为止。怎么找呢，可以使用两个for循环来枚举，但这又和暴力求解一样了，所以可以换种思路，求sums\[right\] - sums\[left\] &gt;= s，移项后为sums\[left\] + s &lt;= sums\[right\]，那这样就好办了，因为数组sums中的元素是递增的，也就是排序的，只需要求出sum\[left\] + s的值，然后使用二分法查找即可找到这个k。



### 代码

维护一个窗口

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        //[left...right]
        int left = 0;
        int right = -1; //刚开始不希望滑动窗口里面不要有任何元素
        
        int result = nums.length + 1;
        int sum = 0;
        while (left < nums.length) { // 因为要找最小子数组，所以需要判断left，left的最后一个位置是nums.length - 1
            if (right < nums.length - 1 && sum < s) { // right到达nums.length - 1的时候不会进入if以免right++
                right++;
                sum += nums[right];
            } else {
                sum -= nums[left];
                left++;
            }
            
            if (sum >= s) {
                result = Math.min(result, right - left + 1);
            }
        }
        
        if (result == nums.length + 1) { // 没有找到一个
            return 0;
        }
        return result;
    }
}
```

模拟一个队列

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int left = 0;
        int right = 0;
        int sum = 0;
        int result = nums.length + 1;
        
        while (right < nums.length) {
            sum += nums[right];
            right++;
            while (sum >= s) {
                result = Math.min(result, right - left); // 注意这里的right已经++了，不用+1
                sum -= nums[left];
                left++;
            }
        }
        
        if (result == nums.length + 1) {
            return 0;
        }
        return result;
    }
}
```

二分搜索

```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
        int len = nums.length;
        int result = nums.length + 1;
        int[] sums = new int[len + 1];
        for (int i = 1; i <= len; i++) { // 先求出前缀和
            sums[i] = sums[i - 1] + nums[i - 1];
        }
        for (int i = 0; i <= len; i++) {
            int target = s + sums[i]; // 二分查找该值
            int index = Arrays.binarySearch(sums, target);
            if (index < 0) {
                // java二分法的实现，如果没找到会返回一个负数，该负数取反得到的正数为待查找的数在数组中应该处于的index
                index = ~index; 
            }
            if (index <= len) {
                result = Math.min(result, index - i);
            }
        }
        return result == nums.length + 1 ? 0 : result;
    }
}
```

