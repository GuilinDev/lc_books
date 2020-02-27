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

这道题找出排序前的数组中在排序后两个相邻数字最大的差值，如果先对数组排序再挨个找那就是_O\(nlogn\)_了，不满足题目的线性时间复杂度要求；但是空间复杂度也可以是线性的，于是用空间换时间，利用桶排序bucket sort，首先找出数组的最大值max和最小值min，之后确定每个桶的容量，为\(max - min\)/数组元素个数 + 1，然后确定桶的个数，\(max - min\)/桶的容量 + 1；然后再每个桶里找出这个桶局部的最大值和最小值，数组中最大间距的两个数不会在一个桶里，而应该在一个桶里的最小值和另一个桶的最大值之间的间距。

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

Given an unsorted array `nums`, reorder it **in-place** such that `nums[0] <= nums[1] >= nums[2] <= nums[3]...`.

**Example:**

```text
Input: nums = [3,5,2,1,6,4]
Output: One possible answer is [3,5,1,6,2,4]
```

### **题意和分析**

这道题要求钟摆排序，首先我们可以先对数组排序，排完序以后第0个数不用管，然后交换第1个数和第2个数，交换第3个数和第4个数，依次类推即可；

优化的办法，那就是每次当前元素nums\[i\]和前一个元素nums\[i-1\]相比，奇数位的元素nums\[i\]肯定比前面的元素nums\[i-1\]大于或等于，如果小于，交换二者的值；偶数位的元素nums\[i\]肯定比前面的元素nums\[i-1\]小于或等于，如果的大于，同样交换二者的值。

### **代码**

排序的办法

```java
class Solution {
    public void wiggleSort(int[] nums) {
        if (nums == null || nums.length == 0) return;

        Arrays.sort(nums);
        for (int i = 1; i < nums.length - 1; i+=2) {
            int temp = nums[i];
            nums[i] = nums[i+1];
            nums[i+1] = temp;
        }
    }
}
```

线型复杂度

```java
class Solution {
    public void wiggleSort(int[] nums) {
        if (nums == null || nums.length == 0) return;

        for (int i = 1; i < nums.length; i++) {
            if ((i % 2 != 0 && nums[i] < nums[i-1]) || i % 2 == 0 && nums[i] > nums[i-1]) {
                int temp = nums[i];
                nums[i] = nums[i-1];
                nums[i-1] = temp;
            }
        }
    }
}
```

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

题目中说了如果两次扫描的做法，先计数，然后打印出来，利用一个三个元素的array或者HashMap或，然后再把打印即可。

如果想一次扫描就排好序，利用三路快排的思想，因为0，1，2分别代表one，two，three，最后需要排成012这样的顺序，那就用两个索引，把第一个索引指向头部\(-1\)，第二个索引指向尾部\(nums.length\(\)\)，然后遍历数组，如果遇到当前元素为0，则当前元素与第一个索引交换值，同时第一个索引往后移动一步；如果遇到2，则当前元素与第二个索引交换值，同时第二个索引往前移动一步；如果遇到1，继续遍历不作改变。

当然，三个指针也是可以的，都从0开始，遍历nums，如果等于2，只有第三个指针移动；如果等于1，第二个和第三个指针一起移动；如果等于0，三个指针都移动。

### **代码**

计数排序的办法，先算出每个元素出现的次数，然后再填充回nums中，需要扫描两遍。

```java
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0) {
            return;
        }
        //统计0，1，2各自出现的次数
        int[] counts = new int[3];
        for (int i = 0; i <= nums.length - 1; i++) {
            assert(nums[i] >= 0 && nums[i] <= 2);
            counts[nums[i]]++;
        }
        int index = 0;//from left to right in nums[],表示0，1各自的锚定点
        for (int i = 0; i <= counts.length - 1; i++){ // 0, 1, 2
            for (int j = index; j <= index + counts[i] - 1; j++) {
                nums[j] = i;
            }
            //填完0/1后移动锚定点
            index += counts[i];
        }
    }
}
```

三路快排思想

```java
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0) {
            return;
        }
        
        int zero = -1; // [0...zero]
        int two = nums.length; // [two...nums.length - 1] 
        for (int i = 0; i < two; ) { //i在交换后不一定会i++,可能继续处理当前元素.two索引以及后面的元素不用管
            if (nums[i] == 0) {
                zero++;
                swap(nums, zero, i);
                i++; //当前元素i为0，交换后zero位置（区间最右边的0）的元素为0，所以需要往右处理下一个元素
            } else if (nums[i] == 2) {
                two--;
                swap(nums, two, i);
                //这里当前元素i为2，交换到two的位置（区间最左边的2）的元素为2，但是交换到i位置的元素还不知道是什么，下一轮循环需要继续处理
            } else { // should be 1 now
                assert(nums[i] == 1);
                i++;//遇到1直接往右走，不用交换
            }
        }
    }
    private static void swap(int[] nums, int index1, int index2) { 
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
        //注意这里是pass by value of reference,以下两种解法会影响只有一个元素的比如[2]这样的例子，交换双方nums[index1]和nums[index2]一起修改
        //nums[index1] = nums[index1] + nums[index2];
        //nums[index2] = nums[index1] - nums[index2];
        //nums[index1] = nums[index1] - nums[index2];
        //或
        //nums[index1] = nums[index1] ^ nums[index2];
        //nums[index2] = nums[index1] ^ nums[index2];
        //nums[index1] = nums[index1] ^ nums[index2];
        //或者写成以下的解法
    }
}


//——————————————————————————————————————————————————
class Solution {
    public void sortColors(int[] nums) {
        if (nums == null || nums.length == 0) {
            return;
        }
        
        int zero = -1; // [0...zero]
        int two = nums.length; // [two...nums.length - 1] 
        for (int i = 0; i < two; ) { //i在交换后不一定会i++,可能继续处理当前元素.two索引以及后面的元素不用管
            if (nums[i] == 0) {
                zero++;
                if (zero != i) {
                    //传引用的值
                    swap(nums, zero, i);
                }
                i++; //当前元素i为0，交换后zero位置（区间最右边的0）的元素为0，所以需要往右处理下一个元素
            } else if (nums[i] == 2) {
                two--;
                if (two != i) {
                    //传引用的值
                    swap(nums, two, i);
                }
                //这里当前元素i为2，交换到two的位置（区间最左边的2）的元素为2，但是交换到i位置的元素还不知道是什么，下一轮循环需要继续处理
            } else { // should be 1 now
                assert(nums[i] == 1);
                i++;//遇到1直接往右走，不用交换
            }
        }
    }
    private static void swap(int[] nums, int index1, int index2) {
        nums[index1] = nums[index1] ^ nums[index2];
        nums[index2] = nums[index1] ^ nums[index2];
        nums[index1] = nums[index1] ^ nums[index2];
    }
}
```

## 252 Meeting Rooms

### 原题概述

Given an array of meeting time intervals consisting of start and end times `[[s1,e1],[s2,e2],...]` \(si &lt; ei\), determine if a person could attend all meetings.

**Example 1:**

```text
Input: [[0,30],[5,10],[15,20]]
Output: false
```

**Example 2:**

```text
Input: [[7,10],[2,4]]
Output: true
```

### 题意和分析

给了一系列会议的时间，问能否同时参见所有的会议，这实际上是求区间是否有交集，朴素解法就是两个for loop，每两个区间比较一下，看是否有overlap，有的话直接返回false。比较两个区间a和b是否有overlap，只用看一个区间的起始位置就好，有两种情况，1）如果a的起始位置大于等于b的起始位置，且a的起始位置小于b的结束位置，那么一定有overlap，2）反过来，如果b的起始位置大于等于a的起始位置，且此时b的起始位置小于a的结束位置，那么也一定有overlap。

先给intervals按照起始时间排个序，然后再只比较起始时间，如果后一个会议的起始时间小于前一个会议的结束时间，返回false，复杂度O\(nlogn\)会好一点。

这道题利用treemap的特性也可以解。

### 代码

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
    public boolean canAttendMeetings(Interval[] intervals) {
        if (intervals == null || intervals.length == 0) {//没有会议，返回true，可以参加
            return true;
        }
        Arrays.sort(intervals, new Comparator<Interval>(){
            public int compare(Interval a, Interval b) {
                return a.start - b.start;
            }
        });
        for (int i = 1; i < intervals.length; i++) {//因为需要跟前面的interval比较，所以从index 1开始
            if (intervals[i].start < intervals[i - 1].end) {
                return false;
            }
        }
        return true;
    }
}
```

Java8 Lamda的写法，将函数体作为参数，进行排序

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
    public boolean canAttendMeetings(Interval[] intervals) {
        if (intervals == null || intervals.length == 0) {//没有会议，返回true，可以参加
            return true;
        }
        Arrays.sort(intervals, (a, b) -> a.start - b.start);
        
        for (int i = 1; i < intervals.length; i++) {//因为需要跟前面的interval比较，所以从index 1开始
            if (intervals[i].start < intervals[i - 1].end) {
                return false;
            }
        }
        return true;
    }
}
```

## 253 Meeting Rooms II

### 原题概述

Given an array of meeting time intervals consisting of start and end times `[[s1,e1],[s2,e2],...]` \(si &lt; ei\), find the minimum number of conference rooms required.

**Example 1:**

```text
Input: [[0, 30],[5, 10],[15, 20]]
Output: 2
```

**Example 2:**

```text
Input: [[7,10],[2,4]]
Output: 1
```

### 题意和分析

这道题是252的扩展，问最少需要多少个房间，如果同一时间有时间冲突，那就得安排不同的会议室。 这道题有以下解法

1）排序，用两个一维数组来分别保存起始时间和结束时间，各自排序，我们定义结果变量result和结束时间指针endIndex，然后我们开始遍历，如果当前起始时间小于结束时间指针的时间，则结果自增1，反之endIndex自增1，这样我们可以找出重叠的时间段，从而安排新的会议室；

2）TreeMap， 遍历intervals，对于起始时间，映射值自增1，对于结束时间，映射值自减1，然后我们定义结果变量curRoom，和房间数rooms，遍历TreeMap，时间从小到大，房间数每次加上映射值，然后更新结果res，遇到起始时间，映射是正数，则房间数会增加，如果一个时间是一个会议的结束时间，也是另一个会议的开始时间，则映射值先减后加仍为0，并不用分配新的房间，而结束时间的映射值为负数更不会增加房间数；

3）最小堆Min Heap， 这种方法先把所有的时间区间按照起始时间排序，然后新建一个最小堆，开始遍历时间区间，如果堆不为空，且首元素小于等于当前区间的起始时间，去掉堆中的首元素，把当前区间的结束时间压入堆，由于最小堆是小的在前面，那么假如首元素小于等于起始时间，说明上一个会议已经结束，可以用该会议室开始下一个会议了，所以不用分配新的会议室，遍历完成后堆中元素的个数即为需要的会议室的个数。

### 代码

排序解法

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
    public int minMeetingRooms(Interval[] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }
        int len = intervals.length;
        int[] starts = new int[len];
        int[] ends = new int[len];

        int rooms = 0;
        int endIndex = 0;

        for (int i = 0; i < len; i++) {
            starts[i] = intervals[i].start;
            ends[i] = intervals[i].end;
        }
        
        Arrays.sort(starts);
        Arrays.sort(ends);

        for (int i = 0; i < len; i++) {
            if (starts[i] < ends[endIndex]) {//有冲突，新的会议开始了而前面endIndex指向的会议还没结束
                rooms++;
            } else {//没冲突
                endIndex++;
            }
        }
        return rooms;
    }
}
```

TreeMap解法

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
    public int minMeetingRooms(Interval[] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }
        Map<Integer, Integer> map = new TreeMap<Integer, Integer>();//TreeMap会自动升序sort keys
        int rooms = 0;
        int curRoom = 0;

        for (Interval interval : intervals) {
            if (map.containsKey(interval.start)) {
                map.put(interval.start, map.get(interval.start) + 1);
            } else {
                map.put(interval.start, 1);
            }

            if (map.containsKey(interval.end)) {
                map.put(interval.end, map.get(interval.end) - 1);
            } else {
                map.put(interval.end, -1);
            }
        }

        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            rooms = Math.max(rooms, curRoom += entry.getValue());
        }
        return rooms;
    }
}
```

最小堆MinHeap

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
    public int minMeetingRooms(Interval[] intervals) {
        if (intervals == null || intervals.length == 0) {
            return 0;
        }
        int rooms = 0;
        Arrays.sort(intervals, (Interval a, Interval b) -> (a.start - b.start));

        PriorityQueue<Integer> heap = new PriorityQueue();
        for (int i = 0; i < intervals.length; i++) {
            while (!heap.isEmpty() && intervals[i].start >= heap.peek()) {//有些meeting开始前另外一些meeting已经结束了
                heap.poll();
            }
            heap.add(intervals[i].end);
            rooms = Math.max(rooms, heap.size());
        }

        return rooms;
    }
}
```

## 274 H-Index

### 原题

Given an array of citations \(each citation is a non-negative integer\) of a researcher, write a function to compute the researcher's h-index.

According to the [definition of h-index on Wikipedia](https://en.wikipedia.org/wiki/H-index): "A scientist has index h if h of his/her N papers have **at least** h citations each, and the other N − h papers have **no more than** h citations each."

**Example:**

```text
Input: citations = [3,0,6,1,5]
Output: 3 
Explanation: [3,0,6,1,5] means the researcher has 5 papers in total and each of them had 
             received 3, 0, 6, 1, 5 citations respectively. 
             Since the researcher has 3 papers with at least 3 citations each and the remaining 
             two with no more than 3 citations each, her h-index is 3.
```

**Note:** If there are several possible values for _h_, the maximum one is taken as the h-index.

### 题意

这道题要求文章数量的引用数，用H-Index来表示，H-Index为i的意思是至少有i篇文章的引用数大于等于i。没有思路的时候想想暴力解法，假设总文章数为n，那么先试试H-Index为n的情况-看看是否至少有n篇文章引用数&gt;=n，不行试试n-1，时间复杂度为O\(n^2\)；暴力解法脑子里过一遍后会比较容易想到桶排序，用空间换时间，先把每篇文章做一个桶，其中是该文章对应的引用数，共n+1个桶（文章数从0开始），其中第n+1个桶统计&gt;=n的引用数（因为这个桶不专门对应一篇文章），第一次遍历就从小到大统计引用数；第二次遍历从后向前，先找最大的引用数，找到直接返回，时间复杂度两次分开遍历，为O\(n\)。

### 代码

```java
class Solution {
    public int hIndex(int[] citations) {
        int len = citations.length;
        int[] buckets = new int[len + 1];
        
        for (int citation : citations) {
            if (citation >= len) { //大于文章数量的，存在桶排序的最后一个位置
                buckets[len]++;
            } else { //
                buckets[citation]++; 
            }
        }
        
        //从桶的最后一个遍历，那是最大的h-index
        int count = 0;
        for (int i = buckets.length - 1; i >= 0; i--) {
            count += buckets[i]; // 这里是累加，原因是排后面较大的引用数也应该记到前面较小的引用数上
            if (count >= i) { //
                return i;
            }
        }
        return 0;
    }
}
```



