# 排序

## **56 Merge Intervals**

### **原题概述**

Given a collection of intervals, merge all overlapping intervals.

**Example 1:**

```text
Input: [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
```

**Example 2:**

```text
Input: [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considerred overlapping.
```

### **题意和分析**

要求合并区间，可以对区间集进行自定义的排序，按照start的值从小到大排序，排完序后开始合并，首先初始化为第一个区间的start和end，然后从开始遍历，如果遍历到的区间和当前区间无重叠，直接将区间存入到结果中；如果有重叠，将结果中最后一个区间的end值更新为这个end值和遍历到的end值之间的较大值，直至结束。排序的时间是主要的指数， O\(n log\(n\)\)。

### **代码**

```java
/**
 * Definition for an interval.
 * public class Interval {
 *     int start;
 *     int end;
 *     Interval() { start = 0; end = 0; }
 *     Interval(int s, int e) { start = s; end = e; }
 * }
 */
class Solution {
    public List<Interval> merge(List<Interval> intervals) {
        if (intervals.size() <= 1) {
            return intervals;
        }

        //Java8的lambda的comparator，把类匿名类作为参数
        intervals.sort((i1, i2) -> Integer.compare(i1.start, i2.start));

        List<Interval> result = new LinkedList<>();//链表保持顺序

        //初始化
        int start = intervals.get(0).start;
        int end = intervals.get(0).end;

        for (Interval interval : intervals) {
            if (interval.start <= end) {//遍历到的interval的start小于之前的end，有重叠,先延伸
                end = Math.max(end, interval.end);
            } else {//没有重叠，将当前的interval加入到结果集
                result.add(new Interval(start, end));
                //加入之前的区间后，把start和end更新为当前遍历到的interval的start和end
                start = interval.start;
                end = interval.end;
            }
        }
        //最后的一个区间没有遍历到的interval比较，需要加入
        result.add(new Interval(start, end));

        return result;
    }
}
```

## **242 Valid Anagram**

### **原题概述**

Given two strings _s_ and _t_ , write a function to determine if _t_ is an anagram of _s_.

**Example 1:**

```text
Input: s = "anagram", t = "nagaram"
Output: true
```

**Example 2:**

```text
Input: s = "rat", t = "car"
Output: false
```

**Note:**  
You may assume the string contains only lowercase alphabets.

**Follow up:**  
What if the inputs contain unicode characters? How would you adapt your solution to such case?

### **题意和分析**

1）toCharArray\(\)排序后比较两个字符串是否相同；

2）利用HashMap来记录第一个字符串的数目，然后第一个字符串根据出现次数来减；

3）创建一个26位的小写字母字符数组，第一次循环相应地加减两个字符串的字符数目，第二次循环看是否有不为0的字符位置，有则返回false，没有则返回true。

### **代码**

只适用于26个字母，如果想考虑所有Unicode的情况可以把数组长度设为127

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        int[] alphabet = new int[26];
        for (int i = 0; i < s.length(); i++) {
            alphabet[s.charAt(i) - 'a']++;
            alphabet[t.charAt(i) - 'a']--;
        }
        //检查数组的各字符是否相等
        for (int ab : alphabet) if (ab != 0) return false;
        return true;
    }
}
```

HashMap的做法

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }
        HashMap<Character, Integer> map = new HashMap<>();
        for (int i = 0; i < s.length(); i++) {

            //getOrDefault()
            int count1 = map.getOrDefault(s.charAt(i), 0) + 1;
            map.put(s.charAt(i), count1);

            int count2 = map.getOrDefault(t.charAt(i), 0) - 1;
            map.put(t.charAt(i), count2);

        }

        //检查hashmap的值
        for (Map.Entry<Character, Integer> entry : map.entrySet()) {
            if (entry.getValue() != 0) {
                return false;
            }
        }
        return true;
    }
}
```

## **179 Largest Number**

### **原题概述**

Given a list of non negative integers, arrange them such that they form the largest number.

**Example 1:**

```text
Input: [10,2]
Output: "210"
```

**Example 2:**

```text
Input: [3,30,34,5,9]
Output: "9534330"
```

**Note:** The result may be very large, so you need to return a string instead of an integer.

### **题意和分析**

给一堆正整数，要求拼出最大的数字，返回最大数字的字符串；做法是对整个数组的元素转换成字符串进行排序，但这个排序不是简单地比较大小，而是首位最大的数字在前面，比如71和59，71应该排在前面；如果首位相同，那就再比较第二位看谁大，如果第二位大，那就把该数放在前面，以此类推，比如753和751；注意3和30（比较3和0），52和5239（比较5和3），2570和25（比较7和2）， 909和90这样的情况（比较9和9），前n位数都相同，最后某个数a的n+1位没有了，另一个数b则还有位数这样的情况，都是前面的数应该更大，应该比较这个多出来的n+1位和首位数字，如果a的首位数大于b的n+1位数，a在前面；如果a的首位数小于等于b的n+1位数，b在前面。

coding的时候只需把两个数字的两种链接方式都试试，来比较一下就知道谁大谁小，不用具体考虑上述的情况。

### **代码**

手动实现comparator

```java
class Solution {
    public String largestNumber(int[] nums) {
        if (nums == null || nums.length == 0) {
            return "";
        }

        //将整型数组转换为字符串数组
        String[] s_num = new String[nums.length];
        for (int i = 0; i < s_num.length; i++) {
            s_num[i] = String.valueOf(nums[i]);
        }

        //手动实现一个comparator
        Comparator<String> comp = new Comparator<String>() {
            @Override
            public int compare(String str1, String str2) {
                String s1 = str1 + str2;
                String s2 = str2 + str1;
                return s2.compareTo(s1);//这里把大的放在前面
            }
        };


        Arrays.sort(s_num, comp);

        //极端情况，如果头部给了0
        if (s_num[0].charAt(0) == '0') return "0";

        StringBuilder sb = new StringBuilder();
        for (String s : s_num) {
            sb.append(s);
        }

        return sb.toString();
    }
}
```

Java8的做法

```java
class Solution {
    public String largestNumber(int[] nums) {

        String result = Arrays.stream(nums)
                .mapToObj(String::valueOf)
                .sorted((a, b) -> (b+a).compareTo(a+b))
                .collect(Collectors.joining(""));

        return result.startsWith("0") ? "0" : result;
    }
}
```

堆排序HeapSort

```java
class Solution {
    public String largestNumber(int[] nums) {
        if (nums == null || nums.length == 0) {
            return "";
        }

        Queue<String> queue = new PriorityQueue<>(nums.length,
                (left, right) -> Long.valueOf(right + left).compareTo(Long.valueOf(left + right))
        );
        for (int num : nums) {
            queue.offer(String.valueOf(num));
        }

        StringBuilder sb = new StringBuilder(nums.length);
        while (!queue.isEmpty()) {
            sb.append(queue.poll());//顺序从堆顶取出元素
        }

        String result = sb.toString();
        return result.startsWith("0") ? "0" : result;
    }
}
```

## **164 Maximum Gap**

### **原题概述**

Given an unsorted array, find the maximum difference between the successive elements in its sorted form.

Return 0 if the array contains less than 2 elements.

**Example 1:**

```text
Input: [3,6,9,1]
Output: 3
Explanation: The sorted form of the array is [1,3,6,9], either
             (3,6) or (6,9) has the maximum difference 3.
```

**Example 2:**

```text
Input: [10]
Output: 0
Explanation: The array contains less than 2 elements, therefore return 0.
```

**Note:**

* You may assume all elements in the array are non-negative integers and fit in the 32-bit signed integer range.
* Try to solve it in linear time/space.

### **题意和分析**

这道题找出排序前的数组中在排序后两个相邻数字最大的差值，如果先对数组排序再挨个找那就是_nO\(logn\)_了，不满足题目的线性时间复杂度要求；但是空间复杂度没有要求，于是用空间换时间，利用桶排序bucket sort，首先找出数组的最大值max和最小值min，之后确定每个桶的容量，为\(max - min\)/数组元素个数 + 1，然后确定桶的个数，\(max - min\)/桶的容量 + 1；然后再每个桶里找出这个桶的最大值和最小值，数组中最大间距的两个数不会在一个桶里，而应该在一个桶里的最小值和另一个桶的最大值之间的间距。

### **代码**

```java
class Solution {
    public int maximumGap(int[] nums) {
        if (nums == null || nums.length < 2) {
            return 0;
        }

        //找到数组中的最大值和最小值
        int min = nums[0];
        int max = nums[0];
        for (int i : nums) {
            min = Math.min(min, i);
            max = Math.max(max, i);
        }

        //gap的最小的可能的值，除法向上取值
        int gap = (int) Math.ceil((double)(max - min)/(nums.length - 1));
        int[] bucketsMIN = new int[nums.length - 1];//存储每个桶里局部的最小值
        int[] bucketsMAX = new int[nums.length - 1];//存储每个桶里局部的最大值
        //初始化局部最大值和最小值的两个数组
        Arrays.fill(bucketsMIN, Integer.MAX_VALUE);
        Arrays.fill(bucketsMAX, Integer.MIN_VALUE);

        //把数字放入到桶里
        for (int i : nums) {
            if (i == min || i == max) continue;
            int idx = (i - min) / gap;//每个桶右边位置的索引
            bucketsMIN[idx] = Math.min(i, bucketsMIN[idx]);
            bucketsMAX[idx] = Math.max(i, bucketsMAX[idx]);
        }

        //扫描整个桶找到最大的gap
        int maxGap = Integer.MIN_VALUE;
        int previous = min;
        for (int i = 0; i < nums.length - 1; i++) {
            if (bucketsMIN[i] == Integer.MAX_VALUE && bucketsMAX[i] == Integer.MIN_VALUE) continue; //空桶

            //最小值减去前面的value为当前的gap
            maxGap = Math.max(maxGap, bucketsMIN[i] - previous);
            //更新previous的值
            previous = bucketsMAX[i];
        }
        maxGap = Math.max(maxGap, max - previous);//最后得到最大的gap值
        return maxGap;
    }
}
```

## **280 Wiggle Sort**

### **原题概述**

### **题意和分析**

### **代码**

## **75 Sort Colors**

### **原题概述**

Given an array with _n_ objects colored red, white or blue, sort them [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) ****so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

**Note:** You are not suppose to use the library's sort function for this problem.

**Example:**

```text
Input: [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Follow up:**

* A rather straight forward solution is a two-pass algorithm using counting sort. First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
* Could you come up with a one-pass algorithm using only constant space?

### **题意和分析**

题目中说了如果两次扫描的做法，先计数，然后打印出来，利用HashMap或者count来统计，然后再把打印即可。如果想一次扫描就排好序，利用双指针的办法，因为0，1，2分别代表red，white，blue，最后需要排成012这样的顺序，那就把第一个索引指向头部，第二个索引指向尾部，然后遍历数组，如果遇到当前元素为0，则当前元素与第一个索引交换值，同时第一个索引往后移动一步；如果遇到2，则当前元素与第二个索引交换值，同时第二个索引往前移动一步；如果遇到1，继续遍历不作改变。

### **代码**

```java
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0 || nums.length == 1) {
            return;
        }
        int red = 0, blue = nums.length - 1;
        for (int i = 0; i <= blue; i++) {//循环到第二个索引处就可以停止了
            if (nums[i] == 0) {
                int temp0 = nums[i];
                nums[i] = nums[red];
                nums[red] = temp0;
                red++;
            }
            if (nums[i] == 2) {
                int temp2 = nums[i];
                nums[i] = nums[blue];
                nums[blue] = temp2;
                blue--;
                i--;//i的值需要自减1是因为从blue的索引处交换一个元素到前面，需要停在当前步再检查一下
            }
        }
    }
}
```

###  

