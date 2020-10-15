# 9 Tag

## 1 Two Sum 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum\#1-2sum](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/ksum#1-2sum)

## 560 SubArray Sum Equals to K 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray\#560-subarray-sum-equals-k](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/zi-shu-zu-subarray#560-subarray-sum-equals-k)

## 85 Maximal Rectangle

[https://app.gitbook.com/@guilindev/s/interview/leetcode/array/array-zhong-de-shu-zi-zu-cheng-san-jiao-xing\#85-maximum-rectangle](https://app.gitbook.com/@guilindev/s/interview/leetcode/array/array-zhong-de-shu-zi-zu-cheng-san-jiao-xing#85-maximum-rectangle)

## 767 Reorganize String

### 原题

Given a string `S`, check if the letters can be rearranged so that two characters that are adjacent to each other are not the same.

If possible, output any possible result.  If not possible, return the empty string.

**Example 1:**

```text
Input: S = "aab"
Output: "aba"
```

**Example 2:**

```text
Input: S = "aaab"
Output: ""
```

**Note:**

* `S` will consist of lowercase letters and have length in range `[1, 500]`.

### 思路

插空法

要相邻两个字符不相同，需要将字符全部错开即可，所以第一步，是把每个字符的数量统计出来。~~但数量不同，错开的方式也不一样，如果一个字符的数量大于或等于其他字符的总和，那么只需将这个字符和其他字符依次放在一起即可，如果这个字符数量没有那么多，则那样做不行，所以直接按顺序来看怎么排比较复杂~~。 那不如换个角度来想，只要不是连续两个相同的就行，那可以将数量最多的依次排开，其余的插空即可，这里分两种情况： 

1.如果这个字符数量大于等于其他的总和，那么其余所有字符只需依次插空 

![](../.gitbook/assets/image%20%28128%29.png)

2.如果这个字符数量没有那么多，插到最末尾后，就重头开始循环插空 

![](../.gitbook/assets/image%20%28127%29.png)

第一个图，依次把b和c插完了 第二个图，当到第二个c时，已经到末尾了，就重头开始插空（插空都是从第1个后面的空格开始，因为对于极端情况，当a比其他所有字符数量刚好大于1时，只有这样才能相邻不相同）

### 代码

```java
class Solution {
    public String reorganizeString(String S) {
        int len = S.length();
		//记录字符的数量
		int[] arr = new int[26];
		char[] array = S.toCharArray();
		int max = 0;
		int idx = 0;
		for (char c : array) {
			arr[c - 'a']++;
			if(max < arr[c - 'a']) {
				max = arr[c - 'a'];
				idx = c - 'a';
			}
		}
		if((len & 1) == 0 && max > len/2) {
			return "";
		}
		if((len & 1) == 1 && max > len/2 + 1) {
			return "";
		}
		StringBuilder sb = new StringBuilder();
		//先把数量最多字符铺开，等待插空
		while(arr[idx] > 0) {
			sb.append((char)(idx+'a'));
			arr[idx]--;
		}
		int position = 1;
		for(int i=0; i<arr.length; i++) {
			
			while(arr[i] > 0) {
				//插空
				sb.insert(position, (char)(i+'a'));
				arr[i]--;
				//空位往后移
				position += 2;
				//到末尾时，重头来
				if(position > sb.length()) {
					position = 1;
				}
			}
			
		}
		
		return sb.toString();
    }
}
```

## 528 Random Pick with Weight

### 原题

You are given an array of positive integers `w` where `w[i]` describes the weight of `ith` index \(0-indexed\).

We need to call the function `pickIndex()` which **randomly** returns an integer in the range `[0, w.length - 1]`. `pickIndex()` should return the integer proportional to its weight in the `w` array. For example, for `w = [1, 3]`, the probability of picking the index `0` is `1 / (1 + 3) = 0.25` \(i.e 25%\) while the probability of picking the index `1` is `3 / (1 + 3) = 0.75` \(i.e 75%\).

More formally, the probability of picking index `i` is `w[i] / sum(w)`.

**Example 1:**

```text
Input
["Solution","pickIndex"]
[[[1]],[]]
Output
[null,0]

Explanation
Solution solution = new Solution([1]);
solution.pickIndex(); // return 0. Since there is only one single element on the array the only option is to return the first element.
```

**Example 2:**

```text
Input
["Solution","pickIndex","pickIndex","pickIndex","pickIndex","pickIndex"]
[[[1,3]],[],[],[],[],[]]
Output
[null,1,1,1,1,0]

Explanation
Solution solution = new Solution([1, 3]);
solution.pickIndex(); // return 1. It's returning the second element (index = 1) that has probability of 3/4.
solution.pickIndex(); // return 1
solution.pickIndex(); // return 1
solution.pickIndex(); // return 1
solution.pickIndex(); // return 0. It's returning the first element (index = 0) that has probability of 1/4.

Since this is a randomization problem, multiple answers are allowed so the following outputs can be considered correct :
[null,1,1,1,1,0]
[null,1,1,1,1,1]
[null,1,1,1,0,0]
[null,1,1,1,0,1]
[null,1,0,1,0,0]
......
and so on.
```

**Constraints:**

* `1 <= w.length <= 10000`
* `1 <= w[i] <= 10^5`
* `pickIndex` will be called at most `10000` times.

### 思路

按数组中元素的值作为权重，写一个随机函数。

![](../.gitbook/assets/image%20%28125%29.png)

方法2，用TreeMap代替二分，见代码

![](../.gitbook/assets/image%20%28126%29.png)

### 代码

方法1，前缀和

```java
class Solution {

    List<Integer> psum = new ArrayList<>();
    int tot = 0;
    Random rand = new Random();

    public Solution(int[] w) {
        for (int x : w) {
            tot += x;
            psum.add(tot);
        }
    }

    public int pickIndex() {
        int targ = rand.nextInt(tot);

        int lo = 0;
        int hi = psum.size() - 1;
        while (lo != hi) {
            int mid = (lo + hi) / 2;
            if (targ >= psum.get(mid)) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * Solution obj = new Solution(w);
 * int param_1 = obj.pickIndex();
 */
```

方法2，TreeMap代替二分

```java
class Solution {
    //(wsum, index)
    TreeMap<Integer, Integer> map;
    public Solution(int[] w) {
        map = new TreeMap();
        map.put(w[0], 0);
        for(int i = 1; i < w.length; i++){
            w[i] += w[i - 1];
            map.put(w[i], i);
        }
    }
    
    public int pickIndex() {
        Random rand = new Random();
        int target = rand.nextInt(map.lastKey());
        return map.higherEntry(target).getValue();
    }
}
```

## 843 Guess the Word

### 原题

This problem is an _**interactive problem**_ new to the LeetCode platform.

We are given a word list of unique words, each word is 6 letters long, and one word in this list is chosen as **secret**.

You may call `master.guess(word)` to guess a word.  The guessed word should have type `string` and must be from the original list with 6 lowercase letters.

This function returns an `integer` type, representing the number of exact matches \(value and position\) of your guess to the **secret word**.  Also, if your guess is not in the given wordlist, it will return `-1` instead.

For each test case, you have 10 guesses to guess the word. At the end of any number of calls, if you have made 10 or less calls to `master.guess` and at least one of these guesses was the **secret**, you pass the testcase.

Besides the example test case below, there will be 5 additional test cases, each with 100 words in the word list.  The letters of each word in those testcases were chosen independently at random from `'a'` to `'z'`, such that every word in the given word lists is unique.

```text
Example 1:
Input: secret = "acckzz", wordlist = ["acckzz","ccbazz","eiowzz","abcczz"]

Explanation:

master.guess("aaaaaa") returns -1, because "aaaaaa" is not in wordlist.
master.guess("acckzz") returns 6, because "acckzz" is secret and has all 6 matches.
master.guess("ccbazz") returns 3, because "ccbazz" has 3 matches.
master.guess("eiowzz") returns 2, because "eiowzz" has 2 matches.
master.guess("abcczz") returns 4, because "abcczz" has 4 matches.

We made 5 calls to master.guess and one of them was the secret, so we pass the test case.
```

**Note:**  Any solutions that attempt to circumvent the judge will result in disqualification.

### 思路

**想法 - 极小极大算法**

首先，需要明确的是，当挑选出一个单词进行查询时，若返回值不为6，那么下一次可挑选的单词和此次挑选单词的相似度肯定是此次的返回值。 举例：若目标单词为abcdef，单词表中拥有abcdef abcdeg abckkk，如果此次挑选的是abcdeg，那么返回值将是5，也就是说目标单词和abcdeg只能有1处差异，那么此时abckkk就可以被排除。

明确了这一点之后再来思考如何在符合条件的单词集合中挑选出应该进行查询的单词。原则上后续的可能性越少对于搜索越有利，每个单词都有对应的相似度为0~6的其他单词的集合，每搜索完一个单词之后，就会将目标放置在和该单词相似度为特定值x的集合之上，那么对于单词的搜索思路就应该是，先找到每个单词的7个相似度的单词集合的元素数目最大值，再在所有最大值中找到最小值，这样就可以最大限度的缩小搜索范围。

**算法**

1.维护一个可行的单词列表list，每次从这个列表中选一个单词word进行猜测 

2.如果对word进行猜测返回的结果等于6，猜对了结束；如果等于一个非6数字 x,那么word和秘密单词之间的距离就是6-x。所以下次我们从list和 {与word的距离为6-x的单词列表 } 的交集中选一个单词，当然这个交集就是新的可行单词列表 list。 

3.不断重复1，2。 

当然用HashSet替代列表查找会更快一些

### 代码

```java
/**
 * // This is the Master's API interface.
 * // You should not implement it, or speculate about its implementation
 * interface Master {
 *     public int guess(String word) {}
 * }
 */
class Solution {
    //从HashSet中获取一个string
    public String getOne(HashSet<String> last){
        Iterator<String> it=last.iterator();
        return it.next();
    }
    //取last和now的交集
    public HashSet<String> merge(HashSet<String> last,HashSet<String> now){
        if(last==null) return now;
        HashSet<String> re=new HashSet<>();
        for(String s:last){
            if(now.contains(s)) re.add(s);
        }
        return re;
    }
    //获得a，b两个字符串之间的编辑距离
    public int getDis(String a,String b){
        int count=0;
        for(int i=0;i<6;i++){
            if(a.charAt(i)!=b.charAt(i)) count++;
        }
        return count;
    }
    public void findSecretWord(String[] wordlist, Master master) {
        //是不是从单词之间的距离入手，如果差1个不对，就在距离为1的单词列表中选
        //然后如果差两个不对 就从距离为2 和上一个距离为1 的交集之间选择
        //然后 随便选一个，距离为3 就从 距离为3的集合和距离为1的集合之间的交集中选择
        //有没有符合这种的图啊
        HashSet<String>[] setarr;
        HashMap<String,HashSet<String>[]> map=new HashMap<>();
        //先构建map
        for(String s:wordlist){
            setarr=new HashSet[7];
            for(int i=1;i<7;i++){
                setarr[i]=new HashSet<>();
            }
            map.put(s,setarr);
        }
        int dis=0;
        for(int i=0;i<wordlist.length;i++){
            for(int j=i+1;j<wordlist.length;j++){
                dis=getDis(wordlist[i],wordlist[j]);
                map.get(wordlist[i])[dis].add(wordlist[j]);
                map.get(wordlist[j])[dis].add(wordlist[i]);
            }
        }
        //楼上代码为初始化图

        int len=0;
        String now=wordlist[0];
        HashSet<String> last=null;
        for(int lll=0;lll<10;lll++){
            len=master.guess(now);
            //len是猜中了多少个字符，所以完美的字符串和它之间的差距是 6-len
            if(len==6) return;
            dis=6-len;
            last=merge(last,map.get(now)[dis]);
            now=getOne(last);
        }
    }
}
```

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

