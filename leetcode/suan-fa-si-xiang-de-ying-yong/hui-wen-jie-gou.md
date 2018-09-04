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

### **题意和分析**

### **代码**

## **179 Largest Number**

### **原题概述**

### **题意和分析**

### **代码**

## **164 Maximum Gap**

### **原题概述**

### **题意和分析**

### **代码**

## **280 Wiggle Sort**

### **原题概述**

### **题意和分析**

### **代码**

## **350 Intersection of Two Arrays II**

### **原题概述**

### **题意和分析**

### **代码**

## **75 Sort Colors**

### **原题概述**

### **题意和分析**

### **代码** 

