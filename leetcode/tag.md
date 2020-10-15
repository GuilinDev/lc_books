# 9 Tag

## 1 Two Sum 

### [https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum\#1-2sum](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum#1-2sum)

## 560 SubArray Sum Equals to K 

### [https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray\#560-subarray-sum-equals-k](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray#560-subarray-sum-equals-k)

## 85 Maximal Rectangle

### 原题

### 思路

### 代码



## 767 Reorganize String

### 原题

### 思路

### 代码

## 528 Random Pick with Weight

### 原题

### 思路

### 代码

## 843 Guess the Word

### 原题

### 思路

### 代码

## 329 Longest Increasing Path in a Matrix 

### 原题

### 思路

### 代码

##  428 Serialize and Deserialize N-ary Tree 

### 原题

### 思路

### 代码

## 1438 Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit 

### 原题

### 思路

### 代码

##  729 My Calendar I 

### 原题

### 思路

### 代码

##  722 Remove Comments 

### 原题

### 思路

### 代码

## 1031 Maximum Sum of Two Non-Overlapping Subarrays

### 原题

### 思路

### 代码

##  1060 Missing Element in Sorted Array 

### 原题

### 思路

### 代码

## 642 Design Search Autocomplete System 

### 原题

### 思路

### 代码

## 359 Logger Rate Limiter 

### 原题

### 思路

### 代码

## 946 Validate Stack Sequences 

### 原题

### 思路

### 代码

## 1293 Shortest Path in a Grid with Obstacles Elimination 

### 原题

### 思路

### 代码

## 1048 Longest String Chain 

### 原题

### 思路

### 代码

## 1423 Maximum Points You Can Obtain from Cards 

### 原题

### 思路

### 代码

## 1153 String Transforms Into Another String 

### 原题

### 思路

### 代码

## 727 Minimum Window Subsequence 

### 原题

### 思路

### 代码

## 489 Robot Room Cleaner 

### 原题

### 思路

### 代码

## 346 Moving Average from Data Stream 

### 原题

### 思路

### 代码

## 753 Cracking the Safe 

### 原题

### 思路

### 代码

## 809 Expressive Words 

### 原题

### 思路

### 代码

## 471 Encode String with Shortest Length 

### 原题

### 思路

### 代码

## 1110 Delete Nodes And Return Forest 

### 原题

### 思路

### 代码

## 777 Swap Adjacent in LR String 

### 原题

### 思路

### 代码

## 659 Split Array into Consecutive Subsequences 

### 原题

### 思路

### 代码

## 299 Bulls and Cows 

### 原题

### 思路

### 代码

## 1477 Find Two Non-overlapping Sub-arrays Each With Target Sum 

### 原题

### 思路

### 代码

## 752 Open the Lock 

### 原题

### 思路

### 代码

## 444 Sequence Reconstruction 

### 原题

### 思路

### 代码

## 951 Flip Equivalent Binary Trees 

### 原题

### 思路

### 代码

## 1376 Time Needed to Inform All Employees 

### 原题

### 思路

### 代码

## 715 Range Module 

### 原题

### 思路

### 代码

## 833 Find And Replace in String

### 原题

### 思路

### 代码 

## 652 Find Duplicate Subtrees

### 原题

### 思路

### 代码 

## 846 Hand of Straights

### 原题

### 思路

### 代码 

## 1296 Divide Array in Sets of K Consecutive Numbers

### 原题

### 思路

### 代码 

## 1548 The Most Similar Path in a Graph 

### 原题

### 思路

### 代码

## 1463Cherry Pickup II

### 原题

### 思路

### 代码

## 1504 Count Submatrices With All Ones 

### 原题

### 思路

### 代码 

## 1231 Divide Chocolate 

### 原题

### 思路

### 代码

## 1345 Jump Game IV

### 原题

### 思路

### 代码 

## 1138 Alphabet Board Path 

### 原题

### 思路

### 代码 

## 562 Longest Line of Consecutive One in Matrix 

### 原题

### 思路

### 代码

## 690 Employee Importance

## 原题

### 思路

### 代码 

## 900 RLE Iterator 

### 原题

### 思路

### 代码

## 552 Student Attendance Record II

### 原题

### 思路

### 代码

## 853 Car Fleet

### 原题

`N` cars are going to the same destination along a one lane road.  The destination is `target` miles away.

Each car `i` has a constant speed `speed[i]` \(in miles per hour\), and initial position `position[i]` miles towards the target along the road.

A car can never pass another car ahead of it, but it can catch up to it, and drive bumper to bumper at the same speed.

The distance between these two cars is ignored - they are assumed to have the same position.

A _car fleet_ is some non-empty set of cars driving at the same position and same speed.  Note that a single car is also a car fleet.

If a car catches up to a car fleet right at the destination point, it will still be considered as one car fleet.

  
How many car fleets will arrive at the destination?

**Example 1:**

```text
Input: target = 12, position = [10,8,0,5,3], speed = [2,4,1,1,3]
Output: 3
Explanation:
The cars starting at 10 and 8 become a fleet, meeting each other at 12.
The car starting at 0 doesn't catch up to any other car, so it is a fleet by itself.
The cars starting at 5 and 3 become a fleet, meeting each other at 6.
Note that no other cars meet these fleets before the destination, so the answer is 3.
```

  
**Note:**

1. `0 <= N <= 10 ^ 4`
2. `0 < target <= 10 ^ 6`
3. `0 < speed[i] <= 10 ^ 6`
4. `0 <= position[i] < target`
5. All initial positions are different.

### 思路

做成一个car class，排序后从右往左检查。O\(nlogn\)。

### 代码

```java
class Solution {
    /**
     [0,3,5,8,10]
     [1,3,1,4,2]
     */
    public static int carFleet(int target, int[] position, int[] speed) {
        if (position == null || position.length == 0 || speed == null || speed.length == 0 || position.length != speed.length) {
            return 0;
        }
        int len = position.length;
        Car[] cars = new Car[len];
        for (int i = 0; i < len; i++) {
            cars[i] = new Car(position[i], speed[i], (target - position[i]) * 1.0 / speed[i]);
        }

        // sort by using position
        Arrays.sort(cars, Comparator.comparingLong(a -> a.position));

        int numOfFleets = 1;

        int curCar = len;
        while (--curCar > 0) {
            if (cars[curCar].timeExpected < cars[curCar - 1].timeExpected) {
                numOfFleets++;
            } else {
                cars[curCar - 1] = cars[curCar];
            }
        }
        return numOfFleets;
    }
    static class Car {
        int position;
        int speed;
        double timeExpected;
        public Car(int p, int s, double t) {
            this.position = p;
            this.speed = s;
            this.timeExpected = t;
        }
    }
}
```

