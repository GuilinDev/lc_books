---
description: kSum问题，归纳为排序后对n-2个数做嵌套循环。
---

# kSum问题

## 1 - 2Sum

### 原题概述



Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have _**exactly**_ one solution, and you may not use the _same_ element twice.

**Example:**

```text
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

### 题意和分析

Leetcode的第一道题，被翻来覆去做过很多遍了；一般就是HashMap存入值和相对应index的信息，回过来再对之前存入的键值对进行查找。

Time: O\(n\),  Space: O\(n\);

另外还有一个办法是可以先对数组排序，然后用两个指针，头尾开始查找，直到找到结果或者两个指针相遇。因为Arrays.sort\(\)使用的排序方法是quick sort和优化过的merge sort，为什么使用了两种排序方法？《Java编程艺术》上的解释是quick sort对基本数据类型包括int，short，long，float，double等的排序，merge sort则是对对象类型进行排序，对比下两个排序办法：

```text
merge sort 
1）最坏时间复杂度是 O(nlogn)；
2）平均时间复杂度是 O(nlogn)；
3）空间复杂度如果用Array实现为O(n),如果用LinkedList来实现为O(logn)。
quick sort 
1）最坏时间复杂度是 O(n^2)；
2）平均时间复杂度是 O(nlogn)；
3）空间复杂度是 O(n)。
```

因此，这个办法的时间复杂度Arrays.sort\(\)的O\(nlogn\)+后面两个指针的的O\(n\) = O\(nlogn\)；另外因为本题是返回两个indices，而不是返回两个值，所以还需要记住之前每个元素的位置，复制一个数组（延伸：Java中常用有几种复制数组的办法？）进行排序，原数组则保持原来顺序。

> 扩展下知识：quick sort 在统计意义上效率比 merge sort 高，为何不都采用 quick sort ？
>
> 使用两个不同类型的排序算法主要是由于 quick sort 是不稳定的，而 merge sort 是 稳定（stable）的。这里的 stable 是指比较相等的数据在排序之后仍然按照排序之前的前后顺序排列（保序性）。对于基本数据类型，稳定性没有意义。而对于对象类型，稳定性比较重要，因为对象相等的判断可能只是判断关键属性，因此最好保持相等对象的非关键属性的顺序与排序前一直；另外一个原因是由于merge sort相对而言比较（compare）的次数比快速排序要少，移动（对象引用的移动，move）次数比快速排序多，而对于对象来说，compare一般比move耗时。

### 代码

HashMap的办法

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];
        Map<Integer, Integer> map = new HashMap<>();
        
        for (int i = 0; i < nums.length; i++) {
            if (map.containsKey(target - nums[i])) {
                //注意存到result的顺序
                result[1] = i;//第1位为当前的i
                result[0] = map.get(target - nums[i]);//第0位为跟当前i匹配之前存入到map中的值和相对应的index
                
                break;
            }
            map.put(nums[i], i);//值和其相对应的index
        }
        return result;
    }
}
```

两个指针的办法

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] sorted = new int[nums.length]; //一个新数组来排序
        System.arraycopy(nums, 0, sorted, 0, nums.length);//deep clone数组
        Arrays.sort(sorted);
        
        int left = 0, right = sorted.length - 1;//新数组里面目标的两个数的indices
        while (left < right) {
            if (sorted[left] + sorted[right] < target) {
                left++;
            } else if (sorted[left] + sorted[right] > target) {
                right--;
            } else {//找到新数组里面的两个数了
                break;
            }
        }
        
        int index1 = -1, index2 = -1; //初始值为-1，不为0，这样知道两个indices是否找到原来数组的对应两个数的索引从而改变过
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == sorted[left] || nums[i] == sorted[right]) {//只可能一个解
                if (index1 == -1) {
                    index1 = i;
                } else {
                    index2 = i;
                }
            }
        }
        
        int [] result = {index1, index2};
        Arrays.sort(result);//之前不知道index1和index2哪个在前，所以需要sort一下
        
        return result;
     }
}
```

## \*167 - 2Sum II - Input Array is Sorted

### 原题概述

Given an array of integers that is already _**sorted in ascending order**_, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.

**Note:**

* Your returned answers \(both index1 and index2\) are not zero-based.
* You may assume that each input would have _exactly_ one solution and you may not use the _same_ element twice.

**Example:**

```text
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.
```

### 题意和分析

是上一道题1-2Sum的变种，按照第二种办法先排序再两个指针找的情况，因为input是sorted的数组，所以连排序都不用了，而且也不用一个额外的数组来存排序之前的indices，相比之下是更简单了。

Time：O\(n\)，Space：O\(1\)。

的当然，跟上一道题一样，也可以用HashMap来解，无论输入的array是否sorted,

Time: O\(n\),  Space: O\(n\)

### 代码

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int[] result = new int[2];
        
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            if (numbers[left] + numbers[right] < target) {
                left++;
            } else if (numbers[left] + numbers[right] > target) {
                right--;
            } else {
                break;//遇到合适的pair直接break，省点时间，有唯一解的情况
            }
        }
        
        //因为两个indices不是zero-based，所以+1，从1开始数
        result[0] = left + 1;
        result[1] = right + 1;
        
        return result;
    }
}
```

## 170 - Two Sum III - Data Structure Design

### 原题概述

Design and implement a TwoSum class. It should support the following operations: `add` and `find`.

`add` - Add the number to an internal data structure.  
`find` - Find if there exists any pair of numbers which sum is equal to the value.

**Example 1:**

```text
add(1); add(3); add(5);
find(4) -> true
find(7) -> false
```

**Example 2:**

```text
add(3); add(1); add(2);
find(3) -> true
find(6) -> false
```

### 题意和分析

这道题跟Array的关系不大，放在这里主要是跟kSum所有的题放在一起。

用1-2Sum里面HashMap的思路来解，add的话是O\(1\)，find的话需要遍历整个keyset，是O\(n\)，Space都是O\(n\)。

### 代码

```java
class TwoSum {
    Map<Integer, Integer> map;//用一个HashMap来保存每次存进来的number的次数

    /** Initialize your data structure here. */
    public TwoSum() {
        map = new HashMap<Integer, Integer>();//初始化map为HashMap
    }
    
    /** Add the number to an internal data structure.. */
    public void add(int number) {
        if (map.containsKey(number)) {
            //如果number之前加入过，则次数+1，因为是2Sum，找两个数，所以map.get(number) + 1写成2也可以
            map.put(number, map.get(number) + 1); 
        } else {
            map.put(number, 1);//第一次加入
        }
    }
    
    /** Find if there exists any pair of numbers which sum is equal to the value. */
    public boolean find(int value) {
        //这里对HashMap的keyset进行遍历，找到是否存在两个数相加等于value
        Iterator<Integer> iterator = map.keySet().iterator();
        while (iterator.hasNext()) {
            int num1 = iterator.next();
            int num2 = value - num1;
            if (map.containsKey(num2)) {//表示第二个数在HashMap中存在，之前加入过，可以考虑配对
                if (num1 != num2 || map.get(num2) >= 2) {//如果两个数不同，肯定可以配对；或者如果两个数相同，第（一）二个数有两个或以上的出现次数，也可以配对，因为一个数不能配对
                    return true;
                }
            }
        }
        return false;
    }
}

/**
 * Your TwoSum object will be instantiated and called as such:
 * TwoSum obj = new TwoSum();
 * obj.add(number);
 * boolean param_2 = obj.find(value);
 */
```

## 653 - 2Sum IV - Input is a BST

这道题实际上是关于Tree而不是Array，[参见这里](https://guilindev.gitbook.io/interview/untitled-1/xun-zhao-tree-zhong-de-nodes)。

## 15 - 3Sum

### 原题概述

Given an array `nums` of _n_ integers, are there elements _a_, _b_, _c_ in `nums` such that _a_ + _b_ + _c_ = 0? Find all unique triplets in the array which gives the sum of zero.

**Note:**

The solution set must not contain duplicate triplets.

**Example:**

```text
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

### 题意和分析

这个题的意思是在Array中找到三个数加起来为0， 用1-2Sum的双指针的方法来解，先对Array排序，然后拿出第一个index从左到右循环，第二第三个index分别向右向左移动来确定triplet；找到triplet后还需要考虑去除重复triplets的问题，这里直接用了hashset来判断triplet-ArrayList&lt;Integer&gt;是否相同，也可以存到map里面，判断一下如果有相同的key就不存进去。

时间复杂度: O\(nlogn\) + O\(n^2\)  = O\(n^2\)；空间复杂度O\(n\)。

### 代码

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        ArrayList<List<Integer>> result = new ArrayList<List<Integer>>(); //保持最终结果
        HashSet<ArrayList<Integer>> noDuplicateTriplets = new HashSet<ArrayList<Integer>>();//保持中间找到的triplet不要是重复的
        
        if (nums == null || nums.length < 3) {
            return result;
        }
        
        Arrays.sort(nums);
        
        for (int first = 0; first <= nums.length - 3; first++) {
            int second = first + 1, third = nums.length - 1;
            
            //接下来需要确定第二个指针second和第三个指针third是否找到合适的triplet或者相遇
            while (second < third) {
                int sum = nums[first] + nums[second] + nums[third];
                if (sum < 0) {
                    second++;
                } else if (sum > 0) {
                    third--;
                } else { //找到了一个triplet
                    ArrayList<Integer> oneTriplet = new ArrayList<Integer>();
                    oneTriplet.add(nums[first]);
                    oneTriplet.add(nums[second]);
                    oneTriplet.add(nums[third]);
                    
                    if (!noDuplicateTriplets.contains(oneTriplet)) {//因为是排好序的，所以找出来的triplet直接跟之前的所有triplets比较一下看看是否重复
                        //下面这句话也可以作为判断条件
                        noDuplicateTriplets.add(oneTriplet);//没有重复就加到hashset里面为了接下来比较下一个可能的triplet比较
                        result.add(oneTriplet);//没有重复同时也加到结果里面
                    }
                    
                    //注意这里second和third是同时移动，因为排过序了后找到的是已经等于target了，所以只移动一个index的话是不会再找到非重复的triplet的
                    second++;
                    third--;
                }
            }
        }
        return result;
    }
}
```

## 16 - 3Sum Closest

### 原题概述

Given an array `nums` of _n_ integers and an integer `target`, find three integers in `nums` such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution.

**Example:**

```text
Given array nums = [-1, 2, 1, -4], and target = 1.

The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

### 题意和分析

找到数组中的一个triplet三个数的和距离target的差值相比其它triplets来说是最小的，并返回这个triplet的和，因为不是返回所有可能的triplets，所以不需要去重了。这是上一道题的延伸，思路类似，先排序，然后确定一个index，剩下的两个indices两头扫描。

 同样，时间复杂度: O\(nlogn\) + O\(n^2\) = O\(n^2\)；空间复杂度O\(n\)。

### 代码

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        if (nums == null || nums.length < 3) {
            return 0;
        }
        
        int result = 0; 
        int min = Integer.MAX_VALUE;
        
        Arrays.sort(nums);
        
        for (int first = 0; first <= nums.length - 3; first++) {
            int second = first + 1, third = nums.length - 1;
            while (second < third) {//循环一轮找到最小的min
                int sum = nums[first] + nums[second] + nums[third];
                if (Math.abs(sum - target) < min) {
                    min = Math.abs(sum - target);
                    result = sum;
                }
                
                if (sum == target) {//等于target那就是差距最小了，直接返回
                    return sum;
                } else if (sum < target) {
                    second++;
                } else {
                    third--;
                }
            }
        }
        return result;
    }
}
```

## 259 - 3Sum Smaller

### 原题概述

Given an array of n integers nums and a target, find the number of index triplets `i, j, k` with `0 <= i < j < k < n` that satisfy the condition `nums[i] + nums[j] + nums[k] < target`.

**Example:**

```text
Input: nums = [-2,0,1,3], and target = 2
Output: 2 
Explanation: Because there are two triplets which sums are less than 2:
             [-2,0,1]
             [-2,0,3]
```

**Follow up:** Could you solve it in O\(n2\) runtime?

### 题意和分析

这个题完全是3Sum的略微变化，维持一个变量counter记录排序后小于target的triplets的数量即可。

Time: O\(nlogn\) + O\(n^2\) = O\(n^2\); Space: O\(1\)

### 代码

```java
class Solution {
    public int threeSumSmaller(int[] nums, int target) {
        if (nums == null || nums.length < 3) {
            return 0;
        }
        
        int count = 0; 
        Arrays.sort(nums);//因为只用返回小于target的triplets的个数，所以顺序没关系，不用单独记住原数组的indices
        
        for (int first = 0; first <= nums.length - 3; first++) {
            int second = first + 1, third = nums.length - 1;
            while (second < third) {
               int sum = nums[first] + nums[second] + nums[third];
                if (sum < target) {
                    count += third - second;//这里的意思是排序后只要三个数相加小于target，那么third之前的数肯定也和first，second相加小于target
                    second++;//上面加完后，右移第二个index再进行同样的确认；如果second后面的数是一样的呢，可否优化？
                } else {
                    third--;//大于或等于的话，把第三个index左移，缩小范围；如果third前面的数是一样的呢，可否优化？
                }
            }
        }
        return count;
    }
}
```

## 18 - 4Sum

### 原题概述

Given an array `nums` of _n_ integers and an integer `target`, are there elements _a_, _b_, _c_, and _d_ in `nums` such that _a_ + _b_ + _c_ + _d_ = `target`? Find all unique quadruplets in the array which gives the sum of `target`.

**Note:**

The solution set must not contain duplicate quadruplets.

**Example:**

```text
Given array nums = [1, 0, -1, 0, -2, 2], and target = 0.

A solution set is:
[
  [-1,  0, 0, 1],
  [-2, -1, 1, 2],
  [-2,  0, 0, 2]
]
```

### 题意和分析

找到Quadruplets的四个数，加起来等于target的值，思路跟3Sum一样，再在外面套一层循环，最后的解决方案不能有重复，所以依然得去重。

时间复杂度: O\(nlogn\) + O\(n^3\)  = O\(n^3\)；空间复杂度O\(n\)。

### 代码

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        ArrayList<List<Integer>> result = new ArrayList<List<Integer>>();
        HashSet<ArrayList<Integer>> noDuplicateQuad = new HashSet<ArrayList<Integer>>();
        
        Arrays.sort(nums);
        
        for (int first = 0; first <= nums.length - 4; first++) {
            for (int second = first + 1; second <= nums.length - 3; second++) {
                int third = second + 1;
                int fourth = nums.length - 1;
                
                while (third < fourth) {
                    int sum = nums[first] + nums[second] + nums[third] + nums[fourth];
                    if (sum < target) {
                        third++;
                    } else if (sum > target) {
                        fourth--;
                    } else { //找到了一个合适的Quadruplet
                        ArrayList<Integer> oneQuadruplet = new ArrayList<>();
                        oneQuadruplet.add(nums[first]);
                        oneQuadruplet.add(nums[second]);
                        oneQuadruplet.add(nums[third]);
                        oneQuadruplet.add(nums[fourth]);
                        
                        if (!noDuplicateQuad.contains(oneQuadruplet)) {
                            noDuplicateQuad.add(oneQuadruplet);
                            result.add(oneQuadruplet);
                        }
                        
                        //这里是两个indices同时移动，因为排过序了后找到的是已经等于target了，所以只移动一个index的话是不会再找到非重复的Quadruplet的
                        third++;
                        fourth--;
                    }
                }
            }
        }
        return result;
    }
}
```

