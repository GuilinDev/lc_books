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

## 1248 Count Number of Nice Subarrays

### 原题

Given an array of integers `nums` and an integer `k`. A __subarray is called **nice** if there are `k` odd numbers on it.

Return the number of **nice** sub-arrays.

**Example 1:**

```text
Input: nums = [1,1,2,1,1], k = 3
Output: 2
Explanation: The only sub-arrays with 3 odd numbers are [1,1,2,1] and [1,2,1,1].
```

**Example 2:**

```text
Input: nums = [2,4,6], k = 1
Output: 0
Explanation: There is no odd numbers in the array.
```

**Example 3:**

```text
Input: nums = [2,2,2,1,2,2,1,2,2,2], k = 2
Output: 16
```

**Constraints:**

* `1 <= nums.length <= 50000`
* `1 <= nums[i] <= 10^5`
* `1 <= k <= nums.length`

### 分析

1\) 滑动窗口

* 刚开始时，不断移动右指针来扩大滑动窗口，使其包含k个奇数；
* 当滑动窗口已经包含k个奇数时，做如下操作：
  * 统计第 1 个奇数左边的偶数个数 leftEvenCount。 这 leftEvenCount 个偶数都可以作为优美子数组的起点，因此起点的选择有 leftEvenCount + 1 种（可以一个偶数都不取，因此别忘了 +1 ）。
  * 统计第 k 个奇数右边的偶数个数 rightEvenCount 。 这 rightEvenCount 个偶数都可以作为优美子数组的终点，因此终点的选择有 rightEvenCount + 1 种（可以一个偶数都不取，因此别忘了 +1 ）。
  *  因此「优美子数组」左右起点的选择组合数为 `(leftEvenCnt + 1) * (rightEvenCnt + 1)`。

时间复杂度 O\(N\)，空间复杂度 O\(1\)

2\) 前缀和

*  计算前缀和数组 `arr`：遍历原数组，每遍历一个元素，计算当前的前缀和（这里到当前元素为止，数组中有多少个奇数）；
* 对上述前缀和数组，可以双重循环统计 `arr[j] - arr[i] == k` 的个数，这样做是 O\(N^2\) 的（这样做会超时）。
* 优化：可以像「1. 2Sum」那样使用 HashMap 优化到 O\(N\)，其中键是「前缀和」，值是「前缀和的个数」也可以如下面代码一样，使用的是 int\[\] prefixCount 数组，下标是「前缀和」，值是「前缀和的个数」，因此可以遍历原数组，每遍历到一个元素，计算当前的前缀和 sum，就在 result 中累加上前缀和为 sum - k 的个数。

时间复杂度 O\(N\)，空间复杂度 O\(N\)

### 代码

双指针

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        int left = 0, right = 0, oddCount = 0, result = 0;
        while (right < nums.length) {
            // 右指针先走，每遇到一个奇数则 oddCount++。
            if ((nums[right++] & 1) == 1) {
                oddCount++;
            }

            //  若当前滑动窗口 [left, right) 中有 k 个奇数了，进入此分支统计当前滑动窗口中的优美子数组个数。
            if (oddCount == k) {
                // 先将滑动窗口的右边界向右拓展，直到遇到下一个奇数（或出界）
                // rightEvenCount 即为第 k 个奇数右边的偶数的个数
                int temp = right;
                while (right < nums.length && (nums[right] & 1) == 0) {// 偶数就继续右移
                    right++;
                }
                int rightEvenCount = right - temp;
                // leftEvenCount 即为第 1 个奇数左边的偶数的个数
                int leftEvenCount = 0;
                while ((nums[left] & 1) == 0) { // 偶数
                    leftEvenCount++;
                    left++;
                }
                // 第 1 个奇数左边的 leftEvenCount 个偶数都可以作为优美子数组的起点
                // (因为第1个奇数左边可以1个偶数都不取，所以起点的选择有 leftEvenCount + 1 种）
                // 第 k 个奇数右边的 rightEvenCnt 个偶数都可以作为优美子数组的终点
                // (因为第k个奇数右边可以1个偶数都不取，所以终点的选择有 rightEvenCount + 1 种）
                // 所以该滑动窗口中，优美子数组左右起点的选择组合数为 (leftEvenCount + 1) * (rightEvenCount + 1)
                result += (leftEvenCount + 1) * (rightEvenCount + 1);

                // 此时 left 指向的是第 1 个奇数，因为该区间已经统计完了，因此 left 右移一位，oddCount--
                left++;
                oddCount--;
            }

        }

        return result;
    }
}
```

前缀和

```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        // 数组 prefixCount 的下标是前缀和（即当前奇数的个数），值是前缀和的个数。
        int[] prefixCount = new int[nums.length + 1];
        prefixCount[0] = 1;
        // 遍历原数组，计算当前的前缀和，统计到 prefixCount 数组中，
        // 并且在 result 中累加上与当前前缀和差值为 k 的前缀和的个数。
        int result = 0, sum = 0;
        for (int num: nums) {
            sum += num & 1;
            prefixCount[sum]++;
            if (sum >= k) {
                result += prefixCount[sum - k];
            }       
        }
        return result;
    }
}
```

