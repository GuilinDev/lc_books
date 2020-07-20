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

> 上面这种方法虽然简单，但是缺点却很多，首先，如果接受到多次同一url地址，仍然会当做不同的url来处理。当然这个缺点可以通过将vector换成哈希表，每次先查找url是否已经存在。虽然这个缺点可以克服掉，但是由于是用计数器编码，那么当前服务器存了多少url就曝露出来了，也许会有安全隐患。而且计数器编码另一个缺点就是数字会不断的增大，那么编码的长度也就不是确定的了。而题目中明确推荐了使用六位随机字符来编码，那么我们只要在所有大小写字母和数字中随机产生6个字符就可以了，我们用哈希表建立6位字符和url之间的映射，如果随机生成的字符之前已经存在了，我们就继续随机生成新的字符串，直到生成了之前没有的字符串为止。下面的代码中使用了两个哈希表，目的是为了建立六位随机字符串和url之间的相互映射，这样进来大量的相同url时，就不用生成新的随机字符串了。

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

两个HashMap的做法（[https://www.youtube.com/watch?v=T2xk6xv9-rs&t=517s](https://www.youtube.com/watch?v=T2xk6xv9-rs&t=517s)）

```java
public class Codec {
    HashMap<String, String> urlToHash = new HashMap<>();
    HashMap<String, String> hashToUrl = new HashMap<>();

    String characters = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz1234567890";
    String baseUrl = "http://tinyurl.com/";
    Random random = new Random();

    // Encodes a URL to a shortened URL.
    public String encode(String longUrl) {
        if (urlToHash.containsKey(longUrl)) {
            return baseUrl + urlToHash.get(longUrl);
        }
        StringBuilder hash = new StringBuilder();
        do {
            for (int i = 0; i < 6; i++) {
                hash.append(characters.charAt(random.nextInt(characters.length())));
            }
        } while (hashToUrl.containsKey(hash.toString()));
        urlToHash.put(longUrl, hash.toString());
        hashToUrl.put(hash.toString(), longUrl);

        return baseUrl + hash.toString();
    }

    // Decodes a shortened URL to its original URL.
    public String decode(String shortUrl) {
        return hashToUrl.get(shortUrl.substring(baseUrl.length()));
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

```java
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

尾递归

```java
class Solution {
    HashSet<Integer> set;
    public boolean isHappy(int n) {
        if (n <= 0) {
            return false;
        }
        set = new HashSet<>();
        return recursion(set, n);
    }
    private boolean recursion(HashSet<Integer> set, int n) {
        if (n == 1) {
            return true;
        }
        int temp = 0;
        while (n != 0) {
            temp += Math.pow(n % 10, 2);
            n /= 10;
        }
        if (!set.add(temp)) {
            return false;
        }  
        return recursion(set, temp);
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

![](../.gitbook/assets/image%20%2840%29.png)

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

## 36 Valid Sudoku

### 原体概述



Determine if a 9x9 Sudoku board is valid. Only the filled cells need to be validated **according to the following rules**:

1. Each row must contain the digits `1-9` without repetition.
2. Each column must contain the digits `1-9` without repetition.
3. Each of the 9 `3x3` sub-boxes of the grid must contain the digits `1-9` without repetition.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)  
A partially filled sudoku which is valid.

The Sudoku board could be partially filled, where empty cells are filled with the character `'.'`.

**Example 1:**

```text
Input:
[
  ["5","3",".",".","7",".",".",".","."],
  ["6",".",".","1","9","5",".",".","."],
  [".","9","8",".",".",".",".","6","."],
  ["8",".",".",".","6",".",".",".","3"],
  ["4",".",".","8",".","3",".",".","1"],
  ["7",".",".",".","2",".",".",".","6"],
  [".","6",".",".",".",".","2","8","."],
  [".",".",".","4","1","9",".",".","5"],
  [".",".",".",".","8",".",".","7","9"]
]
Output: true
```

**Example 2:**

```text
Input:
[
  ["8","3",".",".","7",".",".",".","."],
  ["6",".",".","1","9","5",".",".","."],
  [".","9","8",".",".",".",".","6","."],
  ["8",".",".",".","6",".",".",".","3"],
  ["4",".",".","8",".","3",".",".","1"],
  ["7",".",".",".","2",".",".",".","6"],
  [".","6",".",".",".",".","2","8","."],
  [".",".",".","4","1","9",".",".","5"],
  [".",".",".",".","8",".",".","7","9"]
]
Output: false
Explanation: Same as Example 1, except with the 5 in the top left corner being 
    modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.
```

**Note:**

* A Sudoku board \(partially filled\) could be valid but is not necessarily solvable.
* Only the filled cells need to be validated according to the mentioned rules.
* The given board contain only digits `1-9` and the character `'.'`.
* The given board size is always `9x9`.

### 题意和分析

一个数独表里面已经填了一些数字，没填数字的cell用'.'表示，检查目前这个数独表是否有效。检查的方式是是行，列和3X3的小方块，对二维数组进行遍历，遍历每一行的时候，新建3个HashSet，来检查该行，该行/该列和该cube对应的set是否含有数字，注意，HashSet的add\(\)方法是有返回值的。

### 代码

```java
class Solution {
    public boolean isValidSudoku(char[][] board) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return false;
        }
        int m = board.length, n = board[0].length;
        for (int i = 0; i < m; i++) {
            Set<Character> row = new HashSet<>();
            Set<Character> col = new HashSet<>();
            Set<Character> cube = new HashSet<>();
            for (int j = 0; j < n; j++) {
                //判断数字是否不在1～9范围之内
                if (board[i][j] != '.' && (board[i][j] < '1' || board[i][j] > '9')) {
                    return false;
                }
                //check row
                if (board[i][j] != '.' && !row.add(board[i][j])) {
                    return false;
                }
                //check col
                if (board[j][i] != '.' && !col.add(board[j][i])) {
                    return false;
                }
                //check the cube;
                if (board[3*(i/3) + j/3][3*(i%3) + j%3] != '.' && !cube.add(board[3*(i/3) + j/3][3*(i%3) + j%3])) {
                    return false;
                }
            }
        }
        return true;
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

## 760 Find Anagram Mappings

### 原体概述

Given two lists `A`and `B`, and `B` is an anagram of `A`. `B` is an anagram of `A` means `B` is made by randomizing the order of the elements in `A`.

We want to find an index mapping `P`, from `A` to `B`. A mapping `P[i] = j` means the `i`th element in `A` appears in `B` at index `j`.

These lists `A` and `B` may contain duplicates. If there are multiple answers, output any of them.

For example, given

```text
A = [12, 28, 46, 32, 50]
B = [50, 12, 32, 46, 28]
```

We should return

```text
[1, 4, 3, 2, 0]
```

as `P[0] = 1` because the `0`th element of `A` appears at `B[1]`, and `P[1] = 4` because the `1`st element of `A` appears at `B[4]`, and so on.

**Note:**

1. `A, B` have equal lengths in range `[1, 100]`.
2. `A[i], B[i]` are integers in range `[0, 10^5]`.

### 题意和分析

给两个整型数组，它们之间是anagrams，找出第一个数组中的元素在第二个数组出现的索引的位置。暴力搜索就是n^2，如果先把数组B的元素放入到一个hashmap里面，元素值为key，索引为value，然后对A的每个元素进行寻找，因为HashMap的get\(\)方法是在hashcode不碰撞的情况下是O\(1\)的，\(如果最坏情况全部元素都是碰撞的，那就退化成线性时间复杂度\)，所以整个时间复杂度是O\(2n\)；

另外这道题并没有说数组中的元素是否可以是重复的，如果用HashMap来解，会出现key相同（value索引值不同）的情况，我们不希望遇到同样值的情况下只取到同一个索引值的情况，需要做特殊处理。

### 代码

不考虑重复值的简单解法，也可以AC

```java
class Solution {
    public int[] anagramMappings(int[] A, int[] B) {
        if (A == null || B == null || A.length == 0 || B.length == 0) {
            return new int[0];
        }
        Map<Integer, Integer> map = new HashMap<>();
        int[] result = new int[A.length];
        //把数组B中元素放入到map中
        for (int i = 0; i < B.length; i++) {
            map.put(B[i], i);
        }

        //遍历A数组
        for (int i = 0; i < A.length; i++) {
            result[i] = map.get(A[i]);
        }
        return result;
    }
}
```

考虑重复值

```java
class Solution {
    public int[] anagramMappings(int[] A, int[] B) {
        if (A == null || B == null || A.length == 0 || B.length == 0) {
            return new int[0];
        }
        Map<Integer, List<Integer>> map = new HashMap<>();
        int[] result = new int[A.length];
        //把数组B中元素放入到map中
        for (int i = 0; i < B.length; i++) {
            map.computeIfAbsent(B[i], k -> new ArrayList<>()).add(i);//如果map里没有B[i]这个key，那么就按照后面的这个function添加对应的key和value，如果有这个key，那么就不添加，这个方法避免了hashcode值碰撞
        }

        //遍历A数组
        for (int i = 0; i < A.length; i++) {
            result[i] = map.get(A[i]).remove(map.get(A[i]).size() - 1);//找到第一个key后加入到result中并从map中删除
        }
        return result;
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

## 336 Palindrome Pairs

### 原体概述

Given a list of **unique** words, find all pairs of **distinct** indices `(i, j)` in the given list, so that the concatenation of the two words, i.e. `words[i] + words[j]` is a palindrome.

**Example 1:**

```text
Input: ["abcd","dcba","lls","s","sssll"]
Output: [[0,1],[1,0],[3,2],[2,4]] 
Explanation: The palindromes are ["dcbaabcd","abcddcba","slls","llssssll"]
```

**Example 2:**

```text
Input: ["bat","tab","cat"]
Output: [[0,1],[1,0]] 
Explanation: The palindromes are ["battab","tabbat"]
```

### 题意和分析

给了一个字符串数组，要求找到所有能组合成回文的两个词；暴力解法过不了；用了字典树Trie的结构，参考[这里](https://leetcode.com/problems/palindrome-pairs/discuss/79195/O%28n-*-k2%29-java-solution-with-Trie-structure)，暴力解法就是找出所有pairs，逐一验证isPalindrome. 时间复杂度O\(N^2\*k\)。

palindrome最大的特性就是对称，按长度的奇偶可以分为str，char，reverse\(str\)和str，reverse\(str\)型。 我们有一个str，在i这个位置进行切分，得到的前半部分是一个palindrome。比如"lls", 变成"ll"， "s"。 已知"ll"是palindrome，我们只需要知道reverse\("s"\) 放到前边就可以了。 reverse\("s"\)"ll""s", 即reverse\(str\) PalindromeSubString str的类型。

还有一种就是后半部分是palindrome, 我们找到前半部分的reverse,拼到后面。\["abcdc", "ba"\]。 "cdc"是palindrome, reverse\(ab\) 就是 "ba", 我们有这样的string出现过。

代码细节就是有一个isPalindrome的helper function。 一个hashmap存入所有 paris加速查询。 容易出bug的两个地方, 以\["abcd", "dcba", "lls", "s", "sssll"\]为例。 1. 如果一个str本身就是panlindrome，reverse就是本身，一定在hashmap里，去重的方法就是判断map.get\(reverse\(str\)\) != i. \[\[1,0\],\[0,1\],\[3,2\],\[3,3\],\[2,4\]\] 2. 我们在切割str的时候，j ==0时str变成""和"str", j == str.length\(\)的时候str变成"str"和""。 "abcd", "dcba"这两个互为reverse的string, 在"abcd"尾部加上"dcba"也就是在"dcba"头部加上"abcd". 所以后部分不能为空，否则就和头部为空的情况重复了。 \[\[1,0\],\[0,1\],\[0,1\],\[1,0\],\[3,2\],\[2,4\]\]

空间复杂度因为用了额外的hashmap来储存，需要O\(N\)空间。 时间复杂度每个str分为两个部分，调用isPalindrome,前后两部分总长度为k. 所以每次调用为O\(k\)一共\(k+1\)次。 然后一共有N个str, 总共时间复杂度为O\(N\*k^2\).

### 代码

```java
class Solution {
    public List<List<Integer>> palindromePairs(String[] words) {
        List<List<Integer>> result = new ArrayList<>();
        if (words == null || words.length == 0) {
            return result;
        }
        HashMap<String, Integer> map = new HashMap<>();
        for (int i = 0; i < words.length; i++) {//将单词和对应的索引在map中映射
            map.put(words[i], i);
        }

        for (int i = 0; i < words.length; i++) {//遍历所有单词
            for (int j = 0; j <= words[i].length(); j++) {
                //切割每个单词成两部分，只要其中一部分是回文串，而且另一部分翻转后能够在map中找到，说明就可以组成回文对
                String str1 = words[i].substring(0, j);//单词left
                String str2 = words[i].substring(j);//单词right

                if (isPalindrome(str1)) {
                    String str2rvs = new StringBuilder(str2).reverse().toString();//翻转单词，准备下一步查map
                    if (map.containsKey(str2rvs) && map.get(str2rvs) != i) {//包含翻转后的单词并且不是单词本身
                        List<Integer> list = new ArrayList<>();
                        list.add(map.get(str2rvs));
                        list.add(i);
                        result.add(list);
                    }
                }
                //同上
                if (isPalindrome(str2) && str2.length() != 0) {
                    String str1rvs = new StringBuilder(str1).reverse().toString();
                    if(map.containsKey(str1rvs) && map.get(str1rvs) != i) {
                        List<Integer> list = new ArrayList<>();
                        list.add(i);
                        list.add(map.get(str1rvs));
                        result.add(list);
                    }
                }
            }
        }
        return result;
    }
    //判断字符串是否是回文
    private boolean isPalindrome(String s) {
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
}
```

## 205 Isomorphic Strings

### 原题概述

Given two strings **s** and **t**, determine if they are isomorphic.

Two strings are isomorphic if the characters in **s** can be replaced to get **t**.

All occurrences of a character must be replaced with another character while preserving the order of characters. No two characters may map to the same character but a character may map to itself.

**Example 1:**

```text
Input: s = "egg", t = "add"
Output: true
```

**Example 2:**

```text
Input: s = "foo", t = "bar"
Output: false
```

**Example 3:**

```text
Input: s = "paper", t = "title"
Output: true
```

**Note:**  
You may assume both **s** and **t** have the same length.

### 题意和分析

判断两个词是否是同构词， 原字符串中的每个字符可由另外一个字符替代，可以被其本身替代，相同的字符一定要被同一个字符替代，且一个字符不能被多个字符替代，即不能出现一对多的映射，跟word pattern比较类似。

正常做法是用一个HashMap来做映射，然后逐个检查；当然，不用HashMap而用数组来对照字符也可以，做法是数组前半截存第一个字符串的字符，后半截存第二个字符串的字符，原本两个字符串的字符作为index来记忆位置。

### 代码

使用HashMap

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        Map<Character, Character> map = new HashMap<>();
        int len = s.length();

        for (int i = 0; i < len; i++) {
            char c1 = s.charAt(i);
            char c2 = t.charAt(i);
            if (map.containsKey(c1)) {
                if (map.get(c1) != c2) {
                    return false;
                }
            } else {//不包含key为c1的映射，可能没加入或者加入的是别的pattern
                if (!map.containsValue(c2)) {
                    map.put(c1, c2);
                } else {
                    return false;
                }
            }
        }
        return true;
    }
}
```

利用数组

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        int[] map = new int[512];//正常的ASCII是128位，扩展的ASCII是256位，但不是标准
        for (int i = 0; i < s.length(); i++) {
            if (map[s.charAt(i)] != map[t.charAt(i) + 256]) {//遇到之前存的记录，前后半截不相等
                return false;
            }
            map[s.charAt(i)] = map[t.charAt(i) + 256] = i + 1;//两个字符串做相应的索引，存相同的整数
        }
        return true;
    }
}
```

## 299 Bulls and Cows

### 题目

You are playing the following [Bulls and Cows](https://en.wikipedia.org/wiki/Bulls_and_Cows) game with your friend: You write down a number and ask your friend to guess what the number is. Each time your friend makes a guess, you provide a hint that indicates how many digits in said guess match your secret number exactly in both digit and position \(called "bulls"\) and how many digits match the secret number but locate in the wrong position \(called "cows"\). Your friend will use successive guesses and hints to eventually derive the secret number.

Write a function to return a hint according to the secret number and friend's guess, use `A` to indicate the bulls and `B` to indicate the cows. 

Please note that both secret number and friend's guess may contain duplicate digits.

**Example 1:**

```text
Input: secret = "1807", guess = "7810"

Output: "1A3B"

Explanation: 1 bull and 3 cows. The bull is 8, the cows are 0, 1 and 7.
```

**Example 2:**

```text
Input: secret = "1123", guess = "0111"

Output: "1A1B"

Explanation: The 1st 1 in friend's guess is a bull, the 2nd or 3rd 1 is a cow.
```

**Note:** You may assume that the secret number and your friend's guess only contain digits, and their lengths are always equal.

### 分析

题意比较简单，统计同位置同数字A和不同位置同数字B分别出现的个数，A比较好求，直接比较即可；B的话如果循环两次就是分别统计secret和guess中数字出现的次数，然后按位数比较取较小值代表可重复（需要减去同位置同数字的A）；

如果是一次循环就有点取巧了，secret和guess中的次数分别往两个方向走，判断条件则是各自相反的方向。时间复杂度都是O\(n\)。

### 代码

两次遍历，正常容易想到的

```java
class Solution {
    /*
    A的意思是位置正确且数字正确的个数； 
    B的意思是位置不正确但数字正确的个数。
    */
    public String getHint(String secret, String guess) {
        char[] sc = secret.toCharArray();
        char[] gc = guess.toCharArray();
        
        int[] secret_digits = new int[10];
        int[] guess_digits = new int[10];
        
        int A = 0, total = 0;
        for (int i = 0; i < secret.length(); i++) {
            secret_digits[sc[i] - '0']++;
            guess_digits[gc[i] - '0']++;
            if (sc[i] == gc[i]) {
                A++;
            }
        }
        for (int i = 0; i < 10; i++) {
            //挨个比较取二者较小值，代表所有重复元素
            total += Integer.min(secret_digits[i], guess_digits[i]);
        }
        String ans = A + "A" + (total - A) + "B";//需要减去A
        return ans;
    }
}
```

一次遍历，真是取巧的办法

```java
class Solution {
    public String getHint(String secret, String guess) {
        int numA = 0;
        int numB = 0;
        
        int[] nums = new int[10];
        
        for (int i = 0; i < secret.length(); i++) {
            char s = secret.charAt(i);
            char g = guess.charAt(i);
            if (s == g) {
                numA++;
            } else {//位置和数值都相同的不管
                //以下两个if条件克服了重复元素的问题，要么secret被减掉证明有cows，要么guess被加上也证明有cows
                if (nums[s - '0'] < 0) {//每次循环s不一样
                    numB++;
                }
                if (nums[g - '0'] > 0) {//每次循环g不一样
                    numB++;
                }
                //这里操作与判断条件相反，如果没有同样的元素则不会去相反的方向，cows不会增加
                nums[s - '0']++;
                nums[g - '0']--;
            }
        }
        
        return numA + "A" + numB + "B";
    }
}
```

## 290 Word Pattern

### 题目

Given a `pattern` and a string `str`, find if `str` follows the same pattern.

Here **follow** means a full match, such that there is a bijection between a letter in `pattern` and a **non-empty** word in `str`.

**Example 1:**

```text
Input: pattern = "abba", str = "dog cat cat dog"
Output: true
```

**Example 2:**

```text
Input:pattern = "abba", str = "dog cat cat fish"
Output: false
```

**Example 3:**

```text
Input: pattern = "aaaa", str = "dog cat cat dog"
Output: false
```

**Example 4:**

```text
Input: pattern = "abba", str = "dog dog dog dog"
Output: false
```

**Notes:**  
You may assume `pattern` contains only lowercase letters, and `str` contains lowercase letters that may be separated by a single space.

### 分析

要求求出字符串A的每个字符和字符串B中的单词是否是一个pattern，最后的notes说明可以输入的格式还是比较标准的。这里用HashTable来做，字符串A中的字符作为key，字符串B中的单词作为value，循环一遍挨个比较，中间如果发现有key存在了，就比较当前遍历的value和之前存的value是否相同，不同则返回false；同时也要检查当前的value是否在之前被加入了，这时候要是key不一样也返回false，直到最后没有返回false就为true。

Java中的HashMap的结构要熟悉，containsKey\(\)没有哈希冲突的情况下是O\(1\) ；containsValue\(\)本身的时间复杂度为O\(n\)，如果想优化下就用两个HashTables。

### 代码

使用containsValue\(\)，严格来说最坏的时间复杂度是O\(n^2\)

```java
class Solution {
    public boolean wordPattern(String pattern, String str) {
        String[] strs = str.split(" ");
        if (pattern.length() != strs.length) {
            return false;
        }
        HashMap<Character, String> map = new HashMap<>();
        
        for (int i = 0; i <= pattern.length() - 1; i++) {
            if (map.containsKey(pattern.charAt(i))) {
                if (!map.get(pattern.charAt(i)).equals(strs[i])) {
                    return false;
                }
            } else {
                if (map.containsValue(strs[i])) {
                    return false;
                }
                map.put(pattern.charAt(i), strs[i]);
            }
        }
        return true;
    }
}
```

用额外的一个hashmap来保存第二个字符串中单词的信息，又是空间换时间，时间复杂度O\(n\)

```java
class Solution {
    public boolean wordPattern(String pattern, String str) {
        String[] strs = str.split(" ");
        if (pattern.length() != strs.length) {
            return false;
        }
        HashMap<Character, String> map = new HashMap<>();
        HashMap<String, Character> mapStr = new HashMap<>();
        
        for (int i = 0; i <= pattern.length() - 1; i++) {
            if (map.containsKey(pattern.charAt(i))) {
                if (!map.get(pattern.charAt(i)).equals(strs[i])) {
                    return false;
                }
                //这里无需检查mapStr是否containsKey(strs[i])
            } else {
                if (mapStr.containsKey(strs[i])) {
                    return false;
                }
                map.put(pattern.charAt(i), strs[i]);
                mapStr.put(strs[i], pattern.charAt(i));
            }
        }
        return true;
    }
}
```

## 645 Set Mismatch

### 题目

The set `S` originally contains numbers from 1 to `n`. But unfortunately, due to the data error, one of the numbers in the set got duplicated to **another** number in the set, which results in repetition of one number and loss of another number.

Given an array `nums` representing the data status of this set after the error. Your task is to firstly find the number occurs twice and then find the number that is missing. Return them in the form of an array.

**Example 1:**  


```text
Input: nums = [1,2,2,4]
Output: [2,3]
```

**Note:**  


1. The given array size will in the range \[2, 10000\].
2. The given array's numbers won't have any order.

### 分析

题意就是\[1, n\]之间有个数是重复的，另外有个数是缺失的，找出这两个数返回。直观解法就是借助Hashmap来统计出现次数，时间复杂度O\(n\)，空间复杂度O\(n\)，或者折衷下，先排序然后比较数字，时间复杂度O\(nlogn\)，空间复杂度O\(1\)。

有没有更好方法呢，其实可以对原数组进行标记，将元素变成负数，如果遇到的元素是小于0的，说明已经对该元素变过一次负数，也就是说该元素重复了，最后在遍历一次数组，如果遇到大于0的数，表明在该位置缺少一个元素。

寻找重复数

![](../.gitbook/assets/image%20%2849%29.png)

寻找缺失数

![](../.gitbook/assets/image%20%2850%29.png)



另外用位操作也可以， 因为  
`(1 ^ 2 ^ 3 ^ .. ^ n) ^ (1 ^ 2 ^ 3 ^ .. ^ n) = 0`  
假如 'a' to 'b'分别为缺失数和重复数，那么除了a和b外每个数都被异或两次。 现在结果就是  
`0 ^ a ^ b ^ b ^ b = a ^ b`  
让 `c = a ^ b`, 如果我们可以找到出现两次的数b, 那么'a' 也可以很容易找到`a = c ^ b`.

### 代码

1. 额外数据结构来存储状态

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        int res[] = new int[2];
        //boolean类型默认初始化为false
        boolean[] map=new boolean[nums.length + 1];
        for(int i = 0; i < nums.length; i++)
            if(map[nums[i]] == false)
                map[nums[i]] = true;
            else // 出现两次
                res[0] = nums[i];
        
        for(int i = 1; i < (nums.length + 1); i++) // 没出现的没有改变，仍是false
            if(map[i] == false){
                res[1] = i;
                break;
            }
        return res;
    }
}
```

2. 先排序的解法

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        /**
        特殊情况，如果缺失的数字是1或者n，通过nums[i + 1] - nums[i]与0,2的比较就找不到缺失值了，但总是可以找到重复值，因为必须出现两遍才是重复值。
        所以在遍历完原数组之后，要判断缺失值是否已经找到（找到的标志就是result[1] != 0），如果没有，就去判断缺失了1还是n。
        */
        int[] result = new int[2];
        Arrays.sort(nums);
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i + 1] - nums[i] == 0) {
                result[0] = nums[i];
            }
            if (nums[i + 1] - nums[i] == 2) {
                result[1] = nums[i] + 1;
            }
        }
        if (result[1] == 0) { // 没有找到缺失值，这时候1或者n是备选
            if (nums[0] != 1) {
                result[1] = 1;
            } else {
                result[1] = nums.length;
            }
        }
        return result;
    }
}
```

3. 优化O\(n\) + O\(1\)

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        int[] result = new int[2];
        for (int num : nums) {
            if (nums[Math.abs(num) - 1] < 0) {
                // 已经转过一次负数的数，表明重复
                result[0] = Math.abs(num);
            } else {
                // 将所有元素转为负数
                nums[Math.abs(num)-1] *= -1;
            }
        }
        for (int i = 0; i < nums.length; i++) {
            // 剩下那个没有被转成负数的数
            if (nums[i] > 0) {
                result[1] = i + 1;
            }
        }
        return result;
    }
}
```

可以只遍历一次

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        int dup = -1;
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[Math.abs(nums[i]) - 1] < 0)
                dup = Math.abs(nums[i]);
            else
                nums[Math.abs(nums[i]) - 1] *= -1;

            sum += i + 1 - Math.abs(nums[i]);
        }
        return new int[]{dup, dup + sum};
    }
}
```

4. 位操作

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        int[] result = new int[2];
        for(int i = 0; i < nums.length; i++) {
            int val = Math.abs(nums[i]);
            result[1] ^= (i + 1) ^ val; // 与[1, n]异或
            if (nums[val - 1] < 0) { //最终，异或三次的是重复数，不会变为正数
                result[0] = val;
            }
            else {
                nums[val - 1] = -nums[val - 1]; //正常数改变两次变为正数
            }
        }
        result[1] ^= result[0];
        return result;
    }
}
```

5. 函数式编程

```java
class Solution {
    public int[] findErrorNums(int[] nums) {
        Set<Integer> numSet = Arrays.stream(nums).boxed().collect(Collectors.toSet());
        int setSum = numSet.stream().reduce(0, Integer::sum);
        int actualSum = IntStream.of(nums).sum();
        int targetSum =  IntStream.range(1, nums.length+1).sum();
        return new int[]{actualSum - setSum,  targetSum - setSum};
    }
}
```

