# Hash Table

## 771 Jewels and Stones

### 原题概述

You're given strings `J` representing the types of stones that are jewels, and `S` representing the stones you have.  Each character in `S`is a type of stone you have.  You want to know how many of the stones you have are also jewels.

The letters in `J` are guaranteed distinct, and all characters in `J` and `S` are letters. Letters are case sensitive, so `"a"` is considered a different type of stone from `"A"`.

**Example 1:**

```text
Input: J = "aA", S = "aAAbbbb"
Output: 3
```

**Example 2:**

```text
Input: J = "z", S = "ZZ"
Output: 0
```

**Note:**

* `S` and `J` will consist of letters and have length at most 50.
* The characters in `J` are distinct.

### 题意和分析

这道题J中的字符全是珠宝分类，没有重复，S中的字符是石头或者珠宝，找出S中所有是珠宝的字符。暴力搜索就是把J中字符分别拿到S中去对比下，match一次就+1；decent一点的做法则是用hashset把J中的字符装入（因为没重复），然后把S中的字符分别来看是否存在。

### 代码

```java
class Solution {
    public int numJewelsInStones(String J, String S) {
        int result = 0;
        Set<Character> jset = new HashSet<>();
        for (char j : J.toCharArray()) {
            jset.add(j);
        }
        for (char s : S.toCharArray()) {
            if (jset.contains(s)) {
                result++;
            }
        }
        return result;
    }
}
```

## 535 Encode and Decode TinyURL

### 原题概述

> Note: This is a companion problem to the [System Design](https://leetcode.com/discuss/interview-question/system-design/) problem: [Design TinyURL](https://leetcode.com/discuss/interview-question/124658/Design-a-URL-Shortener-%28-TinyURL-%29-System/).

TinyURL is a URL shortening service where you enter a URL such as `https://leetcode.com/problems/design-tinyurl` and it returns a short URL such as `http://tinyurl.com/4e9iAk`.

Design the `encode` and `decode` methods for the TinyURL service. There is no restriction on how your encode/decode algorithm should work. You just need to ensure that a URL can be encoded to a tiny URL and the tiny URL can be decoded to the original URL.

### 题意和分析

最简单的一种编码就是用个计数器，当前是第几个存入的url就编码成几，然后解码的时候也能根据数字来找到原来的url，题目中推荐用六位随机数。短链接的设计参考[这里](https://leetcode.com/problems/encode-and-decode-tinyurl/discuss/100276/Easy-solution-in-java-5-line-code.)。

### 代码

```java
public class Codec {
    List<String> urls = new ArrayList<>();

    // Encodes a URL to a shortened URL.
    public String encode(String longUrl) {
        urls.add(longUrl);
        return String.valueOf(urls.size() - 1);
    }

    // Decodes a shortened URL to its original URL.
    public String decode(String shortUrl) {
        int index = Integer.valueOf(shortUrl);
        return (index < urls.size()) ? urls.get(index) : "";
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.decode(codec.encode(url));
```

## 202 Happy Number

### 原题概述

Write an algorithm to determine if a number is "happy".

A happy number is a number defined by the following process: Starting with any positive integer, replace the number by the sum of the squares of its digits, and repeat the process until the number equals 1 \(where it will stay\), or it loops endlessly in a cycle which does not include 1. Those numbers for which this process ends in 1 are happy numbers.

**Example:** 

```text
Input: 19
Output: true
Explanation: 
1^2 + 9^2 = 82
8^2 + 22 = 68
6^2 + 8^2 = 100
1^2 + 0^2 + 0^2 = 1
```

### 题意和分析

这道题定义了happy number，把一个数的所有位数来进行平方再相加，如果出现结果1，就是happy number，如果一直循环没有出现1就不是。可以用一个hashset来保存所有出现的结果，每次的结果检查一下之前是否出现过，如果有说明会有循环，返回false；

另外一个办法是，如果不是happy number，则平方再相加后的结果一定会出现4（），只要检查是否为4就可以知道是否是happy number。

### 代码

HashSet

```text
class Solution {
    public boolean isHappy(int n) {
        if (n <= 0) {
            return false;
        }
        Set<Integer> sums = new HashSet<>();
        while (n != 1) {
            int temp = 0;
            while (n != 0) {
                temp += Math.pow(n % 10, 2);
                n /= 10;
            }
            n = temp;//把新计算的数赋值给n
            if (sums.contains(n)) {
                break;
            } else {
                sums.add(temp);
            }
        }
        return n == 1;
    }
}
```

检查是否有4

```java
class Solution {
    public boolean isHappy(int n) {
        if (n <= 0) {
            return false;
        }
        while (n != 1 && n != 4) {//是happy number一定会出现1，不是则一定会出现4
            int temp = 0;
            while (n != 0) {
                temp += Math.pow(n % 10, 2);
                n /= 10;
            }
            n = temp;
        }
        return n == 1;
    }
}
```

## 500 - Keyboard Row

### 原题概述

Given a List of words, return the words that can be typed using letters of **alphabet** on only one row's of American keyboard like the image below.  


![American keyboard](https://leetcode.com/static/images/problemset/keyboard.png)

**Example 1:**  


```text
Input: ["Hello", "Alaska", "Dad", "Peace"]
Output: ["Alaska", "Dad"]
```

**Note:**  


1. You may use one character in the keyboard more than once.
2. You may assume the input string will only contain letters of alphabet.

### 题意和分析

检查一个字符串数组里面的所有字符串，返回一些字符串，这些字符串只用键盘中的一行就可以写出来。可以用regex或者hashmap。把在同row的char标记成同index存在HashMap中；然后判定word中的每一个char是否有相同的index；Time Complexity: O\(n\), n是words中每个word的char总和.

### 代码

```java
class Solution {
    public String[] findWords(String[] words) {
        if (words == null || words.length == 0) {
            return words;
        }
        String[] keyboard = {"qwertyuiop","asdfghjkl","zxcvbnm"};
        HashMap<Character, Integer> hm = new HashMap<>();
        for (int i = 0; i < keyboard.length; i++) {
            for (char c : keyboard[i].toCharArray()) {
                hm.put(c, i);//每一列的i不一样0,1,2
            }
        }
        List<String> result = new ArrayList<>();
        for (String word : words) {
            if (word == "") {
                continue;
            }
            int index = hm.get(word.toLowerCase().charAt(0));//查一下第一个字符的index
            //检查字符串里面所有字符的index
            for (char c : word.toLowerCase().toCharArray()) {
                if (index != hm.get(c)) {//如果有字符的index和第一个字符不相同
                    index = -1;
                }
            }
            if (index != -1) {
                result.add(word);
            }
        }
        return result.toArray(new String[0]);//new String[0]确定是字符串数组
    }
}
```

## 463 - Island Perimeter

### 原题概述

You are given a map in form of a two-dimensional integer grid where 1 represents land and 0 represents water. Grid cells are connected horizontally/vertically \(not diagonally\). The grid is completely surrounded by water, and there is exactly one island \(i.e., one or more connected land cells\). The island doesn't have "lakes" \(water inside that isn't connected to the water around the island\). One cell is a square with side length 1. The grid is rectangular, width and height don't exceed 100. Determine the perimeter of the island.

**Example:**

```text
[[0,1,0,0],
 [1,1,1,0],
 [0,1,0,0],
 [1,1,0,0]]

Answer: 16
Explanation: The perimeter is the 16 yellow stripes in the image below:

```

![](../../.gitbook/assets/image%20%2834%29.png)

### 题意和分析

给一个矩形，里面的小格子是正方形，1代表陆地，0代表水，整个矩形边不超过100，没有湖，需要求出陆地周长。对整个二维数组遍历，每当遍历到一个陆地1时，同时判断下这块陆地的右边和下边看是否也是陆地，如果是就说明有neighbors，最后周长计算为land \* 4 - neighbors \* 2。时间O\(m \* n\)，空间O\(1\)。

### 代码

```java
class Solution {
    public int islandPerimeter(int[][] grid) {
        int lands = 0, neighbors = 0;
        for (int i = 0; i < grid.length; i++) {
            for (int j = 0; j < grid[0].length; j++) {
                if (grid[i][j] == 1) {
                    lands++;
                    if (i < grid.length - 1 && grid[i+1][j] == 1) {//下方是陆地
                        neighbors++;
                    }
                    if (j < grid[0].length - 1 && grid[i][j+1] == 1) {//右边是陆地
                        neighbors++;
                    }
                }
            }
        }
        return lands * 4 - neighbors * 2;
    }
}
```

## 389 - Find the Difference

### 原题概述

Given two strings **s** and **t** which consist of only lowercase letters.

String **t** is generated by random shuffling string **s** and then add one more letter at a random position.

Find the letter that was added in **t**.

**Example:**

```text
Input:
s = "abcd"
t = "abcde"

Output:
e

Explanation:
'e' is the letter that was added.
```

### 题意和分析

给一个字符串s，根据s把里面的字符都shuffle一下然后随机加一个字符然后生产t，找到刚才随机加上的字符。

首先位操作的XOR可以去掉重复，因为自己和自己异或等于0；另外也可以计算两个字符串char codes的差距，然后转换成char即可，时间O\(n\)，空间O\(1\)。

> char b = 'a'+18; //因为char本身在码表中可以用数字表示的，然后运算玩之后还是char，应该输出s 
>
> char a = 'a'; //这个定义就是错误的。 
>
> char b = a + 18; // 这样也是错的，因为JVM运算完后不知道结果是多少，所以会提示损失精度的错误，关于一个类型变量值的问题。

### 代码

XOR

```java
class Solution {
    public char findTheDifference(String s, String t) {
        int len = t.length();
        char ch = t.charAt(len - 1);//初始化，注意长字符串t最后多出来的那位为初始值，因为下面的循环要保证每个元素都遍历到
        for (int i = 0; i < len - 1; i++) {//i < len -1，短的字符串不越界
            ch ^= s.charAt(i);
            ch ^= t.charAt(i);
        }
        return ch;
    }
}
```

XOR更好理解的做法，需要一点额外空间，两个串合在一起然后XOR，最后剩余的就是多出来的字符

```java
class Solution {
    public char findTheDifference(String s, String t) {
        String merge = s + t;
        char ch = 0;//初始化，这里表示ch的初始值为空格' '，P.S.，用ch != '\0'判断字符是否为空格
        for (int i = 0; i < merge.length(); i++) {
            ch ^= merge.charAt(i);//等于 ch = (char) (ch ^ merge.charAt(i));
        }
        return ch;
    }
}
```

int和char转换的做法

```java
class Solution {
    public char findTheDifference(String s, String t) {
        int len = s.length();
        int charCode = t.charAt(len);//初始化为长的字符串最后一位，因为下面的循环要保证每个元素都遍历到
        for (int i = 0; i < len; i++) {
            charCode -= s.charAt(i);
            charCode += t.charAt(i);
        }
        return (char)charCode;
    }
}
```

## 347 - Top K Frequent Elements

### 原题概述

Given a non-empty array of integers, return the **k** most frequent elements.

For example,  
Given `[1,1,1,2,2,3]` and k = 2, return `[1,2]`.

**Note:**   


* You may assume k is always valid, 1 ≤ k ≤ number of unique elements.
* Your algorithm's time complexity **must be** better than O\(n log n\), where n is the array's size.

### 题意和分析

高频题，给一个非空的整数数组和一个总是有效的的整数k，返回前k个最多出现的元素。可以用HashMap来做桶排序，TreeMap和最大堆来做。

### 代码

用最大堆，把数字和其频率放到HashMap中，然后把map的entry放到maxHeap中，从最顶上拿走k个数字的entry然后相对应的keys就是最高频率的k个数字。

```java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);//原数组的元素为key，出现次数为value
        }

        //利用最大堆
        PriorityQueue<Map.Entry<Integer, Integer>> maxHeap = new PriorityQueue<>((a,b)->(b.getValue() - a.getValue()));
        for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
            maxHeap.add(entry);
        }

        List<Integer> result = new ArrayList<>();
        while (result.size() < k) {
            Map.Entry<Integer, Integer> entry = maxHeap.poll();//取出顶部元素并删除
            result.add(entry.getKey());
        }
        return result;
    }
}
```

HashMap桶排序的做法，在建立好数字和其出现次数的映射后利用桶排序取k个最频繁的数字

```java
class Solution {    
    public List<Integer> topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);//原数组的元素为key，出现次数为value
        }

        //创建一个放buckets的list
        List<Integer>[] bucket = new List[nums.length + 1];//如果只有一个元素，那么也需要一个index为1的bucket
        for (int num : map.keySet()) {
            int freq = map.get(num);
            if (bucket[freq] == null) {//没有这个桶，就加一个
                bucket[freq] = new LinkedList<>();
            }
            bucket[freq].add(num);//在每个桶加上各自的元素
        }

        List<Integer> result = new LinkedList<>();
        for (int i = bucket.length - 1; i > 0 && k > 0; i--) {//从做到右取n个
            if (bucket[i] != null) {//只要出现过
                List<Integer> list = bucket[i];
                result.addAll(list);
                k -= list.size();
            }
        }
        return result;
    }
}
```

利用TreeMap自动排序

```java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int num : nums) {
            map.put(num, map.getOrDefault(num, 0) + 1);//原数组的元素为key，出现次数为value
        }

        //利用treeMap自动排序
        TreeMap<Integer, List<Integer>> freqMap = new TreeMap<>();
        for (int num : map.keySet()) {
            int freq = map.get(num);
            if (!freqMap.containsKey(freq)) {
                freqMap.put(freq, new LinkedList<>());
            }
            freqMap.get(freq).add(num);
        }

        List<Integer> result = new ArrayList<>();
        while (result.size() < k) {//取前k位
            Map.Entry<Integer, List<Integer>> entry = freqMap.pollLastEntry();
            result.addAll(entry.getValue());
        }
        return result;
    }
}
```

## 349 - Intersection of Two Arrays

### 原题概述

Given two arrays, write a function to compute their intersection.

**Example:**  
Given nums1 = `[1, 2, 2, 1]`, nums2 = `[2, 2]`, return `[2]`.

**Note:**  


* Each element in the result must be unique.
* The result can be in any order.

### 题意和分析

有两个数组，找到他们交叉的部分（元素的个数），结果中的元素都是unique的，无关顺序。用两个set，第一个set存第一个数组的元素，然后第二个数组来检查是否有相同的元素，有的话就把这些元素存入到第二个set中，然后遍历第二个set将其中的元素作为整数数组返回，时间O\(n\)，空间O\(n\)。

### 代码

两个set

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        Set<Integer> intersect = new HashSet<>();
        for (int i = 0; i < nums1.length; i++) {//把nums1的元素存储到第一个set中
            set1.add(nums1[i]);
        }
        for (int i = 0; i < nums2.length; i++) {//检查nums2的元素是否在第一个set中存在，存在就加入到第二个set中
            if (set1.contains(nums2[i])) {//有交叉
                intersect.add(nums2[i]);
            }
        }
        int[] result = new int[intersect.size()];//遍历把第二个set中的元素存到结果的array中
        int i = 0;
        for (int num : intersect) {
            result[i] = num;
            i++;
        }
        return result;
    }
}
```

双指针做法，只用一个set，将两个数组排序后用个指针分别判断大小，因为要排序，时间O\(nlogn\)，空间O\(n\)。

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        Set<Integer> set = new HashSet<>();
        Arrays.sort(nums1);
        Arrays.sort(nums2);

        int i = 0, j = 0;//双指针
        while (i < nums1.length && j < nums2.length) {
            if (nums1[i] < nums2[j]) {
                i++;
            } else if (nums1[i] > nums2[j]) {
                j++;
            } else {//相等
                set.add(nums1[i]);
                i++;
                j++;
            }
        }

        //将set中的元素存入到整数数组中
        int k = 0;
        int[] result = new int[set.size()];
        for (int num : set) {
            result[k] = num;
            k++;
        }
        return result;
    }
}
```

类似的方法，也可以用二分查找，将一个数组排序，然后用二分查找来遍历另外一个数组，看是否在第一个数组中存在，时间O\(nlogn\)，空间\(n\)。

```java
class Solution {
    public int[] intersection(int[] nums1, int[] nums2) {
        if (nums1 == null || nums1.length == 0 || nums2 == null || nums2.length == 0) {
            return new int[0];
        }
        Set<Integer> set = new HashSet<>();
        Arrays.sort(nums1);

        for (int i = 0; i < nums2.length; i++) {
            if (binarySearch(nums1, nums2[i])) {
                set.add(nums2[i]);
            }
        }
        int[] result = new int[set.size()];
        int k = 0;
        for (int num : set) {
            result[k] = num;
            k++;
        }
        return result;
    }

    private boolean binarySearch(int[] nums, int n) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {//注意是地板除法，所以等于也是包含的，比如一个数的时候
            int mid = left + (right - left) / 2;
            if (nums[mid] < n) {//小于就不用再检查mid了，所以是mid+1
                left = mid + 1;
            } else if (nums[mid] > n) {//大于就不用再检查mid了，所以是mid-1
                right = mid - 1;
            } else {//等于
                return true;
            }
        }
        return false;
    }
}
```

## **350 Intersection of Two Arrays II**

### **原题概述**

Given two arrays, write a function to compute their intersection.

**Example 1:**

```text
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2,2]
```

**Example 2:**

```text
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [4,9]
```

**Note:**

* Each element in the result should appear as many times as it shows in both arrays.
* The result can be in any order.

**Follow up:**

* What if the given array is already sorted? How would you optimize your algorithm?
* What if nums1's size is small compared to nums2's size? Which algorithm is better?
* What if elements of nums2 are stored on disk, and the memory is limited such that you cannot load all elements into the memory at once?

### **题意和分析**

跟上一道349题比起来，可以返回重复的数字，并且尽可能多地返回。可以用HashMap来建立nums1中元素和出现个数的映射，然后遍历nums2，如果nums2中当前字符在nums1建立的哈希表中的个数大于0，将此字符加入到result中，相应的出现次数减去1，以防止重复计算。

另外一种做法是先将两个数组排序，然后用两个指针分别指向两个数组的起始位置，如果两个指针指向的数字相等，将该数字存入结果，两个指针均右移一位；如果第一个指针指向的数字大，第二个指针右移一位，反之亦然。

### **代码**

HashMap

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        HashMap<Integer, Integer> map = new HashMap<>();
        List<Integer> result = new ArrayList<>();

        //将第一个数组建立映射
        for (int i : nums1) {
            int count1 = map.getOrDefault(i, 0) + 1;
            map.put(i, count1);
        }
        //遍历第二个数组
        for (int i : nums2) {
            int count2 = map.getOrDefault(i, 0);
            if (count2 > 0) {
                result.add(i);
                count2--;
            }
            map.put(i, count2);
        }
        //Java8 ArrayList转换为Array
        return result.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

两个指针

```java
class Solution {
    public int[] intersect(int[] nums1, int[] nums2) {
        Arrays.sort(nums1);
        Arrays.sort(nums2);

        List<Integer> result = new ArrayList<>();

        int idx1 = 0, idx2 = 0;
        while (idx1 < nums1.length && idx2 < nums2.length) {
            if (nums1[idx1] == nums2[idx2]) {
                result.add(nums1[idx1]);
                idx1++;
                idx2++;
            } else if (nums1[idx1] < nums2[idx2]) {
                idx1++;
            } else {
                idx2++;
            }
        }
        return result.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

## 242 - Valid Anagram

### 原题概述

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

### 题意和分析

给两个字符串，判断二者是否打乱顺序组成的Anagram。这道题如果先排序再比较是否一样，复杂度为O\(nlogn\)；如果用XOR来消除的话，又不能排除aa和bb这样也能等于0的情况。因为字符可以用数字来表示，所以一个loop分别加减二者的所有字符的值，看是否有的字符为不为0即可，时间O\(n\)，空间创建了两个数组，O\(n\)。

> ASCII, Unicode与UTF-8

### 代码

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s == null && t == null) {
            return true;
        } else if (s == null || t == null) {
            return false;
        } else if (s.length() != t.length()) {
            return false;
        }

        char[] char1 = s.toCharArray();
        char[] char2 = t.toCharArray();

        int[] alphabet = new int[26];//因为题目说了都是小写字母，所以最多26个
        for (int i = 0; i < char1.length; i++) {
            //在数组的特定的位置进行加减
            alphabet[char1[i] - 'a']++;
            alphabet[char2[i] - 'a']--;
        }

        for (int num : alphabet) {
            if (num != 0) {//只要找到一个部位0的表示至少有一个不相同的字符
                return false;
            }
        }
        return true;
    }
}
```

题目有追问，如果字符串中有unicode应该怎么调整，在Java中，Unicode 可以用一个字符来表示\(BMP, Basic Multilingual Plane\)，也可以用两个字符来表示\(high surrogate\)。我们可以用`String.codePointAt(int index)`这个方法来得到Unicode的整数表示法，其中index是哈希表中的key，另外可以用`Character.charCount(int code)`来得到在该位置有多少个characters被用到，从而正确地对index进行递增

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s == null && t == null) {
            return true;
        } else if (s == null || t == null) {
            return false;
        } else if (s.length() != t.length()) {
            return false;
        }

        Map<Integer, Integer> dict = new HashMap<>();//创建一个map来检查两个字符串的字符
        int index = 0;
        while (index < s.length()) {
            int charCode = s.codePointAt(index);//得到Unicode的整数的表达形式
            dict.put(charCode, dict.getOrDefault(charCode, 0) + 1);
            index += Character.charCount(charCode);//Unicode可以有一个或者两个字符的表达形式
        }
        index = 0;
        while (index < t.length()) {
            int charCode = t.codePointAt(index);
            int count = dict.getOrDefault(charCode, 0);//看看是否有同样的字符

            if (count == 0) {
                return false;
            } else {
                dict.put(charCode, count - 1);
            }

            index += Character.charCount(charCode);
        }
        return true;
    }
}
```

Java8开始有新的特性，可以用`Charsequence.copdpoints()`来简化代码

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s == null && t == null) {
            return true;
        } else if (s == null || t == null) {
            return false;
        } else if (s.length() != t.length()) {
            return false;
        }

        Map<Integer, Integer> dict = new HashMap<>();//创建一个map来检查两个字符串的字符
        s.codePoints().forEach(code -> dict.put(code, dict.getOrDefault(code, 0) + 1));
        t.codePoints().forEach(code -> dict.put(code, dict.getOrDefault(code, 0) - 1));

        for (int count : dict.values()) {
            if (count < 0) {
                return false;
            }
        }
        return true;
    }
}
```

## \*447 - Number of Boomerangs

### 原题概述

Given n points in the plane that are all pairwise distinct, a "boomerang" is a tuple of points `(i, j, k)` such that the distance between `i`and `j` equals the distance between `i` and `k` \(**the order of the tuple matters**\).

Find the number of boomerangs. You may assume that n will be at most **500** and coordinates of points are all in the range **\[-10000, 10000\]** \(inclusive\).

**Example:**  


```text
Input:
[[0,0],[1,0],[2,0]]

Output:
2

Explanation:
The two boomerangs are [[1,0],[0,0],[2,0]] and [[1,0],[2,0],[0,0]]
```

### 题意和分析

定义了一种类似回旋镖形状的三元组结构，要求第一个点和第二个点之间的距离跟第一个点和第三个点之间的距离相等。现在给了n个点，找出回旋镖的个数。那么如果有一个点a，还有两个点b和c，如果ab和ac之间的距离相等，那么就有两种排列方法abc和acb；如果有三个点b，c，d都分别和a之间的距离相等，那么有六种排列方法，abc, acb, acd, adc, abd,adb，那么是怎么算出来的呢，很简单，如果有n个点和a距离相等，那么排列方式为n\(n-1\)，这属于最简单的排列组合问题了。如此就可以遍历所有点，让每个点都做一次点a，然后遍历其他所有点，统计和a距离相等的点有多少个，然后分别带入n\(n-1\)计算结果并累加到result中，只有当n大于等于2时，result值才会真正增加。

时间复杂度O\(n^2\)，空间复杂度O\(n\)。

> 如果定义一个这样的二维数组int a\[3\]\[4\]={{1,3,5,7},{9,11,13,15},{17,19,21,23}};则其在内存中的表示下面这样的。
>
> ![](https://images0.cnblogs.com/blog2015/652582/201503/081344491173409.jpg)
>
> 在内存中二维数组是按照**行主序**进行存储的，从内存的角度上看，二维数组本质就是一个一维数组。如果把二维数组的每一行看成一个整体，即看成一个数组中的一个元素，那么整个二维数组就是一个一维数组。而**二维数组的名字代表二维数组第0行的首地址\(注意它是代表一行元素的首地址，而不是第0行第0列元素的首地址，虽然是相等的，但不能这么理解，所以在没有强制转换的情况下，二维数据要么通过行指针进行参数传递，要么通过二维指针进行参数传递\)**。

### 代码

```java
class Solution {
    public int numberOfBoomerangs(int[][] points) {
        if (points == null || points.length == 0) {
            return 0;
        }

        Map<Integer, Integer> map = new HashMap<>();
        int result = 0;
        for (int i = 0; i < points.length; i++) {
            for (int j = 0; j < points.length; j++) {
                if (i == j) {//自己和自己不用比较
                    continue;
                }
                int distance = getDistance(points[i], points[j]);
                map.put(distance, map.getOrDefault(distance, 0) + 1);//key是distance,value是距这个点相同distance的点的个数
            }

            for (int n : map.values()) {
                result += n * (n - 1);//排列方式为n(n-1)
            }
            map.clear();//清空map，下一轮检查一个新的点
        }
        return result;
    }

    private int getDistance(int[] a, int[] b) {
        int dx = a[0] - b[0];
        int dy = a[1] - b[1];

        return dx*dx + dy*dy;
    }
}
```

## 409 - Longest Palindrome

### 原题概述

Given a string which consists of lowercase or uppercase letters, find the length of the longest palindromes that can be built with those letters.

This is case sensitive, for example `"Aa"` is not considered a palindrome here.

**Note:**  
Assume the length of given string will not exceed 1,010.

**Example:**

```text
Input:
"abccccdd"

Output:
7

Explanation:
One longest palindrome that can be built is "dccaccd", whose length is 7.
```

### 题意和分析

给一个字符串，里面有大小写，字符可以打乱，问这些字符可以组成的最长的回文结构长度是多少。所以问题就转化为了求偶数个字符的个数，我们了解回文串的都知道，回文串主要有两种形式，一个是左右完全对称的，比如noon，还有一种是以中间字符为中心，左右对称，比如bob，level等，那么我们统计出来所有偶数个字符的出现总和，然后如果有总数是奇数个字符的话，我们算出偶数的个数，然后最后结果加1即可。

### 代码

```java
class Solution {
    public int longestPalindrome(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        int count = 0;
        Set<Character> set = new HashSet<>();
        for (int i = 0; i < s.length(); i++) {
            if (set.contains(s.charAt(i))) {
                set.remove(s.charAt(i));//找到成对的字符就删掉
                count++;
            } else {
                set.add(s.charAt(i));
            }
        }
        if (!set.isEmpty()) {//奇数个字符
            return count*2 + 1;
        }
        return count*2;//偶数个字符
    }
}
```

另外`hashset.remove(ch)`这个方法，如果hashset中有ch这个元素的话可以remove并且返回true，所以可以这样写

```java
class Solution {
    public int longestPalindrome(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        Set<Character> set = new HashSet<>();
        int count = 0;
        for (char ch : s.toCharArray()) {
            if (set.remove(ch)) {//如果hashset中有这个字符，可以remove并返回true
                count++;
            } else {
                set.add(ch);
            }
        }
        return set.size() > 0 ? count*2 + 1 : count*2;
    }
}
```

另外，也可以利用int\[26\]来检查有多少个相同的字符

```java
class Solution {
    public int longestPalindrome(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }

        int[] lowercase = new int[26];
        int[] uppercase = new int[26];
        int result = 0;
        for (int i = 0; i < s.length(); i++) {
            int ch = s.charAt(i);
            if (ch >= 97) {//判断大小写
                lowercase[ch - 'a']++;
            } else {
                uppercase[ch - 'A']++;
            }
        }
        for (int i = 0; i < 26; i++) {//遍历大小写两个数组，看相同的字符有多少
            //每两个字符组成一个回文
            result += (lowercase[i] / 2) * 2;
            result += (uppercase[i] / 2) * 2;
        }
        return result == s.length() ? result : result + 1;
    }
}
```



