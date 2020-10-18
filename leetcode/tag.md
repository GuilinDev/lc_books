# 9 Tag

##    1 Two Sum 

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

![](../.gitbook/assets/image%20%28132%29.png)

2.如果这个字符数量没有那么多，插到最末尾后，就重头开始循环插空 

![](../.gitbook/assets/image%20%28128%29.png)

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

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#329-longest-increasing-path-in-a-matrix](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#329-longest-increasing-path-in-a-matrix)

##  428 Serialize and Deserialize N-ary Tree 

### 原题

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize an N-ary tree. An N-ary tree is a rooted tree in which each node has no more than N children. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that an N-ary tree can be serialized to a string and this string can be deserialized to the original tree structure.

For example, you may serialize the following `3-ary` tree

![](https://assets.leetcode.com/uploads/2018/10/12/narytreeexample.png)

as `[1 [3[5 6] 2 4]]`. Note that this is just an example, you do not necessarily need to follow this format.

Or you can follow LeetCode's level order traversal serialization format, where each group of children is separated by the null value.

![](https://assets.leetcode.com/uploads/2019/11/08/sample_4_964.png)

For example, the above tree may be serialized as `[1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]`.

You do not necessarily need to follow the above suggested formats, there are many more different formats that work so please be creative and come up with different approaches yourself.

**Constraints:**

* The height of the n-ary tree is less than or equal to `1000`
* The total number of nodes is between `[0, 10^4]`
* Do not use class member/global/static variables to store states. Your encode and decode algorithms should be stateless.

### 思路

1\) DFS, 根据题目提示，至少有两种 encode 形式，而这里选择与 Json 字符串类似的、具有层次信息的一种：“1 \[ 3 \[ 5 6 \] 2 4 \]”。这样就有如下处理思路：

1. 使用栈每一个 “\[” 前的节点，作为父节点（因为可能有很多层级，便要纪律很多个父节点）； 
2. 将 “\[” 与 “\]” 之间的节点放到父节点的 “children” 列表中； 
3. 遇到 “\]” 时，将该父节点出栈，尝试处理与该父节点同一层级、或上一层级的节点。

### 代码

DFS

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Codec {
    //序列化：将二叉树前序遍历，每个子树由[]表示
    // 如题目中给出的示例序列化后变为：1[3[5][6]][2][4]
    // Encodes a tree to a single string.
    public String serialize(Node root) {
        //前序遍历
        if (root == null) return "";
        StringBuilder sb = new StringBuilder();
        helper(root, sb);
        return sb.substring(1, sb.length() - 1);
    }

    //前序遍历
    private void helper(Node root, StringBuilder sb) {
        if (root == null) return;
        sb.append("[").append(root.val);
        if (root.children != null) {
            for (Node child : root.children) {
                helper(child, sb);
            }
        }
        sb.append("]");
    }

    // 创建一个Stack用来保存节点。遍历字符串，如果出现数字，则创建新节点，如果Stack不为空，则取如果stack不为空，则获取顶层元素，
    //加入顶层元素的child中，然后压栈。如果遇到']'则弹栈，直至遍历完字符串。树的元素没有负数，则不需要考虑别的情况。
    public Node deserialize(String data) {
        if (data == null || data.length() == 0) return null;
        Stack<Node> stack = new Stack<>();
        for (int i = 0; i < data.length(); i++) {
            char c = data.charAt(i);
            if (c == ']') stack.pop();
            else {
                if (c >= '0' && c <= '9') {
                    //取当前数组
                    int start = i;
                    while (i < data.length() - 1 && data.charAt(i + 1) >= '0' && data.charAt(i + 1) <= '9') {
                        i++;
                    }
                    Node node = new Node(Integer.valueOf(data.substring(start, i + 1)), new ArrayList<>());
                    if (!stack.isEmpty()) {
                        Node parent = stack.peek();
                        List<Node> list = parent.children;
                        list.add(node);
                    }
                    //压栈
                    stack.push(node);
                }
            }
        }
        if (stack.isEmpty()) return null;
        return stack.peek();
    }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

## 1438 Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit 

### 原题

Given an array of integers `nums` and an integer `limit`, return the size of the longest **non-empty** subarray such that the absolute difference between any two elements of this subarray is less than or equal to `limit`_._

**Example 1:**

```text
Input: nums = [8,2,4,7], limit = 4
Output: 2 
Explanation: All subarrays are: 
[8] with maximum absolute diff |8-8| = 0 <= 4.
[8,2] with maximum absolute diff |8-2| = 6 > 4. 
[8,2,4] with maximum absolute diff |8-2| = 6 > 4.
[8,2,4,7] with maximum absolute diff |8-2| = 6 > 4.
[2] with maximum absolute diff |2-2| = 0 <= 4.
[2,4] with maximum absolute diff |2-4| = 2 <= 4.
[2,4,7] with maximum absolute diff |2-7| = 5 > 4.
[4] with maximum absolute diff |4-4| = 0 <= 4.
[4,7] with maximum absolute diff |4-7| = 3 <= 4.
[7] with maximum absolute diff |7-7| = 0 <= 4. 
Therefore, the size of the longest subarray is 2.
```

**Example 2:**

```text
Input: nums = [10,1,2,4,7,2], limit = 5
Output: 4 
Explanation: The subarray [2,4,7,2] is the longest since the maximum absolute diff is |2-7| = 5 <= 5.
```

**Example 3:**

```text
Input: nums = [4,2,2,2,4,4,2,2], limit = 0
Output: 3
```

**Constraints:**

* `1 <= nums.length <= 10^5`
* `1 <= nums[i] <= 10^9`
* `0 <= limit <= 10^9`

### 思路

给一个整数数组 nums ，和一个表示限制的整数 limit，请你返回最长连续子数组的长度，该子数组中的任意两个元素之间的绝对差必须小于或者等于 limit 。如果不存在满足条件的子数组，则返回 0 。

用单调队列维护当前窗口的最大值和最小值，保持最大值与最小值的差小于 limit 即可

### 代码

```java
class Solution {
    public int longestSubarray(int[] nums, int limit) {
        //这里维护的是最大值们对应的下标
        Deque<Integer> maxQ=new LinkedList<>();
        Deque<Integer> minQ=new LinkedList<>();
        int ans=0;
        //窗口左沿
        int start=0;
        //窗口右沿
        for (int end=0; end<nums.length;end++){
            //右沿元素进入窗口、维护最大值单调队列
            while(!maxQ.isEmpty() && nums[maxQ.peekLast()]<nums[end]){
                maxQ.pollLast();
            }
            maxQ.add(end);
            //右沿元素进入窗口、维护最小值单调队列
            while(!minQ.isEmpty() && nums[minQ.peekLast()]>nums[end]){
                minQ.pollLast();
            }
            minQ.add(end);

            //如果当前窗口的最大值最小值的差大于 limit，则不断缩小窗口（左沿++），直至最大值变小或者最小值变大从而满足 limit 限制
            while(!maxQ.isEmpty() && !minQ.isEmpty() && nums[maxQ.peek()]-nums[minQ.peek()]>limit){
                if(maxQ.peek()<=start) maxQ.poll();
                if(minQ.peek()<=start) minQ.poll();
                start++;
            }
            ans = Math.max(ans,end-start+1);
        }
        return ans;
    }
}

```

##  729 My Calendar I 

### 原题

Implement a `MyCalendar` class to store your events. A new event can be added if adding the event will not cause a double booking.

Your class will have the method, `book(int start, int end)`. Formally, this represents a booking on the half open interval `[start, end)`, the range of real numbers `x` such that `start <= x < end`.

A double booking happens when two events have some non-empty intersection \(ie., there is some time that is common to both events.\)

For each call to the method `MyCalendar.book`, return `true` if the event can be added to the calendar successfully without causing a double booking. Otherwise, return `false` and do not add the event to the calendar. Your class will be called like this: `MyCalendar cal = new MyCalendar();` `MyCalendar.book(start, end)`

**Example 1:**

```text
MyCalendar();
MyCalendar.book(10, 20); // returns true
MyCalendar.book(15, 25); // returns false
MyCalendar.book(20, 30); // returns true
Explanation: 
The first event can be booked.  The second can't because time 15 is already booked by another event.
The third event can be booked, as the first event takes every time less than 20, but not including 20.
```

**Note:**

* The number of calls to `MyCalendar.book` per test case will be at most `1000`.
* In calls to `MyCalendar.book(start, end)`, `start` and `end` are integers in the range `[0, 10^9]`.

### 思路

1\) 线段树

1. 构造线段树节点。 
2. 新添加的线段树节点，要么只能全在node.start往左，要么只能全在node.end往右。 
3. 左右子树如果不存在，这表示该区间不存在，可以插入，如果存在，则往其左右子树递推即可。 
4. 不满足条件的则表示与当前节点的区间发生了重叠，不能插入。

本题构造的线段树节点，以及线段树的更新操作，基本上就是二叉搜索树新增节点的变形，极端情况下，效率会从O\(logN\)退化到O\(N\)

2） TreeMap

直接利用TreeMap，以start为key,end为value。

1. 找到比start小的值，看其value是不是比start大，如果大于start，则表示重复，不能插入 
2. 找到比start大的值，看其本身是不是比end小，如果小于end，则表示重复，不能插入

### 代码

线段树

```java
class MyCalendar {

    // 线段树的根
    private SegmentTreeNode root;

    /**
     * 线段树结构体
     */
    private static class SegmentTreeNode {
        int start;// 时间开始区间
        int end;// 时间结束区间

        SegmentTreeNode left;// 区间左孩子
        SegmentTreeNode right;// 区间右孩子

        SegmentTreeNode(int start,int end){
            this.start = start;
            this.end = end;
        }
    }

    public MyCalendar() {
        root = new SegmentTreeNode(0,0);
    }

    public boolean book(int start, int end) {
        return updateSegment(root,start,end);
    }

    /**
     * 更新线段树节点
     * @param root 线段树节点
     * @param start 新插入的开始时间
     * @param end 新插入的结束时间
     * @return 是否合法
     */
    private boolean updateSegment(SegmentTreeNode root,int start,int end){
        // 新节点，要么只能全在node.start往左，要么只能全在node.end往右
        SegmentTreeNode node = root;
        while (true){
            // 因为start,end是前闭后开区间，所以end可以取等号
            if (end <= node.start){
                // 左子树为空，表示可以添加
                if (node.left == null){
                    node.left = new SegmentTreeNode(start,end);
                    return true;
                }
                // 进入左子树线段树
                node = node.left;
            }else if (start >= node.end){
                // 右子树为空，表示可以添加
                if (node.right == null){
                    node.right = new SegmentTreeNode(start, end);
                    return true;
                }
                // 进入右子树的线段树
                node = node.right;
            }else {
                // start或end在[node.start,node.end)中，产生了重复预定
                return false;
            }
        }
    }
}

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * boolean param_1 = obj.book(start,end);
 */

```

TreeMap

```java
class MyCalendar {
    // key->start ; value->end
    private TreeMap<Integer,Integer> tm;

    public MyCalendar() {
        tm = new TreeMap<>();
    }

    public boolean book(int start, int end) {
        if (start >= end){
            return false;
        }
        Integer low = tm.floorKey(start);
        if(low != null && tm.get(low) > start){
            return false;// 前节点考虑
        }
        Integer high = tm.ceilingKey(start);
        if (high != null && high < end){
            return false;// 后节点考虑
        }
        tm.put(start,end);
        return true;
    }
}

/**
 * Your MyCalendar object will be instantiated and called as such:
 * MyCalendar obj = new MyCalendar();
 * boolean param_1 = obj.book(start,end);
 */

```

##  722 Remove Comments 

### 原题

Given a C++ program, remove comments from it. The program `source` is an array where `source[i]` is the `i`-th line of the source code. This represents the result of splitting the original source code string by the newline character `\n`.

In C++, there are two types of comments, line comments, and block comments.

 The string `//` denotes a line comment, which represents that it and rest of the characters to the right of it in the same line should be ignored.

 The string `/*` denotes a block comment, which represents that all characters until the next \(non-overlapping\) occurrence of `*/` should be ignored. \(Here, occurrences happen in reading order: line by line from left to right.\) To be clear, the string `/*/` does not yet end the block comment, as the ending would be overlapping the beginning.

 The first effective comment takes precedence over others: if the string `//` occurs in a block comment, it is ignored. Similarly, if the string `/*` occurs in a line or block comment, it is also ignored.

 If a certain line of code is empty after removing comments, you must not output that line: each string in the answer list will be non-empty.

 There will be no control characters, single quote, or double quote characters. For example, `source = "string s = "/* Not a comment. */";"` will not be a test case. \(Also, nothing else such as defines or macros will interfere with the comments.\)

 It is guaranteed that every open block comment will eventually be closed, so `/*` outside of a line or block comment always starts a new comment.

 Finally, implicit newline characters can be deleted by block comments. Please see the examples below for details.

After removing the comments from the source code, return the source code in the same format.

**Example 1:**  


```text
Input: 
source = ["/*Test program */", "int main()", "{ ", "  // variable declaration ", "int a, b, c;", "/* This is a test", "   multiline  ", "   comment for ", "   testing */", "a = b + c;", "}"]

The line by line code is visualized as below:
/*Test program */
int main()
{ 
  // variable declaration 
int a, b, c;
/* This is a test
   multiline  
   comment for 
   testing */
a = b + c;
}

Output: ["int main()","{ ","  ","int a, b, c;","a = b + c;","}"]

The line by line code is visualized as below:
int main()
{ 
  
int a, b, c;
a = b + c;
}

Explanation: 
The string /* denotes a block comment, including line 1 and lines 6-9. The string // denotes line 4 as comments.
```

**Example 2:**  


```text
Input: 
source = ["a/*comment", "line", "more_comment*/b"]
Output: ["ab"]
Explanation: The original source string is "a/*comment\nline\nmore_comment*/b", where we have bolded the newline characters.  After deletion, the implicit newline characters are deleted, leaving the string "ab", which when delimited by newline characters becomes ["ab"].
```

**Note:**

The length of `source` is in the range `[1, 100]`.

The length of `source[i]` is in the range `[0, 80]`.

Every open block comment is eventually closed.

There are no single-quote, double-quote, or control characters in the source code.

### 思路

需要逐行分析源代码。此题是考验字符串处理的能力 有五种状态:

* 无注释
* 单行注释 
* 块注释开始
* 块注释中间 
* 块注释结束 

算法如下:

* 如果目前已经是行注释状态，则直接进入下一行判定（因为行注释是注释至行末尾） 
* 如果目前已经是块注释状态，则判断接下来的字符串是否为\*/，如果是，则块注释状态结束；否则进入这一行的下一个字符判定 
* 如果两个注释都没有，则判定当前字符是否可能构成一个注释\(必须是/\)，如果可以，则设置当前的注释状态；如果不可以，则当前字符是一个有效字符，并把字符串累加到StringBuilder中 
* 这里面要注意的是，如果是块注释状态，需要累计到下一行的，所以往resultList里面添加的时候需要判定一下是否为块注释状态。

### 代码

```java
class Solution {
    public List<String> removeComments(String[] source) {
        boolean isLineComment = false;
        boolean isBlockComment = false;
        StringBuilder sbLine = null;
        List<String> resultList = new ArrayList<>();
        for(String strLine : source){
            int strLen = strLine.length();
            int cursor = 0;
            if(!isBlockComment){
                sbLine = new StringBuilder();
            }
            while(cursor < strLen){
                if(isLineComment){
                    isLineComment = false;
                    break;//进入下一行
                }else if(isBlockComment){
                    if(strLine.charAt(cursor) == '*'
                        && cursor < strLen - 1 && strLine.charAt(cursor + 1) == '/'
                        /*&& cursor > 0 && strLine.charAt(cursor - 1) != '/'*/){
                            isBlockComment = false;
                            cursor++;
                        }
                }else{//两个注释都没有
                    //是否可能有注释
                    if(strLine.charAt(cursor) == '/'){
                        if(cursor < strLen - 1 && strLine.charAt(cursor + 1) == '/'){
                            isLineComment = true;
                            cursor++;
                        }else if(cursor < strLen - 1 && strLine.charAt(cursor + 1) == '*'){
                            isBlockComment = true;
                            cursor++;
                        }else{
                            sbLine.append(strLine.charAt(cursor));
                        }
                    }else{
                        sbLine.append(strLine.charAt(cursor));
                    }
                }

                cursor++;
            }
            if(!isBlockComment && sbLine.length() > 0){
                resultList.add(sbLine.toString());
            }
        }
        return resultList;
    }
}
```

## 1031 Maximum Sum of Two Non-Overlapping Subarrays

### 原题

Given an array `A` of non-negative integers, return the maximum sum of elements in two non-overlapping \(contiguous\) subarrays, which have lengths `L` and `M`.  \(For clarification, the `L`-length subarray could occur before or after the `M`-length subarray.\)

Formally, return the largest `V` for which `V = (A[i] + A[i+1] + ... + A[i+L-1]) + (A[j] + A[j+1] + ... + A[j+M-1])` and either:

* `0 <= i < i + L - 1 < j < j + M - 1 < A.length`, **or**
* `0 <= j < j + M - 1 < i < i + L - 1 < A.length`.

1. 
**Example 1:**

```text
Input: A = [0,6,5,2,2,5,1,9,4], L = 1, M = 2
Output: 20
Explanation: One choice of subarrays is [9] with length 1, and [6,5] with length 2.
```

**Example 2:**

```text
Input: A = [3,8,1,3,2,1,8,9,0], L = 3, M = 2
Output: 29
Explanation: One choice of subarrays is [3,8,1] with length 3, and [8,9] with length 2.
```

**Example 3:**

```text
Input: A = [2,1,5,6,0,9,5,0,3,8], L = 4, M = 3
Output: 31
Explanation: One choice of subarrays is [5,6,0,9] with length 4, and [3,8] with length 3.
```

**Note:**

1. `L >= 1`
2. `M >= 1`
3. `L + M <= A.length <= 1000`
4. `0 <= A[i] <= 1000`

### 思路

前缀和

`Lsum`, sum of the last `L` elements  
 `Msum`, sum of the last `M` elements

`Lmax`, max sum of contiguous `L` elements before the last `M` elements.  
 `Mmax`, max sum of contiguous `M` elements before the last `L` elements/

#### **复杂度：**

Two pass, `O(N)` time,`O(1)` extra space.

It can be done in one pass. I just don't feel like merging them.  
 If you don't like change the original input, don't have to.

### 代码

前缀和

```java
public int maxSumTwoNoOverlap(int[] A, int L, int M) {
        int[] prefixSum = new int[A.length + 1];
        for (int i = 0; i < A.length; ++i) {
            prefixSum[i + 1] = prefixSum[i] + A[i];
        }
        return Math.max(maxSum(prefixSum, L, M), maxSum(prefixSum, M, L));
    }
    private int maxSum(int[] p, int L, int M) {
        int ans = 0;
        for (int i = L + M, maxL = 0; i < p.length; ++i) {
            maxL = Math.max(maxL, p[i - M] - p[i - M - L]); // update max of L-length subarray.
            ans = Math.max(ans, maxL + p[i] - p[i - M]); // update max of the sum of L-length & M-length subarrays.
        }
        return ans;
    }
```

滑动窗口

```java
class Solution {
    public int maxSumTwoNoOverlap(int[] A, int L, int M) {
        return Math.max(maxSum(A, L, M), maxSum(A, M, L));
    }
    private int maxSum(int[] A, int L, int M) {
        int sumL = 0, sumM = 0;
        for (int i = 0; i < L + M; ++i) { // compute the initial values of L & M length subarrays.
            if (i < L) sumL += A[i];
            else sumM += A[i];
        }
        int ans = sumM + sumL; // sum of sumL and sumM.
        for (int i = L + M, maxL = sumL; i < A.length; ++i) {
            sumM += A[i] - A[i - M]; // update sumM.
            sumL += A[i - M] - A[i - L - M]; // update sumL.
            maxL = Math.max(maxL, sumL); // update max value of L-length subarray.
            ans = Math.max(ans, maxL + sumM); // update max value of sum of L & M-length subarrays.
        }
        return ans;
    }
}
```

##  1060 Missing Element in Sorted Array 

### 原题

Given a sorted array `A` of **unique** numbers, find the _`K`_`-th` missing number starting from the leftmost number of the array.

**Example 1:**

```text
Input: A = [4,7,9,10], K = 1
Output: 5
Explanation: 
The first missing number is 5.
```

**Example 2:**

```text
Input: A = [4,7,9,10], K = 3
Output: 8
Explanation: 
The missing numbers are [5,6,8,...], hence the third missing number is 8.
```

**Example 3:**

```text
Input: A = [1,2,4], K = 3
Output: 6
Explanation: 
The missing numbers are [3,5,6,7,...], hence the third missing number is 6.
```

**Note:**

1. `1 <= A.length <= 50000`
2. `1 <= A[i] <= 1e7`
3. `1 <= K <= 1e8`

### 思路

 出一个有序数组 `A`，数组中的每个数字都是 **独一无二的**，找出从数组最左边开始的第 _`K`_ 个缺失数字。

暴力法线性扫描，找到 `missing(idx - 1) < K <= missing(idx)`。

优化解法用二分查找。

### 代码

```java
class Solution {
    public int missingElement(int[] nums, int k) {
        int length = nums.length;
        if (k > missing(length - 1, nums))
            return nums[length - 1] + k - missing(length - 1, nums);
        int left = 0, right = length - 1, mid;
        while (left < right) {
            mid = left + (right - left) / 2;
            if (missing(mid, nums) < k) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }
        return nums[left - 1] + k - missing(left - 1, nums);
    }

    /**
     * 到 A[idx] 为止总共缺失的数字个数
     *
     * @param idx  2
     * @param nums [4,7,9,10]
     * @return
     */
    private int missing(int idx, int[] nums) {
        return nums[idx] - nums[0] - idx;   // 应该有的数量 - 实际有的数量 = 缺失的数量
    }
}
```

## 642 Design Search Autocomplete System 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/design\#642-design-search-autocomplete-system](https://app.gitbook.com/@guilindev/s/interview/leetcode/design#642-design-search-autocomplete-system)

## 359 Logger Rate Limiter 

### 原题

Design a logger system that receive stream of messages along with its timestamps, each message should be printed if and only if it is **not printed in the last 10 seconds**.

Given a message and a timestamp \(in seconds granularity\), return true if the message should be printed in the given timestamp, otherwise returns false.

It is possible that several messages arrive roughly at the same time.

**Example:**

```text
Logger logger = new Logger();

// logging string "foo" at timestamp 1
logger.shouldPrintMessage(1, "foo"); returns true; 

// logging string "bar" at timestamp 2
logger.shouldPrintMessage(2,"bar"); returns true;

// logging string "foo" at timestamp 3
logger.shouldPrintMessage(3,"foo"); returns false;

// logging string "bar" at timestamp 8
logger.shouldPrintMessage(8,"bar"); returns false;

// logging string "foo" at timestamp 10
logger.shouldPrintMessage(10,"foo"); returns false;

// logging string "foo" at timestamp 11
logger.shouldPrintMessage(11,"foo"); returns true;
```

### 思路

1\)  直观解法，队列 + 集合

在我们解决问题之前，必须明确问题的条件。这里有一个重要的注意事项：可能会有几条日志同时到达。

我们知道日志的顺序是按时间顺序排列的，即日志的时间戳是单调递增的，尽管不是严格地递增。这个条件非常关键，因为它可以简化任务，如下面的解决方案所示。

第一个解决方案，我们按照问题中描述的任务直观地实现一个解决方案。

我们将接收的日志存储在队列中。为了加速重复项的检查，我们使用集合来检索日志。

![](../.gitbook/assets/image%20%28129%29.png)

从上面的示例可以看出，其中数字表示每个日志的时间戳，时间戳为 18 的日志到达后将使时间戳为 5 和 7 的日志失效，这两个日志超出了 10 秒的时间窗口。

**算法：**

首先，我们使用队列作为滑动窗口，将存储所有时间范围内（10 秒）的日志。 在接收每个日志后，它都有一个时间戮，这意味着滑动窗口需要变化，所以应该将队列中超出时间范围的日志失效。 由于队列和集合应该同步，我们还应该在集合中删除失效的日志。 更新队列和集合后，我们只需检查新接收的日志是否有任何重复项。如果没有，我们将日志添加到队列和集合中。

可以发现使用集合并不是绝对必要的。也可以简单地迭代队列来检查是否有任何重复的日志。

另一个重要的注意事项是如果消息不是按时间顺序排列的，那么我们必须遍历整个队列以删除过期的日志。可以使用一些排序队列（如优先级队列）来保存日志。

**复杂度分析**

时间复杂度：O\(N\)。N 是队列的大小，在最坏的情况下，队列中的日志全部过期了，则需要情况队列。

空间复杂度：O\(N\)，N 是队列的大小，我们在队列和集合中存储了日志的信息，所需空间的上限是 2N，当没有重复的日志时。

2） 优化，哈希表

可以将队列和集合数据结构组合成一个哈希表或字典，这样我们就能够保留队列中所有唯一的日志，也能够快速判断日志的重复性。

做法是用一个哈希表 / 字典，日志作为键，时间戳作为值。哈希表保留所有唯一的日志以及日志的最新时间戳。

![](../.gitbook/assets/image%20%28130%29.png)

从上面的例子可以看到，哈希表中有一条记录，日志 m2 和时间戳 2。然后出现另一条日志 m2，时间戳为 15。由于日志是在 13 秒之前打印的（即超出时间范围），因此它可以再次打印消息。然后，日志 m2 的时间戳将更新为 15。

**算法：**

* 初始化一个哈希表 / 字典来记录日志到时间戳的映射。 
* 新日志到达时，条件如下时可以打印： 
  * 我们以前从未见过这个日志。 
  * 我们以前见过这条日志但是是在 10 多秒之前。 
* 在上述两种情况下，我们将更新哈希表中对应的日志的时间戮。

注：为了清楚起见，我们把这两个情况分别处理，也可以把这两个情况结合起来，得到一个更简洁的解决方案。

这种使用哈希表的方法与使用队列的方法的主要区别在于，在队列中我们要进行清理，即在每次调用函数时，我们首先删除那些过期的日志。

在哈希表中即使日志过期，我们也会保留所有日志。这种特性可能会成为问题，因为随着时间的推移，内存的使用将继续增长。有时，更需要使用前面方法的垃圾回收机制。

复杂度分析

* 时间复杂度：O\(1\)。哈希表的查找和更新是常数的时间复杂度。 
* 空间复杂度：O\(M\)，其中 MMM 是日志的数量。随着时间的推移，哈希表记录了每个出现过的唯一日志。

### 代码

直观解法，队列+集合

```java
class Pair<U, V> {
  public U first;
  public V second;

  public Pair(U first, V second) {
    this.first = first;
    this.second = second;
  }
}

class Logger {
  private LinkedList<Pair<String, Integer>> msgQueue;
  private HashSet<String> msgSet;

  /** Initialize your data structure here. */
  public Logger() {
    msgQueue = new LinkedList<Pair<String, Integer>>();
    msgSet = new HashSet<String>();
  }

  /**
   * Returns true if the message should be printed in the given timestamp, otherwise returns false.
   */
  public boolean shouldPrintMessage(int timestamp, String message) {

    // clean up.
    while (msgQueue.size() > 0) {
      Pair<String, Integer> head = msgQueue.getFirst();
      if (timestamp - head.second >= 10) {
        msgQueue.removeFirst();
        msgSet.remove(head.first);
      } else
        break;
    }

    if (!msgSet.contains(message)) {
      Pair<String, Integer> newEntry = new Pair<String, Integer>(message, timestamp);
      msgQueue.addLast(newEntry);
      msgSet.add(message);
      return true;
    } else
      return false;

  }
}


/**
 * Your Logger object will be instantiated and called as such:
 * Logger obj = new Logger();
 * boolean param_1 = obj.shouldPrintMessage(timestamp,message);
 */
```

哈希表

```java
class Logger {
  private HashMap<String, Integer> msgDict;

  /** Initialize your data structure here. */
  public Logger() {
    msgDict = new HashMap<String, Integer>();
  }

  /**
   * Returns true if the message should be printed in the given timestamp, otherwise returns false.
   */
  public boolean shouldPrintMessage(int timestamp, String message) {

    if (!this.msgDict.containsKey(message)) {
      this.msgDict.put(message, timestamp);
      return true;
    }

    Integer oldTimestamp = this.msgDict.get(message);
    if (timestamp - oldTimestamp >= 10) {
      this.msgDict.put(message, timestamp);
      return true;
    } else
      return false;
  }
}


/**
 * Your Logger object will be instantiated and called as such:
 * Logger obj = new Logger();
 * boolean param_1 = obj.shouldPrintMessage(timestamp,message);
 */
```

## 946 Validate Stack Sequences 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/stack\#946-validate-stack-sequence](https://app.gitbook.com/@guilindev/s/interview/leetcode/stack#946-validate-stack-sequence)

## 1293 Shortest Path in a Grid with Obstacles Elimination 

### 原题

Given a `m * n` grid, where each cell is either `0` \(empty\) or `1` \(obstacle\). In one step, you can move up, down, left or right from and to an empty cell.

Return the minimum number of steps to walk from the upper left corner `(0, 0)` to the lower right corner `(m-1, n-1)` given that you can eliminate **at most** `k` obstacles. If it is not possible to find such walk return -1.

**Example 1:**

```text
Input: 
grid = 
[[0,0,0],
 [1,1,0],
 [0,0,0],
 [0,1,1],
 [0,0,0]], 
k = 1
Output: 6
Explanation: 
The shortest path without eliminating any obstacle is 10. 
The shortest path with one obstacle elimination at position (3,2) is 6. Such path is (0,0) -> (0,1) -> (0,2) -> (1,2) -> (2,2) -> (3,2) -> (4,2).
```

**Example 2:**

```text
Input: 
grid = 
[[0,1,1],
 [1,1,1],
 [1,0,0]], 
k = 1
Output: -1
Explanation: 
We need to eliminate at least two obstacles to find such a walk.
```

**Constraints:**

* `grid.length == m`
* `grid[0].length == n`
* `1 <= m, n <= 40`
* `1 <= k <= m*n`
* `grid[i][j] == 0` **`or`** `1`
* `grid[0][0] == grid[m-1][n-1] == 0`

### 思路

给你一个 m \* n 的网格，其中每个单元格不是 0（空）就是 1（障碍物）。每一步，您都可以在空白单元格中上、下、左、右移动。如果您 最多 可以**消除 k 个障碍物**，请找出从左上角 \(0, 0\) 到右下角 \(m-1, n-1\) 的最短路径，并返回通过该路径所需的步数。如果找不到这样的路径，则返回 -1。

扩展：

* 若题目要求求解最小层级搜索（节点间距离固定为1），通过统计层级计数，遇到终止条件终止即可。 
* 若节点间有加权值，求解最短路径时可以在Node中增加cost记录，比较获取最佳值 
* 若需要求解最短路径，可以逆向根据visited访问记录情况回溯

1） 本题方法1，visited访问标记数组二维 + **贪心** （推荐），精髓在于对标记访问数组 visited值的扩展，常规0\|1标记是否访问，但还需要记录走到当前位置所剩的消除障碍物机会，越多越好。因为后面的路障谁都不清楚够不够用。

2）本题方法2，visited访问标记数组**三维**扩展 （用于比较），比较麻烦些，增加了障碍物且可以有k次机会消除，单纯有障碍物就是标准的BFS处理即可，但有k次消除障碍物，就需要增加一个维度来记录同一个节点被访问的时候 已经使用消除障碍物的次数

时间复杂度：O\(MN∗min\(M+N,K\)\)。

空间复杂度：O\(MN∗min\(M+N,K\)\)。

### 代码

标记+贪心（剩余消除次数越多越好）

```java
class Solution {
    public int shortestPath(int[][] grid, int k) {
        int m = grid.length;
        int n = grid[0].length;
        // 非法参数处理
        if (validateInputParams(k, m, n)) {
            return -1;
        }
        // 特殊场景处理
        if (m == 1 && n == 1) {
            return 0;
        }

        // BFS对于当前点的下一个点选择，如果grid[i][j]=0则有效入队列 visited[i][j]记录消除障碍次数
        // 若grid[i][j]=1则看是否还有消除障碍机会，若没有 此点丢弃
        // 若有消除障碍机会， （上一个点剩余消除障碍机会 - 1）比visited[i][j] 值比大 此点入队， 小则丢弃（贪心）
        // 例子：k=1, 坐标(0,2)可以为消除(0,1)障碍过来的 visited[0][2] = 0，搜索层级为2
        // 也可能为不消除任何障碍过来的 visited[0][2] = 1，层级为6，更新visited[0][2] = 1并入队
        // 因为到后面还需要消除障碍才能到达目标，先消除障碍走到visited[0][2] = 0的肯定到不了目标...
        // 0 1 0 0 0 1 0 0
        // 0 1 0 1 0 1 0 1
        // 0 0 0 1 0 0 1 0
        
        // 二维标记数组初始状态为-1，值记录剩余消除障碍的次数，剩余次数越多 越有价值（此处贪心，记录局部最优）
        int[][] visited = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                visited[i][j] = -1;
            }
        }
        // 初始步数为0，m=n=1的特殊场景已处理
        int minSteps = 0;

        // 初始位置标记已访问,值记录剩余消除障碍物次数  越多越好
        // 1. 对于其他路径到达此点且剩余消除障碍物次数小于等于当前值 —— 剪枝
        // 2. 对于其他路径到达此点且剩余消除障碍物次数大于当前值 —— 取代并入队
        visited[0][0] = k;
        Queue<Point> queue = new LinkedList<>();
        Point startPoint = new Point(0, 0, 0);
        queue.offer(startPoint);
        
        // 定义四个方向移动坐标
        int[] dx = {1, -1, 0, 0};
        int[] dy = {0, 0, 1, -1};
        // BFS搜索-队列遍历
        while (!queue.isEmpty()) {
            minSteps++;
            // BFS搜索-遍历相同层级下所有节点
            // 当前队列长度一定要在循环外部定义，循环内部有插入对列操作
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Point current = queue.poll();
                int x = current.x;
                int y = current.y;
                int oneCount = current.oneCount;

                // 对当前节点四个方向进行识别处理
                for (int j = 0; j < 4; j++) {
                    int xNew = x + dx[j];
                    int yNew = y + dy[j];
                    // 越界判断
                    if (xNew < 0 || xNew >= m || yNew < 0 || yNew >= n) {
                        continue;
                    }
                    // 搜索到目标节点直接返回结果，按层级就是最短步数
                    if (xNew == m - 1 && yNew == n - 1) {
                        return minSteps;
                    }
                    // 穿越障碍次数已满
                    if (grid[xNew][yNew] == 1 && oneCount >= k) {
                        continue;
                    }
                    int oneCountNew = grid[xNew][yNew] == 1 ? oneCount + 1 : oneCount;
                    // 剪枝 - 节点已被访问过，且当前visited记录的剩余障碍物消除次数 >= 当前搜索节点层级的剩余消除次数
                    if (visited[xNew][yNew] != -1 && visited[xNew][yNew] >= k - oneCountNew) {
                        continue;
                    } else {
                        // 否则，贪心将最优值更新，并将该层级节点入队
                        visited[xNew][yNew] = k - oneCountNew;
                    }
                    queue.offer(new Point(xNew, yNew, oneCountNew));
                }
            }
        }
        // BFS没搜索到目标，返回-1
        return -1;
    }

    private boolean validateInputParams(int k, int m, int n) {
        return m > 40 || m < 1 || n > 40 || n < 1 || k < 1 || k > m * n;
    }

    class Point {
        int x;
        int y;
        int oneCount;

        public Point(int x, int y, int oneCount) {
            this.x = x;
            this.y = y;
            this.oneCount = oneCount;
        }
    }
}
```

标记+三维（消除次数额外成一个维度）

```java
class Solution {
    public int shortestPath(int[][] grid, int k) {
        int m = grid.length;
        int n = grid[0].length;
        // 非法参数处理
        if (validateInputParams(k, m, n)) {
            return -1;
        }
        // 特殊场景处理
        if (m == 1 && n == 1) {
            return 0;
        }
        // BFS搜索节点访问标识, 此题要求有k个消除障碍的机会，所以每个节点除了标记是否被访问过
        // 还要记录搜索到此节点时消除了几个障碍。消除相同障碍的下一层节点 可以剪枝（因为有相同代价更早的节点了）
        // 例子：k=1, BFS是按层级来的，绕道的层级扩展越多
        // 坐标(0,2)可以为消除(0,1)障碍过来的 visited[0][2][1] = 1，搜索层级为2
        // 也可能为不消除任何障碍过来的 visited[0][2][0] = 1，层级为6，为扩展搜索不通障碍消除数提供区分
        // 0 1 0 0 0 1 0 0
        // 0 1 0 1 0 1 0 1
        // 0 0 0 1 0 0 1 0

        // 二维标记位置，第三维度标记 到此节点的路径处理障碍总个数
        int[][][] visited = new int[m][n][k+1];
        // 初始步数为0，m=n=1的特殊场景已处理
        int minSteps = 0;
        // 初始位置标记已访问
        visited[0][0][0] = 1;
        Queue<Point> queue = new LinkedList<>();
        Point startPoint = new Point(0, 0, 0);
        queue.offer(startPoint);

        // 定义四个方向移动坐标
        int[] dx = {1, -1, 0, 0};
        int[] dy = {0, 0, 1, -1};
        // BFS搜索-队列遍历
        while (!queue.isEmpty()) {
            minSteps++;
            // BFS搜索-遍历相同层级下所有节点
            // 当前队列长度一定要在循环外部定义，循环内部有插入对列操作
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Point current = queue.poll();
                int x = current.x;
                int y = current.y;
                int oneCount = current.oneCount;

                // 对当前节点四个方向进行识别处理
                for (int j = 0; j < 4; j++) {
                    int xNew = x + dx[j];
                    int yNew = y + dy[j];
                    // 越界
                    if (xNew < 0 || xNew >= m || yNew < 0 || yNew >= n) {
                        continue;
                    }
                    // 搜索到目标节点则直接返回结果
                    if (xNew == m - 1 && yNew == n - 1) {
                        return minSteps;
                    }
                    // 穿越障碍次数已满
                    if (grid[xNew][yNew] == 1 && oneCount >= k) {
                        continue;
                    }
                    int oneCountNew = grid[xNew][yNew] == 1 ? oneCount + 1 : oneCount;
                    // 四个方向节点是否被访问过（第三维度）
                    if (visited[xNew][yNew][oneCountNew] == 1) {
                        continue;
                    } else {
                        // 未被访问过且可以走的节点标记为访问过，对下一步节点确认状态非常重要
                        // 将下一层级节点入队列标记为已访问，可以剪枝更多节点，节省计算耗时
                        visited[xNew][yNew][oneCountNew] = 1;
                    }
                    queue.offer(new Point(xNew, yNew, oneCountNew));
                }
            }
        }
        // BFS没搜索到目标，返回-1
        return -1;
    }

    private boolean validateInputParams(int k, int m, int n) {
        return m > 40 || m < 1 || n > 40 || n < 1 || k < 1 || k > m * n;
    }

    class Point {
        int x;
        int y;
        int oneCount;

        public Point(int x, int y, int oneCount) {
            this.x = x;
            this.y = y;
            this.oneCount = oneCount;
        }
    }
}
```

## 1048 Longest String Chain 

### 原题

Given a list of words, each word consists of English lowercase letters.

Let's say `word1` is a predecessor of `word2` if and only if we can add exactly one letter anywhere in `word1` to make it equal to `word2`.  For example, `"abc"` is a predecessor of `"abac"`.

A _word chain_ is a sequence of words `[word_1, word_2, ..., word_k]` with `k >= 1`, where `word_1` is a predecessor of `word_2`, `word_2` is a predecessor of `word_3`, and so on.

Return the longest possible length of a word chain with words chosen from the given list of `words`.

**Example 1:**

```text
Input: ["a","b","ba","bca","bda","bdca"]
Output: 4
Explanation: one of the longest word chain is "a","ba","bda","bdca".
```

**Note:**

1. `1 <= words.length <= 1000`
2. `1 <= words[i].length <= 16`
3. `words[i]` only consists of English lowercase letters.

### 思路

DFS暴力，升级为动态规划。

 `dp[i]`表示从`words[0]`到`words[i]`最长的词链长度。

将字母按字符长度字典序排序的代码：

```java
Arrays.sort(words, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        return o1.length() - o2.length();
    }
});
```

![](../.gitbook/assets/image%20%28131%29.png)

### 代码

```java
class Solution {
    public int longestStrChain(String[] words) {
        Arrays.sort(words, Comparator.comparingInt(String::length));
        int n = words.length;
        int[] dp = new int[n];
        int res = 0;
        for (int i = 0; i < n; i++) {
            String a = words[i];
            for (int j = i + 1; j < n; j++) {
                String b = words[j];
                if (isPredecessor(a, b)) {
                    dp[j] = Math.max(dp[j], dp[i] + 1);
                    res = Math.max(dp[j], res);
                }
            }
        }
        return res + 1;
    }

    /**
     * 判断a是否是b的前身 是返回true 如 "bda" 是"bdca"的前身
     *
     * @param a
     * @param b
     * @return
     */
    private boolean isPredecessor(String a, String b) {
        int i = 0, j = 0;
        int m = a.length(), n = b.length();
        if ((m + 1) != n) return false;
        while (i < m && j < n) {
            if (a.charAt(i) == b.charAt(j)) i++;
            j++;
        }
        return i == m;
    }

}
```

## 1423 Maximum Points You Can Obtain from Cards 

### 原题

There are several cards **arranged in a row**, and each card has an associated number of points The points are given in the integer array `cardPoints`.

In one step, you can take one card from the beginning or from the end of the row. You have to take exactly `k` cards.

Your score is the sum of the points of the cards you have taken.

Given the integer array `cardPoints` and the integer `k`, return the _maximum score_ you can obtain.

**Example 1:**

```text
Input: cardPoints = [1,2,3,4,5,6,1], k = 3
Output: 12
Explanation: After the first step, your score will always be 1. However, choosing the rightmost card first will maximize your total score. The optimal strategy is to take the three cards on the right, giving a final score of 1 + 6 + 5 = 12.
```

**Example 2:**

```text
Input: cardPoints = [2,2,2], k = 2
Output: 4
Explanation: Regardless of which two cards you take, your score will always be 4.
```

**Example 3:**

```text
Input: cardPoints = [9,7,7,9,7,7,9], k = 7
Output: 55
Explanation: You have to take all the cards. Your score is the sum of points of all cards.
```

**Example 4:**

```text
Input: cardPoints = [1,1000,1], k = 1
Output: 1
Explanation: You cannot take the card in the middle. Your best score is 1. 
```

**Example 5:**

```text
Input: cardPoints = [1,79,80,1,1,1,200,1], k = 3
Output: 202
```

**Constraints:**

* `1 <= cardPoints.length <= 10^5`
* `1 <= cardPoints[i] <= 10^4`
* `1 <= k <= cardPoints.length`

### 思路

暴力法DFS，升级前缀和或滑动窗口。

1）先求个前缀和，再求left\(0-k\) + right\(k-i\) 区间最大解即可

2）滑动窗口，维护一个len-k的窗口，逆向思维，保证窗口里面和最小，然后剩余的k个数的和就是最大

### 代码

前缀和

```java
class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int len = cardPoints.length;
        int []pre = new int[len+1]; pre[0] = 0;
        for(int i = 0;i < len;i++){
            pre[i+1] = cardPoints[i] + pre[i];
        }
        int max = -1;
        for(int i = 0;i <= k;i++){
            max = Math.max(max, pre[i] + pre[len] - pre[len-k+i]);
        }
        return max;
    }
}
```

滑动窗口

```java
class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int len = cardPoints.length, sum = 0;
        for (int cardPoint: cardPoints) {
            sum += cardPoint;
        }
        int min = Integer.MAX_VALUE, temp = 0;
        int length = len - k;
        for (int i = 0; i < len; i++) {
            temp += cardPoints[i];
            if (i >= length) {
                temp -= cardPoints[i - length];
            }
            if (i >= length - 1)
                min = Math.min(min, temp);
        }
        return sum - min;
    }
}

```

## 1153 String Transforms Into Another String 

### 原题

Given two strings `str1` and `str2` of the same length, determine whether you can transform `str1` into `str2` by doing **zero or more** _conversions_.

In one conversion you can convert **all** occurrences of one character in `str1` to **any** other lowercase English character.

Return `true` if and only if you can transform `str1` into `str2`.

**Example 1:**

```text
Input: str1 = "aabcc", str2 = "ccdee"
Output: true
Explanation: Convert 'c' to 'e' then 'b' to 'd' then 'a' to 'c'. Note that the order of conversions matter.
```

**Example 2:**

```text
Input: str1 = "leetcode", str2 = "codeleet"
Output: false
Explanation: There is no way to transform str1 to str2.
```

**Note:**

1. `1 <= str1.length == str2.length <= 10^4`
2. Both `str1` and `str2` contain only lowercase English letters.

### 思路

题目判断的思路就是遍历字符串，如果在str1中位置i的某个字符前面有相同的字符（位置m），那么就判断str2中对应i位置的字符是否m位置的字符是否完全相等，否则就肯定不能转化

两个特例处理下： 

1、两个字符串完全相同 

2、题目中：每一次转化时，将会一次性将 str1 中出现的 所有 相同字母变成其他 任何 小写英文字母（见示例）。 特别注意这个，表示str1某个字符转化时，转化后的字符在str2中不能存在，这个有些坑 最终判断时，只需要看str2中是否 a ~ z 26个字符都有的情况才不满足，否则总会找到某个特定顺序，满足要求的

### 代码

```java
class Solution {
    public boolean canConvert(String str1, String str2) {
        // 判断2个字符串是否相等
        if (str1.equals(str2)) {
            return true;
        }

        // 确认str2中是否包含 a - > z， 26个字符，如果str2中包含的话，那么str1肯定不满足能转换成"字符"，否则总是可能找到特定的转换顺序
        if (containAllChar(str2)) {
            return false;
        }

        HashMap<Character, Integer> str1Map = new HashMap<>();
        for (int i = 0; i < str1.length(); i++) {
            if (!str1Map.containsKey(str1.charAt(i))) {
                str1Map.put(str1.charAt(i), i);
            } else {
                // 判断str2中，字符不与str1中对应字符不相等，那么肯定不能转化
                if (str2.charAt(i) != str2.charAt(str1Map.get(str1.charAt(i)))) {
                    return false;
                }
            }
        }
        return true;
    }

    private boolean containAllChar(String str1) {
        for (int i = 0; i < 26; i++) {
            if (str1.indexOf('a' + i) == -1) {
                return false;
            }
        }
        return true;
    }
}
```

## 727 Minimum Window Subsequence 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer\#727-minimum-window-subsequence](https://app.gitbook.com/@guilindev/s/interview/leetcode/divide-and-conquer#727-minimum-window-subsequence)

## 489 Robot Room Cleaner 

### 原题

Given a robot cleaner in a room modeled as a grid.

Each cell in the grid can be empty or blocked.

The robot cleaner with 4 given APIs can move forward, turn left or turn right. Each turn it made is 90 degrees.

When it tries to move into a blocked cell, its bumper sensor detects the obstacle and it stays on the current cell.

Design an algorithm to clean the entire room using only the 4 given APIs shown below.

```text
interface Robot {
  // returns true if next cell is open and robot moves into the cell.
  // returns false if next cell is obstacle and robot stays on the current cell.
  boolean move();

  // Robot will stay on the same cell after calling turnLeft/turnRight.
  // Each turn will be 90 degrees.
  void turnLeft();
  void turnRight();

  // Clean the current cell.
  void clean();
}
```

**Example:**

```text
Input:
room = [
  [1,1,1,1,1,0,1,1],
  [1,1,1,1,1,0,1,1],
  [1,0,1,1,1,1,1,1],
  [0,0,0,1,0,0,0,0],
  [1,1,1,1,1,1,1,1]
],
row = 1,
col = 3

Explanation:
All grids in the room are marked by either 0 or 1.
0 means the cell is blocked, while 1 means the cell is accessible.
The robot initially starts at the position of row=1, col=3.
From the top left corner, its position is one row below and three columns right.
```

**Notes:**

1. The input is only given to initialize the room and the robot's position internally. You must solve this problem "blindfolded". In other words, you must control the robot using only the mentioned 4 APIs, without knowing the room layout and the initial robot's position.
2. The robot's initial position will always be in an accessible cell.
3. The initial direction of the robot will be facing up.
4. All accessible cells are connected, which means the all cells marked as 1 will be accessible by the robot.
5. Assume all four edges of the grid are all surrounded by wall.

### 思路

DFS + 回溯

这道题既然是模拟扫地机器人，那么DFS其实是蛮直观的。可以用BFS做吗？不能。因为**BFS做是没法回到原点的**。 这道题大部分的实现都还是比较直观的，需要注意以下几点

* 需要一个hashset记录访问过的坐标 
* 给机器人定义的移动方向是需要有顺序的，要不然是顺时针要不然是逆时针。这里给的是顺时针的做法，上右下左。当机器人在某一个方向走到头的时候，需要让它原地180度掉头，回到初始位置，再去扫描下一个位置

### 代码

```java
/**
 * // This is the robot's control interface.
 * // You should not implement it, or speculate about its implementation
 * interface Robot {
 *     // Returns true if the cell in front is open and robot moves into the cell.
 *     // Returns false if the cell in front is blocked and robot stays in the current cell.
 *     public boolean move();
 *
 *     // Robot will stay in the same cell after calling turnLeft/turnRight.
 *     // Each turn will be 90 degrees.
 *     public void turnLeft();
 *     public void turnRight();
 *
 *     // Clean the current cell.
 *     public void clean();
 * }
 */

class Solution {
    
    private static final int[][] directions = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
    
    public void cleanRoom(Robot robot) {
        clean(robot, 0, 0, 0, new HashSet<>());
    }
    
    private void clean(Robot robot, int x, int y, int curDirection, Set<String> visited) {
        // Cleans current cell.
        robot.clean();
        visited.add(x + " " + y);
        
        for (int nDirection = curDirection; 
             nDirection < curDirection + 4; 
             nDirection++) {
            int nx = directions[nDirection % 4][0] + x;
            int ny = directions[nDirection % 4][1] + y;
            if (!visited.contains(nx + " " + ny) && robot.move()) {
                clean(robot, nx, ny, nDirection % 4, visited);
            }
            // Changed orientation.
            robot.turnRight();
        }
        
        // Moves backward one step while maintaining the orientation.
        robot.turnRight();
        robot.turnRight();
        robot.move();
        robot.turnRight();
        robot.turnRight();
    }
}

```

## 346 Moving Average from Data Stream 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/queue\#346-moving-average-from-data-stream](https://app.gitbook.com/@guilindev/s/interview/leetcode/queue#346-moving-average-from-data-stream)

## 753 Cracking the Safe 

### 原题

There is a box protected by a password. The password is a sequence of `n` digits where each digit can be one of the first `k` digits `0, 1, ..., k-1`.

While entering a password, the last `n` digits entered will automatically be matched against the correct password.

For example, assuming the correct password is `"345"`, if you type `"012345"`, the box will open because the correct password matches the suffix of the entered password.

Return any password of **minimum length** that is guaranteed to open the box at some point of entering it.

**Example 1:**

```text
Input: n = 1, k = 2
Output: "01"
Note: "10" will be accepted too.
```

**Example 2:**

```text
Input: n = 2, k = 2
Output: "00110"
Note: "01100", "10011", "11001" will be accepted too.
```

**Note:**

1. `n` will be in the range `[1, 4]`.
2. `k` will be in the range `[1, 10]`.
3. `k^n` will be at most `4096`.

### 思路

寻找欧拉回路\(Euler Circuit\)

![](../.gitbook/assets/image%20%28133%29.png)

### 代码

```java
import java.util.Collections;
import java.util.TreeSet;

class Solution {
    TreeSet<String> visited;
    StringBuilder res;
    public String crackSafe(int n, int k) {
        if(n == 1 && k == 1) return "0";
        visited = new TreeSet<>();
        res = new StringBuilder();
        //  从顶点 00..0 开始
        String start = String.join("", Collections.nCopies(n-1, "0"));;
        findEuler(start, k);

        res.append(start); // 回路添加最后的end顶点，end 就是 start
        return res.toString(); // return new String(res);
    }
    public void findEuler(String curv, int k){

        for(int i = 0; i < k; i ++){
            // 往顶点的 k 条出边检查，顶点加一条出边就是一种密码可能
            String nextv = curv + i;
            if(!visited.contains(nextv)){
                visited.add(nextv);
                findEuler(nextv.substring(1), k);
                res.append(i);
            }
        }
    }
}
```

## 332 Reconstruct Itinerary

### 原题

Given a list of airline tickets represented by pairs of departure and arrival airports `[from, to]`, reconstruct the itinerary in order. All of the tickets belong to a man who departs from `JFK`. Thus, the itinerary must begin with `JFK`.

**Note:**

1. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary `["JFK", "LGA"]` has a smaller lexical order than `["JFK", "LGB"]`.
2. All airports are represented by three capital letters \(IATA code\).
3. You may assume all tickets form at least one valid itinerary.
4. One must use all the tickets once and only once.

**Example 1:**

```text
Input: [["MUC", "LHR"], ["JFK", "MUC"], ["SFO", "SJC"], ["LHR", "SFO"]]
Output: ["JFK", "MUC", "LHR", "SFO", "SJC"]
```

**Example 2:**

```text
Input: [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
Output: ["JFK","ATL","JFK","SFO","ATL","SFO"]
Explanation: Another possible reconstruction is ["JFK","SFO","ATL","JFK","ATL","SFO"].
             But it is larger in lexical order.
```

### 思路

DFS

![](../.gitbook/assets/image%20%28135%29.png)

![](../.gitbook/assets/image%20%28141%29.png)

![](../.gitbook/assets/image%20%28136%29.png)

### 代码

```java
import java.util.*;
class Solution {
    public List<String> findItinerary(List<List<String>> tickets) {
        // 因为逆序插入，所以用链表
        List<String> ans = new LinkedList<>();
        if (tickets == null || tickets.size() == 0)
            return ans;
        Map<String, List<String>> graph = new HashMap<>();
        for (List<String> pair : tickets) {
            // 因为涉及删除操作，我们用链表
            List<String> nbr = graph.computeIfAbsent(pair.get(0), k -> new LinkedList<>());
            nbr.add(pair.get(1));
        }
        // 按目的顶点排序
        graph.values().forEach(x -> x.sort(String::compareTo));
        visit(graph, "JFK", ans);
        return ans;
    }
    // DFS方式遍历图，当走到不能走为止，再将节点加入到答案
    private void visit(Map<String, List<String>> graph, String src, List<String> ans) {
        List<String> nbr = graph.get(src);
        while (nbr != null && nbr.size() > 0) {
            String dest = nbr.remove(0);
            visit(graph, dest, ans);
        }
        ans.add(0, src); // 逆序插入
    }
}
```

也可以不对临接点排序，而是使用小顶堆（Java可以用优先队列）。这样我们删除边的操作和访问最小字典顺序顶点可以用出队操作代替，时间复杂度应该会比排序再删除要低一些。

```java
import java.util.*;
class Solution {
    public List<String> findItinerary(List<List<String>> tickets) {
        // 因为逆序插入，所以用链表
        List<String> ans = new LinkedList<>();
        if (tickets == null || tickets.size() == 0)
            return ans;
        Map<String, PriorityQueue<String>> graph = new HashMap<>();
        for (List<String> pair : tickets) {
            // 因为涉及删除操作，我们用链表
            PriorityQueue<String> nbr = graph.computeIfAbsent(pair.get(0), k -> new PriorityQueue<>());
            nbr.add(pair.get(1));
        }
        visit(graph, "JFK", ans);
        return ans;
    }
    // DFS方式遍历图，当走到不能走为止，再将节点加入到答案
    private void visit(Map<String, PriorityQueue<String>> graph, String src, List<String> ans) {
        PriorityQueue<String> nbr = graph.get(src);
        while (nbr != null && nbr.size() > 0) {
            String dest = nbr.poll();
            visit(graph, dest, ans);
        }
        ans.add(0, src); // 逆序插入
    }
}
```

## 809 Expressive Words 

### 原题

Sometimes people repeat letters to represent extra feeling, such as "hello" -&gt; "heeellooo", "hi" -&gt; "hiiii".  In these strings like "heeellooo", we have _groups_ of adjacent letters that are all the same:  "h", "eee", "ll", "ooo".

For some given string `S`, a query word is _stretchy_ if it can be made to be equal to `S` by any number of applications of the following _extension_ operation: choose a group consisting of characters `c`, and add some number of characters `c` to the group so that the size of the group is 3 or more.

For example, starting with "hello", we could do an extension on the group "o" to get "hellooo", but we cannot get "helloo" since the group "oo" has size less than 3.  Also, we could do another extension like "ll" -&gt; "lllll" to get "helllllooo".  If `S = "helllllooo"`, then the query word "hello" would be stretchy because of these two extension operations: `query = "hello" -> "hellooo" -> "helllllooo" = S`.

Given a list of query words, return the number of words that are stretchy. 

```text
Example:
Input: 
S = "heeellooo"
words = ["hello", "hi", "helo"]
Output: 1
Explanation: 
We can extend "e" and "o" in the word "hello" to get "heeellooo".
We can't extend "helo" to get "heeellooo" because the group "ll" is not size 3 or more.
```

**Constraints:**

* `0 <= len(S) <= 100`.
* `0 <= len(words) <= 100`.
* `0 <= len(words[i]) <= 100`.
* `S` and all words in `words` consist only of lowercase letters

### 思路

题意：

有时候人们会用重复写一些字母来表示额外的感受，比如 "hello" -&gt; "heeellooo", "hi" -&gt; "hiii"。我们将相邻字母都相同的一串字符定义为相同字母组，例如："h", "eee", "ll", "ooo"。

对于一个给定的字符串 S ，如果另一个单词能够通过将一些字母组扩张从而使其和 S 相同，我们将这个单词定义为可扩张的（stretchy）。扩张操作定义如下：选择一个字母组（包含字母 c ），然后往其中添加相同的字母 c 使其长度达到 3 或以上。

例如，以 "hello" 为例，我们可以对字母组 "o" 扩张得到 "hellooo"，但是无法以同样的方法得到 "helloo" 因为字母组 "oo" 长度小于 3。此外，我们可以进行另一种扩张 "ll" -&gt; "lllll" 以获得 "helllllooo"。如果 S = "helllllooo"，那么查询词 "hello" 是可扩张的，因为可以对它执行这两种扩张操作使得 query = "hello" -&gt; "hellooo" -&gt; "helllllooo" = S。

输入一组查询单词，输出其中可扩张的单词数量。

![](../.gitbook/assets/image%20%28134%29.png)

![](../.gitbook/assets/image%20%28139%29.png)

### 代码

```java
class Solution {
    public int expressiveWords(String s, String[] words) {
        List<Object[]> target = countWord(s);
        int ans = 0;
        for(String word : words){
            List<Object[]> list = countWord(word);
            // 如果两段不相等，跳过
            if(list.size() != target.size())
                continue;
            int i = 0;
            for(; i < target.size(); i++){
                // 该段字符不同，break
                if((char)target.get(i)[0] != (char)list.get(i)[0])
                    break;
                int t1 = (int)target.get(i)[1];
                int t2 = (int)list.get(i)[1];
                // 如果该段字母出现的次数比s还要大，无法扩张，若该条件不满足，此时t1 >= t2，
                // 此时若两者不相等且t1小于3，则s无法通过word扩张得到
                if(t1 < t2 || (t1 != t2 && t1 < 3))
                    break;
            }
            // 如果i走到了最后，则word能够扩张得到s
            if(i == target.size())
                ans++;
        }
        return ans;
    }

    // 统计字符串每段字母及其出现的次数
    // List中存储的是长度为2的数组,o[0]是该段的字母，o[1]是该段该字母出现的频次
    List<Object[]> countWord(String s){
        List<Object[]> list = new ArrayList<>();
        int i = 0;
        while(i < s.length()){
            char c = s.charAt(i);
            int count = 0;
            while(i < s.length() && c == s.charAt(i)){
                count++;
                i++;
            }
            list.add(new Object[]{c, count});
        }
        return list;
    }
}

```

## 471 Encode String with Shortest Length 

### 原题

Given a **non-empty** string, encode the string such that its encoded length is the shortest.

The encoding rule is: `k[encoded_string]`, where the `encoded_string` inside the square brackets is being repeated exactly `k` times.

**Note:**

1. `k` will be a positive integer.
2. If an encoding process does not make the string shorter, then do not encode it. If there are several solutions, return **any of them**.

**Example 1:**

```text
Input: s = "aaa"
Output: "aaa"
Explanation: There is no way to encode it such that it is shorter than the input string, so we do not encode it.
```

**Example 2:**

```text
Input: s = "aaaaa"
Output: "5[a]"
Explanation: "5[a]" is shorter than "aaaaa" by 1 character.
```

**Example 3:**

```text
Input: s = "aaaaaaaaaa"
Output: "10[a]"
Explanation: "a9[a]" or "9[a]a" are also valid solutions, both of them have the same length = 5, which is the same as "10[a]".
```

**Example 4:**

```text
Input: s = "aabcaabcd"
Output: "2[aabc]d"
Explanation: "aabc" occurs twice, so one answer can be "2[aabc]d".
```

**Example 5:**

```text
Input: s = "abbbabbbcabbbabbbc"
Output: "2[2[abbb]c]"
Explanation: "abbbabbbc" occurs twice, but "abbbabbbc" can also be encoded to "2[abbb]c", so one answer can be "2[2[abbb]c]".
```

**Constraints:**

* `1 <= s.length <= 150`
* `s` consists of only lowercase English letters.

### 思路

给定一个 非空 字符串，将其编码为具有最短长度的字符串。编码规则是：k\[encoded\_string\]，其中在方括号 encoded\_string 中的内容重复 k 次。

注：k 为正整数 如果编码的过程不能使字符串缩短，则不要对其进行编码。如果有多种编码方式，返回 任意一种 即可。

![](../.gitbook/assets/image%20%28137%29.png)

### 代码

区间型DP

```java
class Solution {
    String[][] dp;
    public String encode(String s){
        int n = s.length();
        dp = new String[n][n];
        
        for(int len = 1; len <= n; len++){
            for(int i = 0; i + len - 1 < n; i++){
                int j = i + len - 1;
                dp[i][j] = lc459(s, i, j);
                if(len > 4){
                    for(int k = i; k < j; k++){
                        String split = dp[i][k] + dp[k + 1][j];
                        if(dp[i][j].length() > split.length()) dp[i][j] = split;
                    }
                }
            }
        }
        return dp[0][n - 1];
    }

    /**
     * 另 t = s + s, 从下标 1 的字符开始查找字符串s， 找到下标p，
     * 如果p != n, 存在连续重复的子字符串ps = s.substring(0, p), 个数为 n / p
     * 否则， 不存在连续重复子字符串， 无法进行编码
     */
    public String lc459(String s, int i, int j){
        s = s.substring(i, j + 1);
        if(s.length() < 5)  return s;
        int p = (s + s).indexOf(s, 1);
        if(p != s.length()){
            int cnt = s.length() / p;
            return cnt + "[" + dp[i][i + p - 1] + "]";
        }
        //否则， 无法进行编码
        return s;
    }
}
```

## 1110 Delete Nodes And Return Forest 

### 原题

Given the `root` of a binary tree, each node in the tree has a distinct value.

After deleting all nodes with a value in `to_delete`, we are left with a forest \(a disjoint union of trees\).

Return the roots of the trees in the remaining forest.  You may return the result in any order.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/07/01/screen-shot-2019-07-01-at-53836-pm.png)

```text
Input: root = [1,2,3,4,5,6,7], to_delete = [3,5]
Output: [[1,2,null,4],[6],[7]]
```

**Constraints:**

* The number of nodes in the given tree is at most `1000`.
* Each node has a distinct value between `1` and `1000`.
* `to_delete.length <= 1000`
* `to_delete` contains distinct values between `1` and `1000`.

### 思路

删点成林，给出二叉树的根节点 root，树上每个节点都有一个不同的值。如果节点值在 to\_delete 中出现，我们就把该节点从树上删去，最后得到一个森林（一些不相交的树构成的集合）。返回森林中的每棵树。你可以按任意顺序组织答案。

算法

首先把to\_delete变成set，此处不多说； 

节点进入结果当中，有2个条件：

* 不被删除 
* 父节点不存在 

因此在遍历过程中，将parentExists标志传递给子节点，子递归就可以选择是否加入到结果。 另外，如果子节点被删除，父节点的left、right字段需要更新。

### 代码

DFS做后序遍历

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    Set<Integer> toDelete;
    public List<TreeNode> delNodes(TreeNode root, int[] to_delete) {
        toDelete = Arrays.stream(to_delete).boxed().collect(Collectors.toSet());
        List<TreeNode> ans = new ArrayList<>();
        helper(root, ans, false);
        return ans;
    }

    boolean helper(TreeNode root, List<TreeNode> ans, boolean parentExists) {
        boolean del = false;
        if (root == null) {
            return del;
        }
        del = toDelete.contains(root.val);
        if (helper(root.left, ans, !del)) {
            root.left = null;
        }
        if (helper(root.right, ans, !del)) {
            root.right = null;
        }
        if (!del && !parentExists) {
            ans.add(root);
        }
        return del;
    }
}
```

## 777 Swap Adjacent in LR String 

### 原题

In a string composed of `'L'`, `'R'`, and `'X'` characters, like `"RXXLRXRXL"`, a move consists of either replacing one occurrence of `"XL"` with `"LX"`, or replacing one occurrence of `"RX"` with `"XR"`. Given the starting string `start` and the ending string `end`, return `True` if and only if there exists a sequence of moves to transform one string to the other.

**Example 1:**

```text
Input: start = "RXXLRXRXL", end = "XRLXXRRLX"
Output: true
Explanation: We can transform start to end following these steps:
RXXLRXRXL ->
XRXLRXRXL ->
XRLXRXRXL ->
XRLXXRRXL ->
XRLXXRRLX
```

**Example 2:**

```text
Input: start = "X", end = "L"
Output: false
```

**Example 3:**

```text
Input: start = "LLR", end = "RRL"
Output: false
```

**Example 4:**

```text
Input: start = "XL", end = "LX"
Output: true
```

**Example 5:**

```text
Input: start = "XLLR", end = "LXLX"
Output: false
```

**Constraints:**

* `1 <= start.length <= 104`
* `start.length == end.length`
* Both `start` and `end` will only consist of characters in `'L'`, `'R'`, and `'X'`.

### 思路

在一个由 'L' , 'R' 和 'X' 三个字符组成的字符串（例如"RXXLRXRXL"）中进行移动操作。一次移动操作指用一个"LX"替换一个"XL"，或者用一个"XR"替换一个"RX"。现给定起始字符串start和结束字符串end，请编写代码，当且仅当存在一系列移动操作使得start可以转换成end时， 返回True。

可以用双指针来解决这个问题，对于 i， j 两个指针，分别让他们指向 start 和 end，且保证 start\[i\] != 'X'，end\[j\] != 'X'。接下来开始移动指针，如果 start\[i\] != end\[j\]，则不满足 转换不变性，如果 start\[i\] == 'L' 且 i &lt; j，则不满足 可到达性。

* 时间复杂度：O\(N\)，其中NNN 为 `start` 和 `end` 的长度。
* 空间复杂度：O\(1\)O\(1\)O\(1\)。

### 代码

```java
class Solution {
    public boolean canTransform(String start, String end) {
        int N = start.length();
        char[] S = start.toCharArray(), T = end.toCharArray();
        int i = -1, j = -1;
        while (++i < N && ++j < N) {
            while (i < N && S[i] == 'X') i++;
            while (j < N && T[j] == 'X') j++;
            /* At this point, i == N or S[i] != 'X',
               and j == N or T[j] != 'X'.  i and j
               are the indices representing the next
               occurrences of non-X characters in S and T.
            */

            // If only one of i < N and j < N, then it isn't solid-
            // there's more people in one of the strings.
            if ((i < N) ^ (j < N)) return false;

            if (i < N && j < N) {
                // If the person isn't the same, it isn't solid.
                // Or, if the person moved backwards, it isn't accessible.
                if (S[i] != T[j] || (S[i] == 'L' && i < j) ||
                        (S[i] == 'R' && i > j) )
                    return false;
            }
        }
        return true;
    }
}
```

## 659 Split Array into Consecutive Subsequences 

### 原题

Given an array `nums` sorted in ascending order, return `true` if and only if you can split it into 1 or more subsequences such that each subsequence consists of consecutive integers and has length at least 3.

**Example 1:**

```text
Input: [1,2,3,3,4,5]
Output: True
Explanation:
You can split them into two consecutive subsequences : 
1, 2, 3
3, 4, 5
```

**Example 2:**

```text
Input: [1,2,3,3,4,4,5,5]
Output: True
Explanation:
You can split them into two consecutive subsequences : 
1, 2, 3, 4, 5
3, 4, 5
```

**Example 3:**

```text
Input: [1,2,3,4,4,5]
Output: False
```

**Constraints:**

* `1 <= nums.length <= 10000`

### 思路

贪心算法。

**想法**

我们把 3 个或更多的连续数字称作 chain。

我们从左到右考虑每一个数字 x，如果 x 可以被添加到当前的 chain 中，我们将 x 添加到 chain 中，这一定会比创建一个新的 chain 要更好。

为什么呢？如果我们以 x 为起点新创建一个 chain ，这条新创建更短的链是可以接在之前的链上的，这可能会帮助我们避免创建一个从 x 开始的长度为 1 或者 2 的短链。

**算法**

我们将每个数字的出现次数统计好，记 tails\[x\] 是恰好在 x 之前结束的链的数目。

现在我们逐一考虑每个数字，如果有一个链恰好在 x 之前结束，我们将 x 加入此链中。否则，如果我们可以新建立一条链就新建。

我们可以优化额外空间到 O\(1\)O\(1\)O\(1\)，因为我们可以像 方法 1 一样统计数字的出现次数，而且我们只需要知道最后 3 个数字的出现次数即可。

### 代码

```java
class Solution {
    public boolean isPossible(int[] nums) {
        Counter count = new Counter();
        Counter tails = new Counter();
        for (int x: nums) count.add(x, 1);

        for (int x: nums) {
            if (count.get(x) == 0) {
                continue;
            } else if (tails.get(x) > 0) {
                tails.add(x, -1);
                tails.add(x+1, 1);
            } else if (count.get(x+1) > 0 && count.get(x+2) > 0) {
                count.add(x+1, -1);
                count.add(x+2, -1);
                tails.add(x+3, 1);
            } else {
                return false;
            }
            count.add(x, -1);
        }
        return true;
    }
}

class Counter extends HashMap<Integer, Integer> {
    public int get(int k) {
        return containsKey(k) ? super.get(k) : 0;
    }

    public void add(int k, int v) {
        put(k, get(k) + v);
    }
}
```

## 299 Bulls and Cows 

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#299-bulls-and-cows](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#299-bulls-and-cows)

## 1477 Find Two Non-overlapping Sub-arrays Each With Target Sum 

### 原题

Given an array of integers `arr` and an integer `target`.

You have to find **two non-overlapping sub-arrays** of `arr` each with sum equal `target`. There can be multiple answers so you have to find an answer where the sum of the lengths of the two sub-arrays is **minimum**.

Return _the minimum sum of the lengths_ of the two required sub-arrays, or return _**-1**_ if you cannot find such two sub-arrays.

**Example 1:**

```text
Input: arr = [3,2,2,4,3], target = 3
Output: 2
Explanation: Only two sub-arrays have sum = 3 ([3] and [3]). The sum of their lengths is 2.
```

**Example 2:**

```text
Input: arr = [7,3,4,7], target = 7
Output: 2
Explanation: Although we have three non-overlapping sub-arrays of sum = 7 ([7], [3,4] and [7]), but we will choose the first and third sub-arrays as the sum of their lengths is 2.
```

**Example 3:**

```text
Input: arr = [4,3,2,6,2,3,4], target = 6
Output: -1
Explanation: We have only one sub-array of sum = 6.
```

**Example 4:**

```text
Input: arr = [5,5,4,4,5], target = 3
Output: -1
Explanation: We cannot find a sub-array of sum = 3.
```

**Example 5:**

```text
Input: arr = [3,1,1,1,5,1,2,1], target = 3
Output: 3
Explanation: Note that sub-arrays [1,2] and [2,1] cannot be an answer because they overlap.
```

**Constraints:**

* `1 <= arr.length <= 10^5`
* `1 <= arr[i] <= 1000`
* `1 <= target <= 10^8`

### 思路

![](../.gitbook/assets/image%20%28140%29.png)

时间复杂度 O\(n\)，一次扫描

空间复杂度 O\(n\)，用了一个dp数组

### 代码

```java
class Solution {
    public int minSumOfLengths(int[] arr, int target) {
        int n = arr.length;
        int[] dp = new int[n];
        // 注意不能设置为最大值，因为相加会溢出
        Arrays.fill(dp, Integer.MAX_VALUE / 2);

        int ans = Integer.MAX_VALUE;
        for(int i = 0, j = 0, sum = 0; j < n; j++){
            sum += arr[j];
            while(i <= j && sum > target){
                sum -= arr[i++];
            }
            // 找到满足条件的一个区间
            if(sum == target){
                dp[j] = j - i + 1;
                if(i != 0){
                    ans = Math.min(ans, dp[i-1] + j - i + 1);
                }
            }
            if(j != 0)
                dp[j] = Math.min(dp[j], dp[j-1]);
        }

        return ans > arr.length ? -1 : ans;
    }
}

```

## 752 Open the Lock 

### 原题

You have a lock in front of you with 4 circular wheels. Each wheel has 10 slots: `'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'`. The wheels can rotate freely and wrap around: for example we can turn `'9'` to be `'0'`, or `'0'` to be `'9'`. Each move consists of turning one wheel one slot.

The lock initially starts at `'0000'`, a string representing the state of the 4 wheels.

You are given a list of `deadends` dead ends, meaning if the lock displays any of these codes, the wheels of the lock will stop turning and you will be unable to open it.

Given a `target` representing the value of the wheels that will unlock the lock, return the minimum total number of turns required to open the lock, or -1 if it is impossible.

**Example 1:**

```text
Input: deadends = ["0201","0101","0102","1212","2002"], target = "0202"
Output: 6
Explanation:
A sequence of valid moves would be "0000" -> "1000" -> "1100" -> "1200" -> "1201" -> "1202" -> "0202".
Note that a sequence like "0000" -> "0001" -> "0002" -> "0102" -> "0202" would be invalid,
because the wheels of the lock become stuck after the display becomes the dead end "0102".
```

**Example 2:**

```text
Input: deadends = ["8888"], target = "0009"
Output: 1
Explanation:
We can turn the last wheel in reverse to move from "0000" -> "0009".
```

**Example 3:**

```text
Input: deadends = ["8887","8889","8878","8898","8788","8988","7888","9888"], target = "8888"
Output: -1
Explanation:
We can't reach the target without getting stuck.
```

**Example 4:**

```text
Input: deadends = ["0000"], target = "8888"
Output: -1
```

**Constraints:**

* `1 <= deadends.length <= 500`
* `deadends[i].length == 4`
* `target.length == 4`
* target **will not be** in the list `deadends`.
* `target` and `deadends[i]` consist of digits only.

### 思路

BFS，类似111-求二叉树的最小深度。

BFS可以求解最值问题，当每种密码锁每次都转动一次时，总共有8个相邻的密码值，当这8个中有target值时，步数+1并返回，可以理解为树的结构，每个结点都有8个子结点，当从子结点中找到target时，步数+1并返回。所以可以用BFS队列来求解最小次数

### 代码

```java
class Solution {
    public int openLock(String[] deadends, String target) {
        //Set，用来判断密码值是否是死亡数组中的值，这样就不用for循环来遍历了
        Set<String> deads = new HashSet<>();
        for (String s : deadends) deads.add(s);
        //Set，用来判断某一密码值相邻密码值是否重复出现，防止死循环
        Set<String> visited = new HashSet<>();
        //Queue，队列，BFS要用到的，就不用多说了吧
        Queue<String> queue = new LinkedList();
        queue.add("0000");
        visited.add("0000");
        //步数
        int step = 0;

        //当所有密码值的情况都到时结束
        while (!queue.isEmpty()) {
            int len = queue.size();

            //遍历队列中的密码值
            for (int i = 0; i < len; i++) {
                String s = queue.poll();
                //如果死亡数组中包含当前密码值，跳过循环，因为这时密码值已经被锁定，不能进行下一步了
                if (deads.contains(s)) continue;
                if (s.equals(target)) return step;

                //遍历当前密码值的相邻8个密码值并作判断是否重复出现
                for (int j = 0; j < 4; j++) {
                    String up = up(s, j);
                    String down = down(s, j);
                    if (!visited.contains(up)) {
                        queue.add(up);
                        visited.add(up);
                    }
                    if (!visited.contains(down)) {
                        queue.add(down);
                        visited.add(down);
                    }

                }
            }
            //当前树的一层遍历结束，步数+1
            step++;
        }
        //无解，返回-1
        return -1;
    }

    public String up (String s, int j) {
        char[] c = s.toCharArray();
        if (c[j] == '9') {
            c[j] = '0';
        } else {
            c[j]++;
        }
        return new String(c);
    }

    public String down (String s, int j) {
        char[] c = s.toCharArray();
        if (c[j] == '0') {
            c[j] = '9';
        } else {
            c[j]--;
        }
        return new String(c);
    }
}
```

## 444 Sequence Reconstruction 

### 原题

Check whether the original sequence `org` can be uniquely reconstructed from the sequences in `seqs`. The `org` sequence is a permutation of the integers from 1 to n, with 1 ≤ n ≤ 104. Reconstruction means building a shortest common supersequence of the sequences in `seqs` \(i.e., a shortest sequence so that all sequences in `seqs` are subsequences of it\). Determine whether there is only one sequence that can be reconstructed from `seqs` and it is the `org` sequence.

**Example 1:**

```text
Input: org = [1,2,3], seqs = [[1,2],[1,3]]
Output: false
Explanation: [1,2,3] is not the only one sequence that can be reconstructed, because [1,3,2] is also a valid sequence that can be reconstructed.
```

**Example 2:**

```text
Input: org = [1,2,3], seqs = [[1,2]]
Output: false
Explanation: The reconstructed sequence can only be [1,2].
```

**Example 3:**

```text
Input: org = [1,2,3], seqs = [[1,2],[1,3],[2,3]]
Output: true
Explanation: The sequences [1,2], [1,3], and [2,3] can uniquely reconstruct the original sequence [1,2,3].
```

**Example 4:**

```text
Input: org = [4,1,5,2,6,3], seqs = [[5,2,6,3],[4,1,5,2]]
Output: true
```

**Constraints:**

* `1 <= n <= 10^4`
* `org` is a permutation of {1,2,...,n}.
* `1 <= segs[i].length <= 10^5`
* `seqs[i][j]` fits in a 32-bit signed integer.

**UPDATE \(2017/1/8\):**  
 The seqs parameter had been changed to a list of list of strings \(instead of a 2d array of strings\). Please reload the code definition to get the latest changes.

### 思路

思路比较简单，但是出错点实在太多了 基本思路

1. 对输入做筛选 
2. 拓扑排序，同时判断入度为0的是否大于1. 
3. 根据排序结果，判断是否有环 
4. 把排序结果和org比较，查看是否相等。

### 代码

```java
class Solution {
    class EdgeNode {
        public int vertex;
        public List<Integer> next;

        public EdgeNode(int vertex) {
            this.vertex = vertex;
            this.next = new ArrayList<>();
        }
    }
    
    public boolean sequenceReconstruction(int[] org, List<List<Integer>> seqs) {
        if (seqs == null || seqs.size() == 0) return false;
        // 先对输入做筛选
        List<List<Integer>> seqq = new ArrayList<>();
        for (List<Integer> list: seqs) {
            List<Integer> l = new ArrayList<>();
            for (int i : list) {
               if (i>0&&i<=org.length){
                   l.add(i);
               }else {
                   return false;
               }
            }
            seqq.add(l);
        }
        seqs=seqq;
        /**
         * 标准的拓扑排序，判断入度为0的是否大于1.
         */
        int n = org.length;
        EdgeNode[] edgeNodes = new EdgeNode[n + 1];
        int[] inDegree = new int[n + 1];
        Arrays.fill(inDegree, -1);
        ArrayList<Integer> ans = new ArrayList<>();
        for (List<Integer> seq : seqs) {
            if (seq.size()==1){
                int t1 = seq.get(0);
                if (t1>n) return false;
                if (edgeNodes[t1] == null) edgeNodes[t1] = new EdgeNode(t1);
                if (inDegree[t1] == -1) inDegree[t1] = 0;
            }else {
                for (int i = 1; i < seq.size(); i++) {
                    int t1 = seq.get(i - 1);
                    int t2 = seq.get(i);
                    if (edgeNodes[t1] == null) edgeNodes[t1] = new EdgeNode(t1);
                    if (edgeNodes[t2] == null) edgeNodes[t2] = new EdgeNode(t2);
                    edgeNodes[t1].next.add(t2);
                    if (inDegree[t1] == -1) inDegree[t1] = 0;
                    if (inDegree[t2] == -1) inDegree[t2] = 0;
                    inDegree[t2]++;
                }
            }
        }
        ArrayDeque<EdgeNode> deque = new ArrayDeque<>();
        for (int i = 1; i < inDegree.length; i++) {
            if (inDegree[i] == 0) {
                deque.add(edgeNodes[i]);
            }
        }
        int index = 0;//用来记录出队列的元素的个数
        //用队列来判断
        while (!deque.isEmpty()) {
            int size = deque.size();
            // 如果队列中同时存在大于1个的，说明这两个元素顺序可变，则返回失败
            if (size > 1) return false;
            for (int i = 0; i < size; i++) {
                EdgeNode poll = deque.poll();
                ans.add(poll.vertex);
                index++;
                for (int k : poll.next) {
                    inDegree[k]--;
                    if (inDegree[k] == 0) {
                        if (edgeNodes[k] == null) System.out.println("k:" + k);
                        if (edgeNodes[k] != null) deque.add(edgeNodes[k]);
                    }
                }
            }
        }
        // 判断有没有环
        if (index < n) return false;
        for (int i = 0; i < org.length; i++) {
            if (org[i]!=ans.get(i)) return false;
        }
        return true;
    }
}
```

## 951 Flip Equivalent Binary Trees 

### 原题

For a binary tree **T**, we can define a **flip operation** as follows: choose any node, and swap the left and right child subtrees.

A binary tree **X** is _flip equivalent_ to a binary tree **Y** if and only if we can make **X** equal to **Y** after some number of flip operations.

Given the roots of two binary trees `root1` and `root2`, return `true` if the two trees are flip equivelent or `false` otherwise.

**Example 1:** ![Flipped Trees Diagram](https://assets.leetcode.com/uploads/2018/11/29/tree_ex.png)

```text
Input: root1 = [1,2,3,4,5,6,null,null,null,7,8], root2 = [1,3,2,null,6,4,5,null,null,null,null,8,7]
Output: true
Explanation: We flipped at nodes with values 1, 3, and 5.
```

**Example 2:**

```text
Input: root1 = [], root2 = []
Output: true
```

**Example 3:**

```text
Input: root1 = [], root2 = [1]
Output: false
```

**Example 4:**

```text
Input: root1 = [0,null,1], root2 = []
Output: false
```

**Example 5:**

```text
Input: root1 = [0,null,1], root2 = [0,1]
Output: true
```

**Constraints:**

* The number of nodes in each tree is in the range `[0, 100]`.
* Each tree will have **unique node values** in the range `[0, 99]`.

### 思路

1）普通递归

**思路**

如果二叉树 root1，root2 根节点值相等，那么只需要检查他们的孩子是不是相等就可以了。

**算法**

存在三种情况：

* 如果 root1 或者 root2 是 null，那么只有在他们都为 null 的情况下这两个二叉树才等价。 
* 如果 root1，root2 的值不相等，那这两个二叉树的一定不等价。 
* 如果以上条件都不满足，也就是当 root1 和 root2 的值相等的情况下，需要继续判断 root1 的孩子节点是不是跟 root2 的孩子节点相当。因为可以做翻转操作，所以这里有两种情况需要去判断。

时间复杂度： O\(min\(N1,N2\)\)，其中 N1​，N2 分别是二叉树 root1，root2 的大小。

空间复杂度： O\(min\(H1,H2\)\)，其中 H1，H2​ 分别是二叉树 root1， root2 的高度。

2）标准态遍历

让树中所有节点的左孩子都小于右孩子，如果当前不满足就翻转。我们把这种状态的二叉树称为 标准态。所有等价二叉树在转换成标准态后都是完全一样的。

**算法**

用深度优先遍历来对比这两棵树在标准态下是否完全一致。对于两颗等价树，在标准态下遍历的结果一定是一样的。

### 代码

普通递归topdown

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean flipEquiv(TreeNode root1, TreeNode root2) {
        if (root1 == root2)
            return true;
        if (root1 == null || root2 == null || root1.val != root2.val)
            return false;

        return (flipEquiv(root1.left, root2.left) && flipEquiv(root1.right, root2.right) ||
                flipEquiv(root1.left, root2.right) && flipEquiv(root1.right, root2.left));
    }
}

```

标准态

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean flipEquiv(TreeNode root1, TreeNode root2) {
        List<Integer> vals1 = new ArrayList();
        List<Integer> vals2 = new ArrayList();
        dfs(root1, vals1);
        dfs(root2, vals2);
        return vals1.equals(vals2);
    }

    public void dfs(TreeNode node, List<Integer> vals) {
        if (node != null) {
            vals.add(node.val);
            int L = node.left != null ? node.left.val : -1;
            int R = node.right != null ? node.right.val : -1;

            if (L < R) {
                dfs(node.left, vals);
                dfs(node.right, vals);
            } else {
                dfs(node.right, vals);
                dfs(node.left, vals);
            }

            vals.add(null);
        }
    }
}
```

## 1376 Time Needed to Inform All Employees 

### 原题

A company has `n` employees with a unique ID for each employee from `0` to `n - 1`. The head of the company has is the one with `headID`.

Each employee has one direct manager given in the `manager` array where `manager[i]` is the direct manager of the `i-th` employee, `manager[headID] = -1`. Also it's guaranteed that the subordination relationships have a tree structure.

The head of the company wants to inform all the employees of the company of an urgent piece of news. He will inform his direct subordinates and they will inform their subordinates and so on until all employees know about the urgent news.

The `i-th` employee needs `informTime[i]` minutes to inform all of his direct subordinates \(i.e After informTime\[i\] minutes, all his direct subordinates can start spreading the news\).

Return _the number of minutes_ needed to inform all the employees about the urgent news.

**Example 1:**

```text
Input: n = 1, headID = 0, manager = [-1], informTime = [0]
Output: 0
Explanation: The head of the company is the only employee in the company.
```

**Example 2:** ![](https://assets.leetcode.com/uploads/2020/02/27/graph.png)

```text
Input: n = 6, headID = 2, manager = [2,2,-1,2,2,2], informTime = [0,0,1,0,0,0]
Output: 1
Explanation: The head of the company with id = 2 is the direct manager of all the employees in the company and needs 1 minute to inform them all.
The tree structure of the employees in the company is shown.
```

**Example 3:** ![](https://assets.leetcode.com/uploads/2020/02/28/1730_example_3_5.PNG)

```text
Input: n = 7, headID = 6, manager = [1,2,3,4,5,6,-1], informTime = [0,6,5,4,3,2,1]
Output: 21
Explanation: The head has id = 6. He will inform employee with id = 5 in 1 minute.
The employee with id = 5 will inform the employee with id = 4 in 2 minutes.
The employee with id = 4 will inform the employee with id = 3 in 3 minutes.
The employee with id = 3 will inform the employee with id = 2 in 4 minutes.
The employee with id = 2 will inform the employee with id = 1 in 5 minutes.
The employee with id = 1 will inform the employee with id = 0 in 6 minutes.
Needed time = 1 + 2 + 3 + 4 + 5 + 6 = 21.
```

**Example 4:**

```text
Input: n = 15, headID = 0, manager = [-1,0,0,1,1,2,2,3,3,4,4,5,5,6,6], informTime = [1,1,1,1,1,1,1,0,0,0,0,0,0,0,0]
Output: 3
Explanation: The first minute the head will inform employees 1 and 2.
The second minute they will inform employees 3, 4, 5 and 6.
The third minute they will inform the rest of employees.
```

**Example 5:**

```text
Input: n = 4, headID = 2, manager = [3,3,-1,2], informTime = [0,0,162,914]
Output: 1076
```

**Constraints:**

* `1 <= n <= 10^5`
* `0 <= headID < n`
* `manager.length == n`
* `0 <= manager[i] < n`
* `manager[headID] == -1`
* `informTime.length == n`
* `0 <= informTime[i] <= 1000`
* `informTime[i] == 0` if employee `i` has no subordinates.
* It is **guaranteed** that all the employees can be informed.

### 思路

BFS或DFS自底向上。

### 代码

朴素BFS

```java
class Solution {
 
    //定义结点类，表示员工的id和被通知时的时间
    class Node{
        public int id;
        public int time;
        public Node(int i,int time){
            this.id = i;
            this.time = time;
        }
    }
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        int result=0;

        //HashMap索引为员工编号，值为存放下属id的List
        Map<Integer, List<Integer>> map = new HashMap<>();
        for(int i=0;i<n;i++) {
            List<Integer> ls = map.getOrDefault(manager[i],new ArrayList<Integer>());
            ls.add(i);
            map.put(manager[i],ls);
        }

        Queue<Node> q = new LinkedList<>();
        q.offer(new Node(headID,0));
        while(!q.isEmpty()) {
            Node node= q.poll();
            //遍历当前员工的HashMap，将下属的员工加入队列
            for(Integer i:map.getOrDefault(node.id,new ArrayList<>())){
                q.offer(new Node(i,node.time+informTime[node.id]));
            }
            Math.max(node.time,result);
        }
        return result;
    }
}
```

DFS Bottom up

```java
class Solution {
    public int numOfMinutes(int n, int headID, int[] manager, int[] informTime) {
        int[] arr=new int[n];
        int ans=0;
        // 从0开始遍历
        for(int i=0;i<n;i++){
            int index=i;
            int sum=0;
            if(informTime[index]==0)    //表明是叶子节点
                while((index=manager[index])!=-1){  //循环往上找，直到头结点
                    sum+=informTime[index];         //记录每次的和
                    if(sum<=arr[index]) break;      //如果某个叶结点走到这个结点时和小于之前一个叶结点走到这里的和，那么接下来不用走了，因为肯定比之前的小（why? 因为这个类似相交链表，他们后面一段路都是一样的。都是同一个manager）
                    arr[index]=sum;                 //否则就更新这个值
                    ans=Math.max(sum,ans);          //记录这个数组的最大值
                }
        }
        return ans;
    }
}
```

## 715 Range Module 

### 原题

A Range Module is a module that tracks ranges of numbers. Your task is to design and implement the following interfaces in an efficient manner.

`addRange(int left, int right)` Adds the half-open interval `[left, right)`, tracking every real number in that interval. Adding an interval that partially overlaps with currently tracked numbers should add any numbers in the interval `[left, right)` that are not already tracked.

`queryRange(int left, int right)` Returns true if and only if every real number in the interval `[left, right)` is currently being tracked.

`removeRange(int left, int right)` Stops tracking every real number currently being tracked in the interval `[left, right)`.

**Example 1:**  


```text
addRange(10, 20): null
removeRange(14, 16): null
queryRange(10, 14): true (Every number in [10, 14) is being tracked)
queryRange(13, 15): false (Numbers like 14, 14.03, 14.17 in [13, 15) are not being tracked)
queryRange(16, 17): true (The number 16 in [16, 17) is still being tracked, despite the remove operation)
```

**Note:**

A half open interval `[left, right)` denotes all real numbers `left <= x < right`.

`0 < left < right < 10^9` in all calls to `addRange, queryRange, removeRange`.

The total number of calls to `addRange` in a single test case is at most `1000`.

The total number of calls to `queryRange` in a single test case is at most `5000`.

The total number of calls to `removeRange` in a single test case is at most `1000`.

### 思路

使用基于平衡树的集合（TreeSet）ranges 来维护这个数据结构。ranges 内部的区间按照右端点从小到大排序。

* addRange\(\)，removeRange\(\): 和 Python 的实现方法相同，我们遍历 ranges 里的所有区间，找到其中所有与 \[left,right\] 重合的区间。如果需要添加区间 \[left,right\]，就删除这些重合的区间，并将 \[left,right\]添加到 ranges 中。如果需要删除区间，就在删除这些重合的区间的同时记录下出现的新区间，并在删除操作结束后把这 0，1，2 个新区间添加到 ranges 中。
* queryRange\(\)：由于 ranges 是一颗平衡树，我们可以在对数时间复杂度内找出是否有一个区间包含 \[left,right\]。

### 代码

```java
class RangeModule {
    TreeSet<Interval> ranges;
    public RangeModule() {
        ranges = new TreeSet();
    }

    public void addRange(int left, int right) {
        Iterator<Interval> itr = ranges.tailSet(new Interval(0, left - 1)).iterator();
        while (itr.hasNext()) {
            Interval iv = itr.next();
            if (right < iv.left) break;
            left = Math.min(left, iv.left);
            right = Math.max(right, iv.right);
            itr.remove();
        }
        ranges.add(new Interval(left, right));
    }

    public boolean queryRange(int left, int right) {
        Interval iv = ranges.higher(new Interval(0, left));
        return (iv != null && iv.left <= left && right <= iv.right);
    }

    public void removeRange(int left, int right) {
        Iterator<Interval> itr = ranges.tailSet(new Interval(0, left)).iterator();
        ArrayList<Interval> todo = new ArrayList();
        while (itr.hasNext()) {
            Interval iv = itr.next();
            if (right < iv.left) break;
            if (iv.left < left) todo.add(new Interval(iv.left, left));
            if (right < iv.right) todo.add(new Interval(right, iv.right));
            itr.remove();
        }
        for (Interval iv: todo) ranges.add(iv);
    }
}

class Interval implements Comparable<Interval>{
    int left;
    int right;

    public Interval(int left, int right){
        this.left = left;
        this.right = right;
    }

    public int compareTo(Interval that){
        if (this.right == that.right) return this.left - that.left;
        return this.right - that.right;
    }
}

/**
 * Your RangeModule object will be instantiated and called as such:
 * RangeModule obj = new RangeModule();
 * obj.addRange(left,right);
 * boolean param_2 = obj.queryRange(left,right);
 * obj.removeRange(left,right);
 */
```

## 833 Find And Replace in String

### 原题

To some string `S`, we will perform some replacement operations that replace groups of letters with new ones \(not necessarily the same size\).

Each replacement operation has `3` parameters: a starting index `i`, a source word `x` and a target word `y`.  The rule is that if `x` starts at position `i` in the **original** **string** **`S`**, then we will replace that occurrence of `x` with `y`.  If not, we do nothing.

For example, if we have `S = "abcd"` and we have some replacement operation `i = 2, x = "cd", y = "ffff"`, then because `"cd"` starts at position `2` in the original string `S`, we will replace it with `"ffff"`.

Using another example on `S = "abcd"`, if we have both the replacement operation `i = 0, x = "ab", y = "eee"`, as well as another replacement operation `i = 2, x = "ec", y = "ffff"`, this second operation does nothing because in the original string `S[2] = 'c'`, which doesn't match `x[0] = 'e'`.

All these operations occur simultaneously.  It's guaranteed that there won't be any overlap in replacement: for example, `S = "abc", indexes = [0, 1], sources = ["ab","bc"]` is not a valid test case.

**Example 1:**

```text
Input: S = "abcd", indexes = [0,2], sources = ["a","cd"], targets = ["eee","ffff"]
Output: "eeebffff"
Explanation: "a" starts at index 0 in S, so it's replaced by "eee".
"cd" starts at index 2 in S, so it's replaced by "ffff".
```

**Example 2:**

```text
Input: S = "abcd", indexes = [0,2], sources = ["ab","ec"], targets = ["eee","ffff"]
Output: "eeecd"
Explanation: "ab" starts at index 0 in S, so it's replaced by "eee". 
"ec" doesn't starts at index 2 in the original S, so we do nothing.
```

Notes:

1. `0 <= indexes.length = sources.length = targets.length <= 100`
2. `0 < indexes[i] < S.length <= 1000`
3. All characters in given inputs are lowercase letters.

### 思路

直接模拟过程。

在 Java 的代码中，我们根据替换操作得到数组 match，其中 match\[ix\] = j 表示字符串 S 从第 ix 位开始和 sources\[j\] 匹配，并且会被替换成 target\[j\]，也就是说 sources\[j\] 是字符串 S\[ix:\] 的前缀。在得到 match 数组后，我们对 S 从左到右进行扫描，对于每个位置 ix，如果 match\[ix\] 有值 j，那么在 ans 尾部添加字符串 targets\[j\]，并将 ix 增加 sources\[j\].length\(\)；否则在 ans 尾部添加字符 S\[ix\]，并将 ix 增加 1。

时间复杂度：O\(NQ\)，其中 NN 是字符串 S 的长度，QQ 是替换操作的数量。

空间复杂度：O\(N\)，我们认为 sources 和 targets 中的字符串长度均为常数。

### 代码

```java
class Solution {
    public String findReplaceString(String S, int[] indexes, String[] sources, String[] targets) {
        int N = S.length();
        int[] match = new int[N];
        Arrays.fill(match, -1);

        for (int i = 0; i < indexes.length; ++i) {
            int ix = indexes[i];
            if (S.substring(ix, ix + sources[i].length()).equals(sources[i]))
                match[ix] = i;
        }

        StringBuilder ans = new StringBuilder();
        int ix = 0;
        while (ix < N) {
            if (match[ix] >= 0) {
                ans.append(targets[match[ix]]);
                ix += sources[match[ix]].length();
            } else {
                ans.append(S.charAt(ix++));
            }
        }
        return ans.toString();
    }
}

```

## 652 Find Duplicate Subtrees

### 原题

Given the `root` of a binary tree, return all **duplicate subtrees**.

For each kind of duplicate subtrees, you only need to return the root node of any **one** of them.

Two trees are **duplicate** if they have the **same structure** with the **same node values**.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/08/16/e1.jpg)

```text
Input: root = [1,2,3,4,null,2,4,null,null,4]
Output: [[2,4],[4]]
```

**Example 2:**![](https://assets.leetcode.com/uploads/2020/08/16/e2.jpg)

```text
Input: root = [2,1,1]
Output: [[1]]
```

**Example 3:**![](https://assets.leetcode.com/uploads/2020/08/16/e33.jpg)

```text
Input: root = [2,2,2,3,null,3,null]
Output: [[2,3],[3]]
```

**Constraints:**

* The number of the nodes in the tree will be in the range `[1, 10^4]`
* `-200 <= Node.val <= 200`

### 思路

![](../.gitbook/assets/image%20%28165%29.png)

![](../.gitbook/assets/image%20%28151%29.png)

![](../.gitbook/assets/image%20%28177%29.png)

### 代码

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
import java.util.LinkedList;
class Solution {
    public LinkedList<TreeNode> result = new LinkedList<>();
    public HashMap<String,Integer> map = new HashMap<>();
    public List<TreeNode> findDuplicateSubtrees(TreeNode root) {
        if(root == null){
            return result;
        }
        serialize(root);
        return result;
    }

    public String serialize(TreeNode root){
        if(root == null){
            return "null";
        }
        //序列化以当前节点为根节点的二叉树
        String str = serialize(root.left) + ","+ root.val + ","+ serialize(root.right);
        //使用一个HashMap来记录所有的子树的序列
        map.put(str,map.getOrDefault(str,0)+1);
        //当其value为2时，则表示出现了重复结构，将这个子树的根节点放入到结果当中。
        if(map.get(str) == 2){
            result.add(root);
        }
        return str;
    }
}
```

## 846 Hand of Straights

[https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table\#846-hand-of-straights](https://app.gitbook.com/@guilindev/s/interview/leetcode/hash-table#846-hand-of-straights)

## 1296 Divide Array in Sets of K Consecutive Numbers

### 原题

Given an array of integers `nums` and a positive integer `k`, find whether it's possible to divide this array into sets of `k` consecutive numbers  
Return `True` if its possible ****otherwise return `False`.

**Example 1:**

```text
Input: nums = [1,2,3,3,4,4,5,6], k = 4
Output: true
Explanation: Array can be divided into [1,2,3,4] and [3,4,5,6].
```

**Example 2:**

```text
Input: nums = [3,2,1,2,3,4,3,4,5,9,10,11], k = 3
Output: true
Explanation: Array can be divided into [1,2,3] , [2,3,4] , [3,4,5] and [9,10,11].
```

**Example 3:**

```text
Input: nums = [3,3,2,2,1,1], k = 3
Output: true
```

**Example 4:**

```text
Input: nums = [1,2,3,4], k = 3
Output: false
Explanation: Each array should be divided in subarrays of size 3.
```

**Constraints:**

* `1 <= nums.length <= 10^5`
* `1 <= nums[i] <= 10^9`
* `1 <= k <= nums.length`

**Note:** This question is the same as 846: [https://leetcode.com/problems/hand-of-straights/](https://leetcode.com/problems/hand-of-straights/)

### 思路

解法同846 Hand of Straights。

1. 如果数组个数len不是k的倍数，说明不可能划分； 
2. 拿一个Map统计每个元素出现的次数，然后从小到大遍历数组，每次拿第一个count不能0（遍历过程中，若元素被归到前面集合，count要更新的）的元素为起点，看看后面是否能构成k个连续的集合。若一发现不能构成k个连续集合，就立马reture false。 
3. 为了更加快速的结束，可以定义一个集合数，总的需要划分的集合数为 len / klen/k，如果遍历过程中的集合数达到这个总的集合数，就可以立马结束。

### 代码

```java
class Solution {
 public boolean isPossibleDivide(int[] nums, int k) {
        int len = nums.length;

        if (len % k != 0) {
            return false;
        }

        Arrays.sort(nums);
        Map<Integer, Integer> countMap = new HashMap<>();
        for (int num : nums) {
            countMap.put(num, countMap.getOrDefault(num, 0) + 1);
        }

        int needKSetCount = len / k;  // 需要的集合数（每个集合个数为k）
        int kCount = 0;   // 以下遍历过程集合数
        for (int num: nums) {
            int curNumCount = countMap.get(num);
            if (curNumCount == 0) {
                // 等于0说明被归到前面集合中了
                continue;
            }

            countMap.put(num, curNumCount - 1);
            for (int i = 1; i < k; i++) {
                int count = countMap.getOrDefault(num + i, 0);
                if (count == 0) {
                    // 等于0就说明以当前num为起点，找不到k个大小的连续集合
                    return false;
                }

                countMap.put(num + i, count - 1);
            }

            kCount++;
            // 当集合数已经达到需要的集合数，说明已经成功了
            if (kCount == needKSetCount) {
                return true;
            }
        }

        return true;
    }
}
```

## 1548 The Most Similar Path in a Graph 

### 原题

We have `n` cities and `m` bi-directional `roads` where `roads[i] = [ai, bi]` connects city `ai` with city `bi`. Each city has a name consisting of exactly 3 upper-case English letters given in the string array `names`. Starting at any city `x`, you can reach any city `y` where `y != x` \(i.e. the cities and the roads are forming an undirected connected graph\).

You will be given a string array `targetPath`. You should find a path in the graph of the **same length** and with the **minimum edit distance** to `targetPath`.

You need to return _the order of the nodes in the path with the minimum edit distance_, The path should be of the same length of `targetPath` and should be valid \(i.e. there should be a direct road between `ans[i]` and `ans[i + 1]`\). If there are multiple answers return any one of them.

The **edit distance** is defined as follows:

![](https://assets.leetcode.com/uploads/2020/08/08/edit.jpg)

**Follow-up:** If each node can be visited only once in the path, What should you change in your solution?

**Example 1:**![](https://assets.leetcode.com/uploads/2020/08/08/e1.jpg)

```text
Input: n = 5, roads = [[0,2],[0,3],[1,2],[1,3],[1,4],[2,4]], names = ["ATL","PEK","LAX","DXB","HND"], targetPath = ["ATL","DXB","HND","LAX"]
Output: [0,2,4,2]
Explanation: [0,2,4,2], [0,3,0,2] and [0,3,1,2] are accepted answers.
[0,2,4,2] is equivalent to ["ATL","LAX","HND","LAX"] which has edit distance = 1 with targetPath.
[0,3,0,2] is equivalent to ["ATL","DXB","ATL","LAX"] which has edit distance = 1 with targetPath.
[0,3,1,2] is equivalent to ["ATL","DXB","PEK","LAX"] which has edit distance = 1 with targetPath.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2020/08/08/e2.jpg)

```text
Input: n = 4, roads = [[1,0],[2,0],[3,0],[2,1],[3,1],[3,2]], names = ["ATL","PEK","LAX","DXB"], targetPath = ["ABC","DEF","GHI","JKL","MNO","PQR","STU","VWX"]
Output: [0,1,0,1,0,1,0,1]
Explanation: Any path in this graph has edit distance = 8 with targetPath.
```

**Example 3:**

![](https://assets.leetcode.com/uploads/2020/08/09/e3.jpg)

```text
Input: n = 6, roads = [[0,1],[1,2],[2,3],[3,4],[4,5]], names = ["ATL","PEK","LAX","ATL","DXB","HND"], targetPath = ["ATL","DXB","HND","DXB","ATL","LAX","PEK"]
Output: [3,4,5,4,3,2,1]
Explanation: [3,4,5,4,3,2,1] is the only path with edit distance = 0 with targetPath.
It's equivalent to ["ATL","DXB","HND","DXB","ATL","LAX","PEK"]
```

**Constraints:**

* `2 <= n <= 100`
* `m == roads.length`
* `n - 1 <= m <= (n * (n - 1) / 2)`
* `0 <= ai, bi <= n - 1`
* `ai != bi` 
* The graph is guaranteed to be **connected** and each pair of nodes may have **at most one** direct road.
* `names.length == n`
* `names[i].length == 3`
* `names[i]` consists of upper-case English letters.
* There can be two cities with **the same** name.
* `1 <= targetPath.length <= 100`
* `targetPath[i].length == 3`
* `targetPath[i]` consists of upper-case English letters.

### 思路

DFS + memoization

### 代码

```java
class Solution {
    List<List<Integer>> adjMatrix;
    String[] names;
    String[] targetPath;
    int[][] visited;
    int[][] nextChoiceForMin;
    public List<Integer> mostSimilar(int n, int[][] roads, String[] names, String[] targetPath) {
        // INITIALIZE VARIABLES
        this.visited = new int[names.length][targetPath.length];
        this.nextChoiceForMin = new int[names.length][targetPath.length];
        this.targetPath = targetPath;
        this.names = names;
        this.adjMatrix = new ArrayList<List<Integer>>();
       
        for (int[] x : visited) Arrays.fill(x, -1);
        
        // BUILD ADJACENCY MATRIX
        for (int i = 0;i < n; i++) adjMatrix.add(new ArrayList<Integer>());
        for (int[] road : roads) {
            adjMatrix.get(road[0]).add(road[1]);
            adjMatrix.get(road[1]).add(road[0]);
        }
        
        // FROM EACH NODE, CALCULATE MIN COST AND THE CITY THAT GAVE THE MIN COST
        int min = Integer.MAX_VALUE;
        int start = 0;
        for (int i = 0;i < names.length; i++) {
            int resp = dfs(i, 0);
            if (resp < min) {
                min = resp;
                start = i;
            }
        }
        
        // BUILD THE ANSWER BASED ON WHATS THE BEST NEXT CHOICE 
        List<Integer> ans = new ArrayList<Integer>();
        while (ans.size() < targetPath.length) {
            ans.add(start);
            start = nextChoiceForMin[start][ans.size()-1];
        }
        return ans;
    }
    
    public int dfs(int namesIdx, int currPathIdx) {
        // IF WE VISITED IT ALREADY, RETURN THE PREVIOUS COUNT
        if (visited[namesIdx][currPathIdx] != -1) return visited[namesIdx][currPathIdx];
        
        // IF ITS DIFFERENT, ADD 1 ELSE ADD 0.
        int editDist = (names[namesIdx].equals(targetPath[currPathIdx])) ? 0 : 1;
        
        // IF WE FILLED UP THE PATH, WE'RE DONE
        if (currPathIdx == targetPath.length-1) return editDist;
        
        // FROM EACH NEIGHBOR, CALCULATE MIN COST AND SAVE THE CITY THAT GAVE THE MIN COST  
        int min = Integer.MAX_VALUE;
        for (int neighbor : adjMatrix.get(namesIdx)) {
            int neighborCost = dfs(neighbor, currPathIdx+1);
            if (neighborCost < min) {
                min = neighborCost;
                nextChoiceForMin[namesIdx][currPathIdx] = neighbor; // HERE IS WHERE WE SAVE
            }
        }
        
        
        editDist += min; // UPDATE OUR EDIT DISTANCE
        visited[namesIdx][currPathIdx] = editDist; // SAVE OUR EDIT DISTANCE
        return editDist; // RETURN OUR EDIT DISTANCE
    }
}    

/*
                0.     1.   2.   3.   4
  names         [ATL, PEK, LAX, DXB, HND]
    
                           0.      1.         2.         3
   targetPath   ["ATL","DXB","HND","LAX"]
    
    DO THIS STARTING AT EVERY NODE
    ATL at idx = 0 is it different? no, so add to cost + 0 = 0
        3
        DXB at idx = 1 is it different? no, so add to cost + 0 = 0 
            ATL at idx = 2 is it different? yes, so add to cost + 1 = 1
                DXB at idx = 3 is it different? yes, so add to cost + 1 AND RETURN = 2
                LAX at idx = 3 is it different? no, so add to cost + 0 AND RETURN = 1
            PEK at idx = 2 is it different? yes, so add to cost + 1 = 1
                DXB at idx = 3 SOLVED ALREADY RETURN PREV   RETURN 
                LAX at idx = 3 SOLVED ALREADY RETURN PREV
                HND at idx = 3 is it different? yes, so add to cost + 1 AND RETURN
        LAX at idx = 1 is it different? yes, so add to cost + 1
            ATL at idx = 2, SOLVED ALREADY RETURN PREV
            PEK at idx = 2, SOLVED ALREADY RETURN PREV
            HND at idx = 2 is it different? no, so addto cost + 0
                PEK at idx = 3 is it diffferent? yes, so add to cost + 
                LAX at idx = 3 SOLVED ALREADY RETURN PREV
    */
```

## 741 Cherry Pickup

### 题目

In a N x N `grid` representing a field of cherries, each cell is one of three possible integers.

* 0 means the cell is empty, so you can pass through;
* 1 means the cell contains a cherry, that you can pick up and pass through;
* -1 means the cell contains a thorn that blocks your way.

Your task is to collect maximum number of cherries possible by following the rules below:

* Starting at the position \(0, 0\) and reaching \(N-1, N-1\) by moving right or down through valid path cells \(cells with value 0 or 1\);
* After reaching \(N-1, N-1\), returning to \(0, 0\) by moving left or up through valid path cells;
* When passing through a path cell containing a cherry, you pick it up and the cell becomes an empty cell \(0\);
* If there is no valid path between \(0, 0\) and \(N-1, N-1\), then no cherries can be collected.

**Example 1:**

```text
Input: grid =
[[0, 1, -1],
 [1, 0, -1],
 [1, 1,  1]]
Output: 5
Explanation: 
The player started at (0, 0) and went down, down, right right to reach (2, 2).
4 cherries were picked up during this single trip, and the matrix becomes [[0,1,-1],[0,0,-1],[0,0,0]].
Then, the player went left, up, up, left to return home, picking up one more cherry.
The total number of cherries picked up is 5, and this is the maximum possible.
```

**Note:**

* `grid` is an `N` by `N` 2D array, with `1 <= N <= 50`.
* Each `grid[i][j]` is an integer in the set `{-1, 0, 1}`.
* It is guaranteed that grid\[0\]\[0\] and grid\[N-1\]\[N-1\] are not -1.

### 分析

自己摘樱桃。DP 

### 代码

topdown

![](../.gitbook/assets/image%20%28179%29.png)

时间复杂度：O\(N^3\)。其中 NN 是 grid 的长度，动态规划有 O\(N^3\)O\(N 3 \) 的状态 

空间复杂度：O\(N^3\)，memo 所使用的空间。

```java
class Solution {
    int[][][] memo;
    int[][] grid;
    int N;
    public int cherryPickup(int[][] grid) {
        this.grid = grid;
        N = grid.length;
        memo = new int[N][N][N];
        for (int[][] layer: memo)
            for (int[] row: layer)
                Arrays.fill(row, Integer.MIN_VALUE);
        return Math.max(0, dp(0, 0, 0));
    }
    public int dp(int r1, int c1, int c2) {
        int r2 = r1 + c1 - c2;
        if (N == r1 || N == r2 || N == c1 || N == c2 ||
                grid[r1][c1] == -1 || grid[r2][c2] == -1) {
            return -999999;        
        } else if (r1 == N-1 && c1 == N-1) {
            return grid[r1][c1];
        } else if (memo[r1][c1][c2] != Integer.MIN_VALUE) {
            return memo[r1][c1][c2];
        } else {
            int ans = grid[r1][c1];
            if (c1 != c2) ans += grid[r2][c2];
            ans += Math.max(Math.max(dp(r1, c1+1, c2+1), dp(r1+1, c1, c2+1)),
                            Math.max(dp(r1, c1+1, c2), dp(r1+1, c1, c2)));
            memo[r1][c1][c2] = ans;
            return ans;
        }
    }
}
```

bottom up

![](../.gitbook/assets/image%20%28175%29.png)

* 时间复杂度：O\(N^3\)。其中 NN 是 `grid` 的长度。
* 空间复杂度：O\(N^2\)，`dp` 和 `dp2` 所使用的空间

```java
class Solution {
    public int cherryPickup(int[][] grid) {
        int N = grid.length;
        int[][] dp = new int[N][N];
        for (int[] row: dp) Arrays.fill(row, Integer.MIN_VALUE);
        dp[0][0] = grid[0][0];

        for (int t = 1; t <= 2*N - 2; ++t) {
            int[][] dp2 = new int[N][N];
            for (int[] row: dp2) Arrays.fill(row, Integer.MIN_VALUE);

            for (int i = Math.max(0, t-(N-1)); i <= Math.min(N-1, t); ++i) {
                for (int j = Math.max(0, t-(N-1)); j <= Math.min(N-1, t); ++j) {
                    if (grid[i][t-i] == -1 || grid[j][t-j] == -1) continue;
                    int val = grid[i][t-i];
                    if (i != j) val += grid[j][t-j];
                    for (int pi = i-1; pi <= i; ++pi)
                        for (int pj = j-1; pj <= j; ++pj)
                            if (pi >= 0 && pj >= 0)
                                dp2[i][j] = Math.max(dp2[i][j], dp[pi][pj] + val);
                }
            }
            dp = dp2;
        }
        return Math.max(0, dp[N-1][N-1]);
    }
}
```

## 1463 Cherry Pickup II

### 原题

Given a `rows x cols` matrix `grid` representing a field of cherries. Each cell in `grid` represents the number of cherries that you can collect.

You have two robots that can collect cherries for you, Robot \#1 is located at the top-left corner \(0,0\) , and Robot \#2 is located at the top-right corner \(0, cols-1\) of the grid.

Return the maximum number of cherries collection using both robots  by following the rules below:

* From a cell \(i,j\), robots can move to cell \(i+1, j-1\) , \(i+1, j\) or \(i+1, j+1\).
* When any robot is passing through a cell, It picks it up all cherries, and the cell becomes an empty cell \(0\).
* When both robots stay on the same cell, only one of them takes the cherries.
* Both robots cannot move outside of the grid at any moment.
* Both robots should reach the bottom row in the `grid`.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/04/29/sample_1_1802.png)

```text
Input: grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]
Output: 24
Explanation: Path of robot #1 and #2 are described in color green and blue respectively.
Cherries taken by Robot #1, (3 + 2 + 5 + 2) = 12.
Cherries taken by Robot #2, (1 + 5 + 5 + 1) = 12.
Total of cherries: 12 + 12 = 24.
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2020/04/23/sample_2_1802.png)

```text
Input: grid = [[1,0,0,0,0,0,1],[2,0,0,0,0,3,0],[2,0,9,0,0,0,0],[0,3,0,5,4,0,0],[1,0,2,3,0,0,6]]
Output: 28
Explanation: Path of robot #1 and #2 are described in color green and blue respectively.
Cherries taken by Robot #1, (1 + 9 + 5 + 2) = 17.
Cherries taken by Robot #2, (1 + 3 + 4 + 3) = 11.
Total of cherries: 17 + 11 = 28.
```

**Example 3:**

```text
Input: grid = [[1,0,0,3],[0,0,0,3],[0,0,3,3],[9,0,3,3]]
Output: 22
```

**Example 4:**

```text
Input: grid = [[1,1],[1,1]]
Output: 4
```

**Constraints:**

* `rows == grid.length`
* `cols == grid[i].length`
* `2 <= rows, cols <= 70`
* `0 <= grid[i][j] <= 100` 

### 思路

两个机器人摘樱桃

DP

### 代码

```java


class Solution {
    public int cherryPickup(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        // dp[i][j][p]表示当前到达第i行，机器人1在第j列，机器人2在第p列时得最大值
        // 其中：j<=i && p>=m-1-i && j<p 
        // j<=i && p>=m-1-i的原因：比如第0行的中间部分，机器人无法到达
        // j<p 为了让机器人1的路径始终在机器人2的左边。万一有交叉呢？那就把交叉部分互换，反正两个机器人不分你我
        // 使用滚动数组优化
        int[][][] dp = new int[2][m][m];
        dp[0][0][m - 1] = grid[0][0] + grid[0][m - 1];
        int now = 0;
        int[] f = {-1, 0, 1};
        for (int i = 1; i < n; i++) {
            now ^= 1;
            for (int j = 0; j <= i && j < m; j++) {
                for (int p = m - 1; p > j && p >= m - 1 - i; p--) {
                    dp[now][j][p] = 0;
                    int val = grid[i][j] + grid[i][p];
                    for (int x = 0; x < 3; x++) {
                        int fj = j + f[x];
                        if (fj < 0 || fj >= m) {
                            continue;
                        }
                        for (int y = 0; y < 3; y++) {
                            int fp = p + f[y];
                            if (fp < m && fj < fp) {
                                dp[now][j][p] = Math.max(dp[now][j][p], dp[now ^ 1][fj][fp] + val);
                            }
                        }
                    }
                }
            }
        }
        int ans = 0;
        for (int j = 0; j < m; j++) {
            for (int p = j + 1; p < m; p++) {
                ans = Math.max(ans, dp[now][j][p]);
            }
        }
        return ans;
    }
}

```

## 1504 Count Submatrices With All Ones 

### 原题

Given a `rows * columns` matrix `mat` of ones and zeros, return how many **submatrices** have all ones.

**Example 1:**

```text
Input: mat = [[1,0,1],
              [1,1,0],
              [1,1,0]]
Output: 13
Explanation:
There are 6 rectangles of side 1x1.
There are 2 rectangles of side 1x2.
There are 3 rectangles of side 2x1.
There is 1 rectangle of side 2x2. 
There is 1 rectangle of side 3x1.
Total number of rectangles = 6 + 2 + 3 + 1 + 1 = 13.
```

**Example 2:**

```text
Input: mat = [[0,1,1,0],
              [0,1,1,1],
              [1,1,1,0]]
Output: 24
Explanation:
There are 8 rectangles of side 1x1.
There are 5 rectangles of side 1x2.
There are 2 rectangles of side 1x3. 
There are 4 rectangles of side 2x1.
There are 2 rectangles of side 2x2. 
There are 2 rectangles of side 3x1. 
There is 1 rectangle of side 3x2. 
Total number of rectangles = 8 + 5 + 2 + 4 + 2 + 2 + 1 = 24.
```

**Example 3:**

```text
Input: mat = [[1,1,1,1,1,1]]
Output: 21
```

**Example 4:**

```text
Input: mat = [[1,0,1],[0,1,0],[1,0,1]]
Output: 5
```

**Constraints:**

* `1 <= rows <= 150`
* `1 <= columns <= 150`
* `0 <= mat[i][j] <= 1`

### 思路

1）直观暴力枚举

![](../.gitbook/assets/image%20%28162%29.png)

时间复杂度：O\(n^2m\)，其中 nn 为矩阵行数，mm 为矩阵列数。我们预处理 row 数组需要 O\(nm\) 的时间，统计答案的时候一共需要枚举 O\(nm\)个位置，每次枚举的时候需要 O\(n\) 的时间计算，因此时间复杂度为 O\(n^2m\)，故总时间复杂度为 O\(nm+n^2m\)=O\(n^2m\)。 

空间复杂度：O\(nm\)。我们需要 O\(nm\) 的空间来存储 row 数组。

2）单调栈

1. 首先O\(n^2\)的预处理，left\[i\]\[j\]表示\(i,j\)为结束的元素，在一行中，最大能拓展的1的长度。（如其他题解） 
2. 使用单调栈来将O\(m\*n^2\)降低到O\(mn\),可以参考代码中的注释和PPT动图理解。（关键点在与以\(i,j\)为右下角的矩阵构建条件（上一行有效的参与构建的情况，可以模拟一下）） 
3. 为此，首先按列枚举，然后从上到下枚举行，在枚举行的时候，使用单调栈来维护之前已经参与构建矩阵的信息

时间复杂度：O\(nm\)，其中 n 为矩阵行数，m 为矩阵列数。预处理 row 数组需要 O\(nm\) 的时间复杂度，计算答案的时候我们需要对 O\(m\) 列进行统计，每一列统计答案的时候单调栈的时间复杂度为 O\(n\)，因此总时间复杂度为 O\(nm\)。 

空间复杂度：O\(n\)。单调栈最坏情况下需要 O\(n\)的空间。

### 代码

暴力枚举

```java
class Solution {
    public int numSubmat(int[][] mat) {
        int n = mat.length;
        int m = mat[0].length;
        int[][] row = new int[n][m];
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                if (j == 0) {
                    row[i][j] = mat[i][j];
                } else if (mat[i][j] != 0) {
                    row[i][j] = row[i][j - 1] + 1;
                } else {
                    row[i][j] = 0;
                }
            }
        }
        int ans = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                int col = row[i][j];
                for (int k = i; k >= 0 && col != 0; --k) {
                    col = Math.min(col, row[k][j]);
                    ans += col;
                }
            }
        }
        return ans;
    }
}
```

单调栈

```java
class Solution {
    public int numSubmat(int[][] mat) {
        int n = mat.length;
        int m = mat[0].length;
        int[][] left = new int[n + 1][m + 1];
        int res = 0;
        for (int i = 1; i <= n; i++) {
            int now = 0;
            for (int j = 1; j <= m; j++) {
                if (mat[i - 1][j - 1] == 1) now++;
                else now = 0;
                left[i][j] = now;
            }
        }
        //首先按列枚举
        for (int j = 1; j <= m; j++) {
            int to_sum = 0;//每个顶点(i,j)为矩阵右下角的矩阵个数，动态更新。
            //栈里元素int[]=<当前的栈顶元素的向左能过延伸的最长长度，被push进栈的元素中，大于栈顶元素的个数>
            Stack<int[]> stack = new Stack<>();
            for (int i = 1; i <= n; i++) {
                int cnt = 0;//计算当前要被push进栈的left[i][j]之前，栈中有多少元素大于left[i][j]
                while (!stack.isEmpty() && stack.peek()[0] > left[i][j]) {
                    //把栈中永不会参与到(i,j)为右下角元素的构建那些矩阵单元去掉（参考PPT的动态）
                    //去掉的过程相当于对栈中元素进行了修剪对齐（使之单调）（见ppT动图）
                    to_sum -= (stack.peek()[1] + 1) * (stack.peek()[0] - left[i][j]);
                    cnt +=stack.peek()[1]+1; // 大于当前left[i][j] 的栈中元素个数 计数
                    stack.pop();
                }
                to_sum+=left[i][j];
                res+=to_sum;
                stack.push(new int[]{left[i][j],cnt});
            }
        }
        return res;
        
    }
}
```

## 1231 Divide Chocolate 

### 原题

You have one chocolate bar that consists of some chunks. Each chunk has its own sweetness given by the array `sweetness`.

You want to share the chocolate with your `K` friends so you start cutting the chocolate bar into `K+1` pieces using `K` cuts, each piece consists of some **consecutive** chunks.

Being generous, you will eat the piece with the **minimum total sweetness** and give the other pieces to your friends.

Find the **maximum total sweetness** of the piece you can get by cutting the chocolate bar optimally.

**Example 1:**

```text
Input: sweetness = [1,2,3,4,5,6,7,8,9], K = 5
Output: 6
Explanation: You can divide the chocolate to [1,2,3], [4,5], [6], [7], [8], [9]
```

**Example 2:**

```text
Input: sweetness = [5,6,7,8,9,1,2,3,4], K = 8
Output: 1
Explanation: There is only one way to cut the bar into 9 pieces.
```

**Example 3:**

```text
Input: sweetness = [1,2,2,1,2,2,1,2,2], K = 2
Output: 5
Explanation: You can divide the chocolate to [1,2,2], [1,2,2], [1,2,2]
```

**Constraints:**

* `0 <= K < sweetness.length <= 10^4`
* `1 <= sweetness[i] <= 10^5`

### 思路

可以用二分来猜答案，每次试探一个总甜度,假设为x，如果可以找到一种分组方案可以让所有的分组的总甜度都不小于当前二分试探的这个总甜度x，那么就说明当前试探的总甜度x是一个可行解，而且很容易可以想到，所有小于x的甜度肯定也是满足要求的。因此当前x满足要求的话，说明可能还会存在更大的甜度，因此相当于二分中的从大于x的右半部分继续寻找。反之，如果当前总甜度x不满足要求（找不到一种分组方案使得所有的分组的总甜度都不小于x），则说明总甜度x太大了，那么我们就应该往小的找。 是的，这就是二分。

### 代码

```java
class Solution {
    public int maximizeSweetness(int[] arr, int k) {
        // 二分猜答案
        int sum = 0;
        int min = Integer.MAX_VALUE;
        for (int num : arr) {
            sum += num;
            min = Math.min(min, num);
        }

        int low = min;
        int high = sum / (k + 1);
        int ans = 0;
        while (low <= high) {
            int mid = (low + high) >>> 1;
            if (isOk(arr, k + 1, mid)) {
                ans = mid;
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }

        return ans;
    }
    private boolean isOk(int[] arr, int k, int lower) {
        // 划分成k组，每组的和都必须大于lower
        int sum = 0;
        int count = 0;
        for (int num : arr) {
            sum += num;
            if (sum >= lower) {
                count++;
                if (count == k) {
                    return true;
                }
                sum = 0;
            }
        }
        return count >= k;
    }
}
```

## 1345 Jump Game IV

### 原题

Given an array of integers `arr`, you are initially positioned at the first index of the array.

In one step you can jump from index `i` to index:

* `i + 1` where: `i + 1 < arr.length`.
* `i - 1` where: `i - 1 >= 0`.
* `j` where: `arr[i] == arr[j]` and `i != j`.

Return _the minimum number of steps_ to reach the **last index** of the array.

Notice that you can not jump outside of the array at any time.

**Example 1:**

```text
Input: arr = [100,-23,-23,404,100,23,23,23,3,404]
Output: 3
Explanation: You need three jumps from index 0 --> 4 --> 3 --> 9. Note that index 9 is the last index of the array.
```

**Example 2:**

```text
Input: arr = [7]
Output: 0
Explanation: Start index is the last index. You don't need to jump.
```

**Example 3:**

```text
Input: arr = [7,6,9,6,9,6,9,7]
Output: 1
Explanation: You can jump directly from index 0 to index 7 which is last index of the array.
```

**Example 4:**

```text
Input: arr = [6,1,9]
Output: 2
```

**Example 5:**

```text
Input: arr = [11,22,7,7,7,7,7,7,7,22,13]
Output: 3
```

**Constraints:**

* `1 <= arr.length <= 5 * 10^4`
* `-10^8 <= arr[i] <= 10^8`

### 思路

**题意**： 给一个一维的数组，从下标0开始出发，有三种操作：每次可以向左，右移动一位，或者移动到与自己数值相同的位置。

**思路**: 这类搜索求最短步数的解法是**典型的bfs**，注意这题数据的特殊性，**裸bfs会超时**。 这是因为第三个操作枚举所有相同数值的位置时候，如果出现很多 相同值的数值，光枚举相同值时间复杂度会近似O\(n\)，整体就变成O\(n^2\)。

这一步枚举，可以把连续出现相同值的区间只保留左右两个端点，起到搜索剪枝的作用。

### 代码

```java
class Solution {
    final int MAX = Integer.MAX_VALUE;
    public int minJumps(int[] arr) {
        int n = arr.length;
        Queue<Integer> queue = new LinkedList<>();
        Map<Integer, List<Integer>> map = new HashMap<>();
        for(int i=0; i<n; i++){
            int a = arr[i];
            List<Integer> list = map.getOrDefault(a, new ArrayList<>());
            list.add(i);
            map.put(a, list);
        }
        int[] dis = new int[n]; //每个元素到最后一个元素的距离，用来存结果
        Arrays.fill(dis, MAX);
        dis[n-1] = 0;
        queue.offer(n-1);
        boolean[] visited = new boolean[n]; //记录元素是否被访问过，初始都是false
        while(!queue.isEmpty()){
            int x = queue.poll();
            if(x-1>=0 && dis[x-1]==MAX){ //等于MAX说明x左边的元素还没有计算过
                dis[x-1] = dis[x]+1;
                queue.offer(x-1);
            }
            if(x+1<n && dis[x+1]==MAX){
                dis[x+1] = dis[x]+1;
                queue.offer(x+1);
            }
            if(!visited[x]){
                for(int i: map.get(arr[x])){
                    if(dis[i] == MAX){
                        dis[i] = dis[x]+1;
                        queue.offer(i);
                        visited[i] = true;
                    }     
                }
            }         
        }
        return dis[0];
    }
}
```

## 1138 Alphabet Board Path 

### 原题

On an alphabet board, we start at position `(0, 0)`, corresponding to character `board[0][0]`.

Here, `board = ["abcde", "fghij", "klmno", "pqrst", "uvwxy", "z"]`, as shown in the diagram below.

![](https://assets.leetcode.com/uploads/2019/07/28/azboard.png)

We may make the following moves:

* `'U'` moves our position up one row, if the position exists on the board;
* `'D'` moves our position down one row, if the position exists on the board;
* `'L'` moves our position left one column, if the position exists on the board;
* `'R'` moves our position right one column, if the position exists on the board;
* `'!'` adds the character `board[r][c]` at our current position `(r, c)` to the answer.

\(Here, the only positions that exist on the board are positions with letters on them.\)

Return a sequence of moves that makes our answer equal to `target` in the minimum number of moves.  You may return any path that does so.

**Example 1:**

```text
Input: target = "leet"
Output: "DDR!UURRR!!DDD!"
```

**Example 2:**

```text
Input: target = "code"
Output: "RR!DDRR!UUL!R!"
```

**Constraints:**

* `1 <= target.length <= 100`
* `target` consists only of English lowercase letters.

### 思路

坐标计算，左 上 下 右

### 代码

```java
class Solution {

	public String alphabetBoardPath(String target) {
		int x = 0, y = 0, nx = 0, ny = 0;
		StringBuffer ans = new StringBuffer("");
		for (int i = 0; i < target.length(); i++) {
			int temp = target.charAt(i) - 'a' ;
			if (i > 0 && target.charAt(i) == target.charAt(i - 1)) {
				ans.append("!");
			} else {
				nx = temp / 5;
				ny = temp % 5;
				if (ny < y) {
					for (int z = 0; z < y - ny; z++)
						ans.append("L");
				}
				if (nx < x) {
					for (int z = 0; z < x - nx; z++)
						ans.append("U");
				}
				if (nx > x) {
					for (int z = 0; z < nx - x; z++)
						ans.append("D");
				}
				if (ny > y) {
					for (int z = 0; z < ny - y; z++)
						ans.append("R");
				}
				
				ans.append("!");
				x = nx;
				y = ny;
				
			}
		}
		return ans.toString();
	}
}
```

## 562 Longest Line of Consecutive One in Matrix 

### 原题

 Given a 01 matrix **M**, find the longest line of consecutive one in the matrix. The line could be horizontal, vertical, diagonal or anti-diagonal.

**Example:**  


```text
Input:
[[0,1,1,0],
 [0,1,1,0],
 [0,0,0,1]]
Output: 3
```

**Hint:** The number of elements in the given matrix will not exceed 10,000.

### 思路

1）DFS+备忘录

2）二维DP

设 dp\[i\]\[j\] 代表以矩阵第 i 行，第 j 列结尾的连续 1 线段的长度。那么我们需要记录四个 dp 矩阵：横，竖，对角线，以及反对角线分别需要一个 dp 矩阵来记录。

* 横向 dp 矩阵仅需要考虑其左边的连续 1 线段，即 dp\[i\]\[j - 1\]。 
* 纵向 dp 矩阵仅需要考虑其上边的连续 1 线段，即 dp\[i - 1\]\[j\]。 
* 对角线方向 dp 矩阵仅需要考虑其左上的连续 1 线段，即 dp\[i - 1\]\[j - 1\]。 
* 反对角线方向 dp 矩阵仅需要考虑其右上的连续 1 线段，即 dp\[i - 1\]\[j + 1\]。

![](../.gitbook/assets/image%20%28161%29.png)

3\) 状态压缩为以为DP

dp 数组中的每一个位置的值只依赖于上一行。因此不需要将整个矩阵的结果全部存储，只需要保留上一行的结果即可。而对于横向的 dp 数组，由于其不依赖于上一行，上一行的结果也可以不存储。这样可以达到节省空间的效果。

![](../.gitbook/assets/image%20%28173%29.png)

### 代码

二维DP

```java
class Solution {
    public int longestLine(int[][] M) {
        if (M == null || M.length == 0 || M[0].length == 0)
            return 0;
        int ans = 0;
        int[][] horizontal = new int[M.length][M[0].length];
        int[][] vertical = new int[M.length][M[0].length];
        int[][] diagonal = new int[M.length][M[0].length];
        int[][] antidiagonal = new int[M.length][M[0].length];
        for (int i = 0; i != M.length; ++i) {
            for (int j = 0; j != M[0].length; ++j) {
                if (M[i][j] == 0) {
                    horizontal[i][j] = 0;
                    vertical[i][j] = 0;
                    diagonal[i][j] = 0;
                    antidiagonal[i][j] = 0;
                } else {
                    horizontal[i][j] = j > 0 ? horizontal[i][j - 1] + 1 : 1;
                    vertical[i][j] = i > 0 ? vertical[i - 1][j] + 1 : 1;
                    diagonal[i][j] = i > 0 && j > 0 ? diagonal[i - 1][j - 1] + 1 : 1;
                    antidiagonal[i][j] = i > 0 && j < M[0].length - 1 ? antidiagonal[i - 1][j + 1] + 1 : 1;
                    ans = Math.max(ans, horizontal[i][j]);
                    ans = Math.max(ans, vertical[i][j]);
                    ans = Math.max(ans, diagonal[i][j]);
                    ans = Math.max(ans, antidiagonal[i][j]);
                }
            }
        }
        return ans;
    }
}

```

状态压缩为一维

```java
class Solution {
    public int longestLine(int[][] M) {
        if (M == null || M.length == 0 || M[0].length == 0)
            return 0;
        int ans = 0;
        int[] horizontal = new int[M[0].length];
        int[] vertical = new int[M[0].length];
        int[] diagonal = new int[M[0].length];
        int[] antidiagonal = new int[M[0].length];
        for (int i = 0; i != M.length; ++i) {
            int[] vertical_new = new int[M[0].length];
            int[] diagonal_new = new int[M[0].length];
            int[] antidiagonal_new = new int[M[0].length];
            for (int j = 0; j != M[0].length; ++j) {
                if (M[i][j] == 0) {
                    horizontal[j] = 0;
                    vertical_new[j] = 0;
                    diagonal_new[j] = 0;
                    antidiagonal_new[j] = 0;
                } else {
                    horizontal[j] = j > 0 ? horizontal[j - 1] + 1 : 1;
                    vertical_new[j] = i > 0 ? vertical[j] + 1 : 1;
                    diagonal_new[j] = i > 0 && j > 0 ? diagonal[j - 1] + 1 : 1;
                    antidiagonal_new[j] = i > 0 && j < M[0].length - 1 ? antidiagonal[j + 1] + 1 : 1;
                    ans = Math.max(ans, horizontal[j]);
                    ans = Math.max(ans, vertical_new[j]);
                    ans = Math.max(ans, diagonal_new[j]);
                    ans = Math.max(ans, antidiagonal_new[j]);
                }
            }
            vertical = vertical_new;
            diagonal = diagonal_new;
            antidiagonal = antidiagonal_new;
        }
        return ans;
    }
}
```

## 690 Employee Importance

### 原题

You are given a data structure of employee information, which includes the employee's **unique id**, their **importance value** and their **direct** subordinates' id.

For example, employee 1 is the leader of employee 2, and employee 2 is the leader of employee 3. They have importance value 15, 10 and 5, respectively. Then employee 1 has a data structure like \[1, 15, \[2\]\], and employee 2 has \[2, 10, \[3\]\], and employee 3 has \[3, 5, \[\]\]. Note that although employee 3 is also a subordinate of employee 1, the relationship is **not direct**.

Now given the employee information of a company, and an employee id, you need to return the total importance value of this employee and all their subordinates.

**Example 1:**

```text
Input: [[1, 5, [2, 3]], [2, 3, []], [3, 3, []]], 1
Output: 11
Explanation:
Employee 1 has importance value 5, and he has two direct subordinates: employee 2 and employee 3. They both have importance value 3. So the total importance value of employee 1 is 5 + 3 + 3 = 11.
```

**Note:**

1. One employee has at most one **direct** leader and may have several subordinates.
2. The maximum number of employees won't exceed 2000.

### 思路

直观DFS

* 让我们使用 emap = {employee.id -&gt; employee} 快速查询员工。 
* 现在要找出一个员工的总重要性，它将是该员工的重要性，加上该员工每个下属的总重要性。这是一个简单的深度优先搜索。
* 时间复杂度：O\(N\)。其中 N 是员工人数。我们可以在 `DFS` 中查询每个员工。
* 空间复杂度：O\(N\)，计算 `DFS` 时隐式调用堆栈的大小。

### 代码

```java
class Solution {
    Map<Integer, Employee> emap;
    public int getImportance(List<Employee> employees, int queryid) {
        emap = new HashMap();
        for (Employee e: employees) emap.put(e.id, e);
        return dfs(queryid);
    }
    public int dfs(int eid) {
        Employee employee = emap.get(eid);
        int ans = employee.importance;
        for (Integer subid: employee.subordinates)
            ans += dfs(subid);
        return ans;
    }
}
```

## 900 RLE Iterator 

### 原题

Write an iterator that iterates through a run-length encoded sequence.

The iterator is initialized by `RLEIterator(int[] A)`, where `A` is a run-length encoding of some sequence.  More specifically, for all even `i`, `A[i]` tells us the number of times that the non-negative integer value `A[i+1]` is repeated in the sequence.

The iterator supports one function: `next(int n)`, which exhausts the next `n` elements \(`n >= 1`\) and returns the last element exhausted in this way.  If there is no element left to exhaust, `next` returns `-1` instead.

For example, we start with `A = [3,8,0,9,2,5]`, which is a run-length encoding of the sequence `[8,8,8,5,5]`.  This is because the sequence can be read as "three eights, zero nines, two fives".

**Example 1:**

```text
Input: ["RLEIterator","next","next","next","next"], [[[3,8,0,9,2,5]],[2],[1],[1],[2]]
Output: [null,8,8,5,-1]
Explanation: 
RLEIterator is initialized with RLEIterator([3,8,0,9,2,5]).
This maps to the sequence [8,8,8,5,5].
RLEIterator.next is then called 4 times:

.next(2) exhausts 2 terms of the sequence, returning 8.  The remaining sequence is now [8, 5, 5].

.next(1) exhausts 1 term of the sequence, returning 8.  The remaining sequence is now [5, 5].

.next(1) exhausts 1 term of the sequence, returning 5.  The remaining sequence is now [5].

.next(2) exhausts 2 terms, returning -1.  This is because the first term exhausted was 5,
but the second term did not exist.  Since the last term exhausted does not exist, we return -1.

```

**Note:**

1. `0 <= A.length <= 1000`
2. `A.length` is an even integer.
3. `0 <= A[i] <= 10^9`
4. There are at most `1000` calls to `RLEIterator.next(int n)` per test case.
5. Each call to `RLEIterator.next(int n)` will have `1 <= n <= 10^9`.

### 思路

![](../.gitbook/assets/image%20%28160%29.png)

* 时间复杂度：O\(N + Q\)，其中 NN 是数组 `A` 的长度，QQ 是调用函数 `next()` 的次数。
* 空间复杂度：O\(N\)。

### 代码

```java
class RLEIterator {
    int[] A;
    int i, q;

    public RLEIterator(int[] A) {
        this.A = A;
        i = q = 0;
    }

    public int next(int n) {
        while (i < A.length) {
            if (q + n > A[i]) {
                n -= A[i] - q;
                q = 0;
                i += 2;
            } else {
                q += n;
                return A[i+1];
            }
        }

        return -1;
    }
}

/**
 * Your RLEIterator object will be instantiated and called as such:
 * RLEIterator obj = new RLEIterator(A);
 * int param_1 = obj.next(n);
 */
```

## 551 Student Attendance Record I

### 原题

 You are given a string representing an attendance record for a student. The record only contains the following three characters:

1. **'A'** : Absent.
2. **'L'** : Late.
3. **'P'** : Present.

A student could be rewarded if his attendance record doesn't contain **more than one 'A' \(absent\)** or **more than two continuous 'L' \(late\)**.

You need to return whether the student could be rewarded according to his attendance record.

**Example 1:**  


```text
Input: "PPALLP"
Output: True
```

**Example 2:**  


```text
Input: "PPALLL"
Output: False
```

### 思路

解决这个问题最简单的方法就是同济字符串中 AA 的数目并检查 LLL 是否是给定字符串的一个子串。如果 A 的数目比 2 少且 LLL 不是给定字符串的一个子串，那么返回 true，否则返回 false。

Java 中indexOf 方法可以用来检查一个串是否是另一个串的子串。如果找不到子串，那么返回 -1，否则返回这个字符串第一次出现的位置。

```java
public class Solution {
    public boolean checkRecord(String s) {
        int count=0;
        for(int i=0;i<s.length() && count<2 ;i++)
            if(s.charAt(i)=='A')
                count++;
        return count<2 && s.indexOf("LLL")<0; // 当 A 的数目有 22 个的时候就中断循环
    }
}
```

### 代码

不需要 indexOf 的单遍循环方法，统计 AA 的数目并检查子字符串 LLL 是否是一个子串。

```java
public class Solution {
    public boolean checkRecord(String s) {
        int countA = 0;
        for (int i = 0; i < s.length() && countA < 2; i++) {
            if (s.charAt(i) == 'A')
                countA++;
            if (i <= s.length() - 3 && s.charAt(i) == 'L' && s.charAt(i + 1) == 'L' && s.charAt(i + 2) == 'L')
                return false;
        }
        return countA < 2;
    }
}
```

## 552 Student Attendance Record II

### 原题

Given a positive integer **n**, return the number of all possible attendance records with length n, which will be regarded as rewardable. The answer may be very large, return it after mod 109 + 7.

A student attendance record is a string that only contains the following three characters:

1. **'A'** : Absent.
2. **'L'** : Late.
3. **'P'** : Present.

A record is regarded as rewardable if it doesn't contain **more than one 'A' \(absent\)** or **more than two continuous 'L' \(late\)**.

**Example 1:**  


```text
Input: n = 2
Output: 8 
Explanation:
There are 8 records with length 2 will be regarded as rewardable:
"PP" , "AP", "PA", "LP", "PL", "AL", "LA", "LL"
Only "AA" won't be regarded as rewardable owing to more than one absent times. 
```

**Note:** The value of **n** won't exceed 100,000.

### 思路

![](../.gitbook/assets/image%20%28146%29.png)

![](../.gitbook/assets/image%20%28174%29.png)

### 代码

```java
class Solution {
    public int checkRecord(int n) {
        if(n==0)
            return 0;
        if(n==1)
            return 3;
        if(n==2)
            return 8;
        int max=1000000007;
        long [][][] dp = new long[n+1][2][3];
        //有两个数，且A个数为零，结尾不为L  --PP LP 
        dp[2][0][0]=2;
        //有两个数，且A的个数为1，结果不为L  AP 和PA LA
        dp[2][1][0]=3;
        //有两个数，没有A，且结尾为L  --PL
        dp[2][0][1]=1;
        //有两个数，一个A，且结尾为L -- AL
        dp[2][1][1]=1;
        //有两个数，结尾为两个L   ---LL
        dp[2][0][2]=1;
        //有两个数，结尾两个L，有一个A ---无
        dp[2][1][2]=0;
        for(int i=3;i<=n;i++){
            dp[i][0][0]=(dp[i-1][0][0]+dp[i-1][0][2]+dp[i-1][0][1])%max;
            dp[i][1][0]=(dp[i-1][0][0]+dp[i-1][0][1]+dp[i-1][1][1]+dp[i-1][0][2]+dp[i-1][1][2]+dp[i-1][1][0])%max;
            dp[i][0][1]=dp[i-1][0][0]%max;
            dp[i][1][1]=dp[i-1][1][0]%max;
            dp[i][0][2]=dp[i-1][0][1]%max;
            dp[i][1][2]=dp[i-1][1][1]%max;
        }
        return (int)((dp[n][0][0]+dp[n][1][0]+dp[n][0][1]+dp[n][1][1]+dp[n][0][2]+ dp[n][1][2])%max);
    }
}
```

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

## 1240 Tiling a Rectangle with the Fewest Squares

### 原题

Given a rectangle of size `n` x `m`, find the minimum number of integer-sided squares that tile the rectangle.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/10/17/sample_11_1592.png)

```text
Input: n = 2, m = 3
Output: 3
Explanation: 3 squares are necessary to cover the rectangle.
2 (squares of 1x1)
1 (square of 2x2)
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/10/17/sample_22_1592.png)

```text
Input: n = 5, m = 8
Output: 5
```

**Example 3:**

![](https://assets.leetcode.com/uploads/2019/10/17/sample_33_1592.png)

```text
Input: n = 11, m = 13
Output: 6
```

**Constraints:**

* `1 <= n <= 13`
* `1 <= m <= 13`

### 分析

铺瓷砖，房子的客厅大小为 n x m，为保持极简的风格，需要使用尽可能少的 正方形 瓷砖来铺盖地面。

假设正方形瓷砖的规格不限，边长都是整数。最少需要用到多少块方形瓷砖？

![](../.gitbook/assets/image%20%28180%29.png)

时间复杂度： O\(N ^ 4\), 空间复杂度： O\(N ^ 2\)

### 代码

```java
class Solution {
    public int tilingRectangle(int n, int m) {
        int INF = 0x3f3f3f3f;
        int[][] dp = new int[n + 1][m + 1];
        for(int i = 1; i <= n; i++){
            for(int j = 1; j <= m; j++){
                //如果是正方形
                if(i == j){
                    dp[i][j] = 1;
                    continue;
                }
                dp[i][j] = INF;
                //1.横切
                for(int k = 1; k < i; k++){
                    dp[i][j] = Math.min(dp[i][j], dp[k][j] + dp[i - k][j]);
                }
                //2.竖切
                for(int k = 1; k < j; k++){
                    dp[i][j] = Math.min(dp[i][j], dp[i][k] + dp[i][j - k]);
                }
                
                //3.横竖切
                for(int p = 1; p <= Math.min(i, j); p++){
                    for(int k = 1; k <= p; k++){
                        if(p - k <= 0 || j - p <= 0 || i - p + k <= 0 || j - p - k <= 0) continue;
                        dp[i][j] = Math.min(dp[i][j], dp[p - k][j - p] + 
                                                        dp[i - p + k][j - p - k] +
                                                        dp[i - p][p + k] + 2);
                    }
                }
            }
        }
        return dp[n][m];
    }
}
```

## 840 Magic Squares In Grid

### 原题

A `3 x 3` magic square is a `3 x 3` grid filled with distinct numbers **from** `1` **to** `9` such that each row, column, and both diagonals all have the same sum.

Given a `row x col` `grid` of integers, how many `3 x 3` "magic square" subgrids are there?  \(Each subgrid is contiguous\).

**Example 1:**![](https://assets.leetcode.com/uploads/2020/09/11/magic_main.jpg)

```text
Input: grid = [[4,3,8,4],[9,5,1,9],[2,7,6,2]]
Output: 1
Explanation: 
The following subgrid is a 3 x 3 magic square:

while this one is not:

In total, there is only one magic square inside the given grid.
```

**Example 2:**

```text
Input: grid = [[8]]
Output: 0
```

**Example 3:**

```text
Input: grid = [[4,4],[3,3]]
Output: 0
```

**Example 4:**

```text
Input: grid = [[4,7,8],[9,5,1],[2,3,6]]
Output: 0
```

**Constraints:**

* `row == grid.length`
* `col == grid[i].length`
* `1 <= row, col <= 10`
* `0 <= grid[i][j] <= 15`

### 分析

3 x 3 的幻方是一个填充有从 1 到 9 的不同数字的 3 x 3 矩阵，其中每行，每列以及两条对角线上的各数之和都相等。给定一个由整数组成的 grid，其中有多少个 3 × 3 的 “幻方” 子矩阵？（每个子矩阵都是连续的）。

![](../.gitbook/assets/image%20%28166%29.png)

* 时间复杂度：O\(R\*C\)O\(R∗C\)。其中 R, CR,C 指的是给定 `grid` 的行和列。
* 空间复杂度：O\(1\)O\(1\)。

### 代码

```java
class Solution {
    public int numMagicSquaresInside(int[][] grid) {
        int R = grid.length, C = grid[0].length;
        int ans = 0;
        for (int r = 0; r < R-2; ++r)
            for (int c = 0; c < C-2; ++c) {
                if (grid[r+1][c+1] != 5) continue;  // optional skip
                if (magic(grid[r][c], grid[r][c+1], grid[r][c+2],
                          grid[r+1][c], grid[r+1][c+1], grid[r+1][c+2],
                          grid[r+2][c], grid[r+2][c+1], grid[r+2][c+2]))
                    ans++;
            }

        return ans;
    }

    public boolean magic(int... vals) {
        int[] count = new int[16];
        for (int v: vals) count[v]++;
        for (int v = 1; v <= 9; ++v)
            if (count[v] != 1)
                return false;

        return (vals[0] + vals[1] + vals[2] == 15 &&
                vals[3] + vals[4] + vals[5] == 15 &&
                vals[6] + vals[7] + vals[8] == 15 &&
                vals[0] + vals[3] + vals[6] == 15 &&
                vals[1] + vals[4] + vals[7] == 15 &&
                vals[2] + vals[5] + vals[8] == 15 &&
                vals[0] + vals[4] + vals[8] == 15 &&
                vals[2] + vals[4] + vals[6] == 15);
    }
}
```

## 527 Word Abbreviation

### 原题

Given an array of n distinct non-empty strings, you need to generate **minimal** possible abbreviations for every word following rules below.

1. Begin with the first character and then the number of characters abbreviated, which followed by the last character.
2. If there are any conflict, that is more than one words share the same abbreviation, a longer prefix is used instead of only the first character until making the map from word to abbreviation become unique. In other words, a final abbreviation cannot map to more than one original words.
3. If the abbreviation doesn't make the word shorter, then keep it as original.

**Example:**  


```text
Input: ["like", "god", "internal", "me", "internet", "interval", "intension", "face", "intrusion"]
Output: ["l2e","god","internal","me","i6t","interval","inte4n","f2e","intr4n"]
```

**Note:**

1. Both n and the length of each word will not exceed 400.
2. The length of each word is greater than 1.
3. The words consist of lowercase English letters only.
4. The return answers should be **in the same order** as the original array.

给定一个由n个不重复非空字符串组成的数组，你需要按照以下规则为每个单词生成最小的缩写。

初始缩写由起始字母+省略字母的数量+结尾字母组成。 若存在冲突，亦即多于一个单词有同样的缩写，则使用更长的前缀代替首字母，直到从单词到缩写的映射唯一。换而言之，最终的缩写必须只能映射到一个单词。 若缩写并不比原单词更短，则保留原样。 示例:

输入: \["like", "god", "internal", "me", "internet", "interval", "intension", "face", "intrusion"\] 

输出: \["l2e","god","internal","me","i6t","interval","inte4n","f2e","intr4n"\]

注意:

n和每个单词的长度均不超过 400。 每个单词的长度大于 1。 单词只由英文小写字母组成。 返回的答案需要和原数组保持同一顺序。

### 分析

单词缩写

1\) 贪心

![](../.gitbook/assets/image%20%28163%29.png)

* 时间复杂度：O\(C^2\)。其中 C 是给定数组中所有字符串的字符总数目。
* 空间复杂度：O\(C\)

2）

![](../.gitbook/assets/image%20%28158%29.png)

* 时间复杂度： O\(ClogC\)，其中 C 是给定数组所有单词的总字符数。该算法时间主要耗费在排序上。
* 空间复杂度： O\(C\)

3\) 

![](../.gitbook/assets/image%20%28147%29.png)

* 时间复杂度：O\(C\)，其中 C 是给定数组中所有单词的字符总数。
* 空间复杂度：O\(C\)。

### 代码

1\) 贪心

```java
class Solution {
    public List<String> wordsAbbreviation(List<String> words) {
        int N = words.size();
        String[] ans = new String[N];
        int[] prefix = new int[N];

        for (int i = 0; i < N; ++i)
            ans[i] = abbrev(words.get(i), 0);

        for (int i = 0; i < N; ++i) {
            while (true) {
                Set<Integer> dupes = new HashSet();
                for (int j = i+1; j < N; ++j)
                    if (ans[i].equals(ans[j]))
                        dupes.add(j);

                if (dupes.isEmpty()) break;
                dupes.add(i);
                for (int k: dupes)
                    ans[k] = abbrev(words.get(k), ++prefix[k]);
            }
        }

        return Arrays.asList(ans);
    }

    public String abbrev(String word, int i) {
        int N = word.length();
        if (N - i <= 3) return word;
        return word.substring(0, i+1) + (N - i - 2) + word.charAt(N-1);
    }
}
```

2） 分组 + 最短公共前缀

```java
class Solution {
    public List<String> wordsAbbreviation(List<String> words) {
        Map<String, List<IndexedWord>> groups = new HashMap();
        String[] ans = new String[words.size()];

        int index = 0;
        for (String word: words) {
            String ab = abbrev(word, 0);
            if (!groups.containsKey(ab))
                groups.put(ab, new ArrayList());
            groups.get(ab).add(new IndexedWord(word, index));
            index++;
        }

        for (List<IndexedWord> group: groups.values()) {
            Collections.sort(group, (a, b) -> a.word.compareTo(b.word));

            int[] lcp = new int[group.size()];
            for (int i = 1; i < group.size(); ++i) {
                int p = longestCommonPrefix(group.get(i-1).word, group.get(i).word);
                lcp[i] = p;
                lcp[i-1] = Math.max(lcp[i-1], p);
            }

            for (int i = 0; i < group.size(); ++i)
                ans[group.get(i).index] = abbrev(group.get(i).word, lcp[i]);
        }

        return Arrays.asList(ans);
    }

    public String abbrev(String word, int i) {
        int N = word.length();
        if (N - i <= 3) return word;
        return word.substring(0, i+1) + (N - i - 2) + word.charAt(N-1);
    }

    public int longestCommonPrefix(String word1, String word2) {
        int i = 0;
        while (i < word1.length() && i < word2.length()
                && word1.charAt(i) == word2.charAt(i))
            i++;
        return i;
    }
}

class IndexedWord {
    String word;
    int index;
    IndexedWord(String w, int i) {
        word = w;
        index = i;
    }
}
```

3） 分组 + Trie 树

```java
class Solution {
    public List<String> wordsAbbreviation(List<String> words) {
        Map<String, List<IndexedWord>> groups = new HashMap();
        String[] ans = new String[words.size()];

        int index = 0;
        for (String word: words) {
            String ab = abbrev(word, 0);
            if (!groups.containsKey(ab))
                groups.put(ab, new ArrayList());
            groups.get(ab).add(new IndexedWord(word, index));
            index++;
        }

        for (List<IndexedWord> group: groups.values()) {
            TrieNode trie = new TrieNode();
            for (IndexedWord iw: group) {
                TrieNode cur = trie;
                for (char letter: iw.word.substring(1).toCharArray()) {
                    if (cur.children[letter - 'a'] == null)
                        cur.children[letter - 'a'] = new TrieNode();
                    cur.count++;
                    cur = cur.children[letter - 'a'];
                }
            }

            for (IndexedWord iw: group) {
                TrieNode cur = trie;
                int i = 1;
                for (char letter: iw.word.substring(1).toCharArray()) {
                    if (cur.count == 1) break;
                    cur = cur.children[letter - 'a'];
                    i++;
                }
                ans[iw.index] = abbrev(iw.word, i-1);
            }
        }

        return Arrays.asList(ans);
    }

    public String abbrev(String word, int i) {
        int N = word.length();
        if (N - i <= 3) return word;
        return word.substring(0, i+1) + (N - i - 2) + word.charAt(N-1);
    }

    public int longestCommonPrefix(String word1, String word2) {
        int i = 0;
        while (i < word1.length() && i < word2.length()
                && word1.charAt(i) == word2.charAt(i))
            i++;
        return i;
    }
}

class TrieNode {
    TrieNode[] children;
    int count;
    TrieNode() {
        children = new TrieNode[26];
        count = 0;
    }
}
class IndexedWord {
    String word;
    int index;
    IndexedWord(String w, int i) {
        word = w;
        index = i;
    }
}

```

## 1368 Minimum Cost to Make at Least One Valid Path in a Grid

### 原题

 Given a _m_ x _n_ `grid`. Each cell of the `grid` has a sign pointing to the next cell you should visit if you are currently in this cell. The sign of `grid[i][j]` can be:

* **1** which means go to the cell to the right. \(i.e go from `grid[i][j]` to `grid[i][j + 1]`\)
* **2** which means go to the cell to the left. \(i.e go from `grid[i][j]` to `grid[i][j - 1]`\)
* **3** which means go to the lower cell. \(i.e go from `grid[i][j]` to `grid[i + 1][j]`\)
* **4** which means go to the upper cell. \(i.e go from `grid[i][j]` to `grid[i - 1][j]`\)

Notice that there could be some **invalid signs** on the cells of the `grid` which points outside the `grid`.

You will initially start at the upper left cell `(0,0)`. A valid path in the grid is a path which starts from the upper left cell `(0,0)` and ends at the bottom-right cell `(m - 1, n - 1)` following the signs on the grid. The valid path **doesn't have to be the shortest**.

You can modify the sign on a cell with `cost = 1`. You can modify the sign on a cell **one time only**.

Return _the minimum cost_ to make the grid have at least one valid path.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/02/13/grid1.png)

```text
Input: grid = [[1,1,1,1],[2,2,2,2],[1,1,1,1],[2,2,2,2]]
Output: 3
Explanation: You will start at point (0, 0).
The path to (3, 3) is as follows. (0, 0) --> (0, 1) --> (0, 2) --> (0, 3) change the arrow to down with cost = 1 --> (1, 3) --> (1, 2) --> (1, 1) --> (1, 0) change the arrow to down with cost = 1 --> (2, 0) --> (2, 1) --> (2, 2) --> (2, 3) change the arrow to down with cost = 1 --> (3, 3)
The total cost = 3.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2020/02/13/grid2.png)

```text
Input: grid = [[1,1,3],[3,2,2],[1,1,4]]
Output: 0
Explanation: You can follow the path from (0, 0) to (2, 2).
```

**Example 3:**![](https://assets.leetcode.com/uploads/2020/02/13/grid3.png)

```text
Input: grid = [[1,2],[4,3]]
Output: 1
```

**Example 4:**

```text
Input: grid = [[2,2,2],[2,2,2]]
Output: 3
```

**Example 5:**

```text
Input: grid = [[4]]
Output: 0
```

**Constraints:**

* `m == grid.length`
* `n == grid[i].length`
* `1 <= m, n <= 100`

### 分析

BFS+记忆搜索 

这题是求最短距离的变种，按最短距离的bfs解法来写。 

在这题中求的最小cost可以当作最短距离，只是这个cost的算法不太一样，当我们使用bfs时向上下左右四个方向扩展，向网络所指方向扩展则cost不变，往其他方向 则cost+1，遍历过程中使用二维数组dst来保存由\(0, 0\)到其他网格的最小花费。

### 代码

```java
import java.util.Arrays;
import java.util.LinkedList;
import java.util.Queue;

class Solution {

    public int minCost(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        //由(0, 0)到其他网格的最小花费，为-1则表示待计算
        int dst[][] = new int[n][m];
        //用来保存待扩展的四个方向在纵轴和横轴上的增量，右-左-下-上
        int d[][] = {{}, {0, 1}, {0, -1}, {1, 0}, {-1, 0}};
        for (int i = 0; i < n; i++) {
            Arrays.fill(dst[i], -1);
        }
        //用来执行bfs的队列
        Queue<int[]> queue = new LinkedList<>();
        //int数组三个参数：纵轴、横轴、当前cost
        queue.offer(new int[] {0, 0, 0});
        while (!queue.isEmpty()) {
            int x = queue.size();
            for (int i = 0; i < x; i++) {
                int q[] = queue.poll();
                if (q[0] == n - 1 && q[1] == m - 1) {
                    continue;
                }
                int val = grid[q[0]][q[1]];
                for (int j = 1; j <= 4; j++) {
                    int r = q[0] + d[j][0];
                    int c = q[1] + d[j][1];
                    if (r >= 0 && c >= 0 && r < n && c < m) {
                        int add = j == val ? 0 : 1;
                        if (dst[r][c] == -1 || dst[r][c] > q[2] + add) {
                            dst[r][c] = q[2] + add;
                            queue.offer(new int[] {r, c, dst[r][c]});
                        }
                    }
                }
            }
        }
        return Math.max(0, dst[n - 1][m - 1]);
    }
}
```

## 1444 Number of Ways of Cutting a Pizza

### 原题

给你一个 rows x cols 大小的矩形披萨和一个整数 k ，矩形包含两种字符： 'A' （表示苹果）和 '.' （表示空白格子）。你需要切披萨 k-1 次，得到 k 块披萨并送给别人。

切披萨的每一刀，先要选择是向垂直还是水平方向切，再在矩形的边界上选一个切的位置，将披萨一分为二。如果垂直地切披萨，那么需要把左边的部分送给一个人，如果水平地切，那么需要把上面的部分送给一个人。在切完最后一刀后，需要把剩下来的一块送给最后一个人。

请你返回确保每一块披萨包含 至少 一个苹果的切披萨方案数。由于答案可能是个很大的数字，请你返回它对 10^9 + 7 取余的结果。

Given a rectangular pizza represented as a `rows x cols` matrix containing the following characters: `'A'` \(an apple\) and `'.'` \(empty cell\) and given the integer `k`. You have to cut the pizza into `k` pieces using `k-1` cuts. 

For each cut you choose the direction: vertical or horizontal, then you choose a cut position at the cell boundary and cut the pizza into two pieces. If you cut the pizza vertically, give the left part of the pizza to a person. If you cut the pizza horizontally, give the upper part of the pizza to a person. Give the last piece of pizza to the last person.

_Return the number of ways of cutting the pizza such that each piece contains **at least** one apple._ Since the answer can be a huge number, return this modulo 10^9 + 7.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/04/23/ways_to_cut_apple_1.png)

```text
Input: pizza = ["A..","AAA","..."], k = 3
Output: 3 
Explanation: The figure above shows the three ways to cut the pizza. Note that pieces must contain at least one apple.
```

**Example 2:**

```text
Input: pizza = ["A..","AA.","..."], k = 3
Output: 1
```

**Example 3:**

```text
Input: pizza = ["A..","A..","..."], k = 1
Output: 1
```

**Constraints:**

* `1 <= rows, cols <= 50`
* `rows == pizza.length`
* `cols == pizza[i].length`
* `1 <= k <= 10`
* `pizza` consists of characters `'A'` and `'.'` only.

### 分析

DP

想着最后总会剩下右下角的区域.. 而且能不能切开,还要判断一下切开的区域有没有水果. 所以先对处理出了一个map数组... map\[i\]\[j\] 表示从i,j开始到最后的区域里面 有多少的水果 map\[i\]\[j\] = map\[i + 1\]\[j\] + map\[i\]\[j+1\] - map\[i+1\]\[j+1\];

然后就定义一个 dp\[k\]\[i\]\[j\] 切了k次 剩下 区域是 i,j 的可行方案有多少种 在方案能行的情况下 dp\[n\]\[i\]\[j\] 有2个来源.. 最后一道横着切 . 或者竖着切 也就是 i 不变 或者 j不变 只变 一个 横着切 dp\[n\]\[i\]\[j\] = \(dp\[n-1\]\[0\]\[j\] + dp\[n-1\]\[1\]\[j\] + dp\[n-1\]\[2\]\[j\] +...dp\[n-1\]\[i-1\]\[j\]\); 如果方案可行的话 同理竖着切....

最后结果就是 dp\[k-1\]\[\]\[\] 的所有值...

### 代码

```java
class Solution {
    public final int MOD = 1000000007;

    public int ways(String[] pizza, int k) {
        int rol = pizza.length;
        int col = pizza[0].length();
        int[][] map = new int[rol + 1][col + 1];
        for (int i = rol - 1; i >= 0; i--) {
            for (int j = col - 1; j >= 0; j--) {
                map[i][j] = map[i + 1][j] + map[i][j+1] - map[i+1][j+1];
                if (pizza[i].charAt(j) == 'A') {
                    map[i][j]++;
                }
            }
        }
        int[][][] dp = new int[k][rol][col];
        dp[0][0][0] = 1;
        for (int n = 1; n < k; n++) {   //切n次
            for (int i = 0; i < rol; i++) {
                for (int j = 0; j < col; j++) {
                    //剩下i , j
                    int count = 0;
                    for (int l = 0; l < i; l++) {   //j不变
                        int flag = map[l][j] - map[i][j];
                        if (flag != 0 && map[i][j] != 0) {
                            count += dp[n - 1][l][j];
                            count %= MOD;
                        }
                    }
                    for (int l = 0; l < j; l++) {
                        int flag = map[i][l] - map[i][j];
                        if (flag != 0 && map[i][j] != 0) {
                            count += dp[n - 1][i][l];
                            count %= MOD;
                        }
                    }
                    dp[n][i][j] = count;
                }
            }
        }
        int res = 0;
        for (int i = 0; i < rol; i++) {
            for (int j = 0; j < col; j++) {
                res += dp[k - 1][i][j];
                res %= MOD;
            }
        }
        return res;
    }
}
```

## 465 Optimal Account Balancing

### 原题

最优账单平衡，一群朋友在度假期间会相互借钱。比如说，小爱同学支付了小新同学的午餐共计 10 美元。如果小明同学支付了小爱同学的出租车钱共计 5 美元。我们可以用一个三元组 \(x, y, z\) 表示一次交易，表示 x 借给 y 共计 z 美元。用 0, 1, 2 表示小爱同学、小新同学和小明同学（0, 1, 2 为人的标号），上述交易可以表示为 \[\[0, 1, 10\], \[2, 0, 5\]\]。

给定一群人之间的交易信息列表，计算能够还清所有债务的最小次数。

注意：

一次交易会以三元组 \(x, y, z\) 表示，并有 x ≠ y 且 z &gt; 0。 人的标号可能不是按顺序的，例如标号可能为 0, 1, 2 也可能为 0, 2, 6。

A group of friends went on holiday and sometimes lent each other money. For example, Alice paid for Bill's lunch for $10. Then later Chris gave Alice $5 for a taxi ride. We can model each transaction as a tuple \(x, y, z\) which means person x gave person y $z. Assuming Alice, Bill, and Chris are person 0, 1, and 2 respectively \(0, 1, 2 are the person's ID\), the transactions can be represented as `[[0, 1, 10], [2, 0, 5]]`.

Given a list of transactions between a group of people, return the minimum number of transactions required to settle the debt.

**Note:**

1. A transaction will be given as a tuple \(x, y, z\). Note that `x ≠ y` and `z > 0`.
2. Person's IDs may not be linear, e.g. we could have the persons 0, 1, 2 or we could also have the persons 0, 2, 6.

**Example 1:**

```text
Input:
[[0,1,10], [2,0,5]]

Output:
2

Explanation:
Person #0 gave person #1 $10.
Person #2 gave person #0 $5.

Two transactions are needed. One way to settle the debt is person #1 pays person #0 and #2 $5 each.
```

**Example 2:**

```text
Input:
[[0,1,10], [1,0,1], [1,2,5], [2,0,5]]

Output:
1

Explanation:
Person #0 gave person #1 $10.
Person #1 gave person #0 $1.
Person #1 gave person #2 $5.
Person #2 gave person #0 $5.

Therefore, person #1 only need to give person #0 $4, and all debt is settled.
```

### 分析

DFS

先把所有人的钱的正负都放到一个数组里。 然后从第一个人开始，找到钱数的正负和他不一样的人，试图把第一个人的钱都放到另外一个人身上。 然后for循环第二个人一直到第n个

### 代码

```java
class Solution {
    int[] acc;
    int res = Integer.MAX_VALUE;
    int n;
    public int minTransfers(int[][] transactions) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < transactions.length; i ++) {
            int first = transactions[i][0];
            int second = transactions[i][1];
            int amount = transactions[i][2];
            map.put(first, map.getOrDefault(first, 0) - amount);
            map.put(second, map.getOrDefault(second, 0) + amount);
        }
        acc = map.values().stream().mapToInt(i -> i).toArray();
        this.n = acc.length;        
        helper(0, 0);
        return res;
    }

    void helper(int start, int cnt) {
        while (start < n && acc[start] == 0) {
            start ++;
        }
        if (start == n) {
            res = Math.min(res, cnt);
            return;
        }
        for (int i = start + 1; i < n; i ++) {
            if (acc[i] < 0 && acc[start] > 0 || acc[start] < 0 && acc[i] > 0) {
                acc[i] += acc[start];
                helper(start + 1, cnt + 1);
                acc[i] -= acc[start];
            }
        }
    }
}
```

## 679 24 Game

### 原题

 你有 4 张写有 1 到 9 数字的牌。你需要判断是否能通过 `*`，`/`，`+`，`-`，`(`，`)` 的运算得到 24。

You have 4 cards each containing a number from 1 to 9. You need to judge whether they could operated through `*`, `/`, `+`, `-`, `(`, `)` to get the value of 24.

**Example 1:**  


```text
Input: [4, 1, 8, 7]
Output: True
Explanation: (8-4) * (7-1) = 24
```

**Example 2:**  


```text
Input: [1, 2, 1, 2]
Output: False
```

**Note:**  


1. The division operator `/` represents real division, not integer division. For example, 4 / \(1 - 2/3\) = 12.
2. Every operation done is between two numbers. In particular, we cannot use `-` as a unary operator. For example, with `[1, 1, 1, 1]` as input, the expression `-1 - 1 - 1 - 1` is not allowed.
3. You cannot concatenate numbers together. For example, if the input is `[1, 2, 1, 2]`, we cannot write this as 12 + 12.

### 分析

我们第一步肯定是挑出两个数，算出一个数，替代它们俩。

然后在三个数中玩 24 点，再挑出两个数，算出一个数替代它们。

然后在两个数中玩 24 点……

这就有了递归的思路。

挑出不同的两数组合，需要两层循环。

递归函数里：

* 我们枚举出所有可能的运算操作——（对应递归树的不同分支） 
* 逐个尝试一种运算——（选择进入一个分支） 
* 传入长度更小的新数组继续递归——（递归计算当前子树（子问题）） 
* 当做到只剩一个数时——（到达了递归树的底部），看看是不是24。 
  * 是就返回true——（结束当前递归，并且控制它不进入别的分支）
  *  否则返回false，离开错误的分支，进入别的递归分支，尝试别的运算。 我是不是给你描绘出一棵递归树啦？

就是穷举，或者说，不带「提前回溯」的「回溯」。

### 代码

```java
class Solution {
  public boolean judgePoint24(int[] nums) {
    List<Double> list = new ArrayList<>(4);
      for (int num : nums) {
         list.add((double) num);
      }
    return solve(list);
  }
  boolean solve(List<Double> nums){
    if(nums.size() == 1){
      return Math.abs(nums.get(0) - 24) <= 0.00001;
    }
    for(int i = 0; i < nums.size(); i++){
      for(int j = i + 1; j < nums.size(); j++){
        List<Double> copy = new ArrayList<>(nums);
        double b = copy.remove(j), a = copy.remove(i);
        boolean valid = false;
        copy.add(a + b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, a - b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, a * b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, a / b);
        valid |= solve(copy);
        copy.set(copy.size() - 1, b - a);
        valid |= solve(copy);
        copy.set(copy.size() - 1, b / a);
        valid |= solve(copy);
        if(valid) return true;
      }
    }
    return false;
  }
}
```

## 837 New 21 Game

### 原题

爱丽丝参与一个大致基于纸牌游戏 “21点” 规则的游戏，描述如下：

爱丽丝以 0 分开始，并在她的得分少于 K 分时抽取数字。 抽取时，她从 \[1, W\] 的范围中随机获得一个整数作为分数进行累计，其中 W 是整数。 每次抽取都是独立的，其结果具有相同的概率。

当爱丽丝获得不少于 K 分时，她就停止抽取数字。 爱丽丝的分数不超过 N 的概率是多少

Alice plays the following game, loosely based on the card game "21".

Alice starts with `0` points, and draws numbers while she has less than `K` points.  During each draw, she gains an integer number of points randomly from the range `[1, W]`, where `W` is an integer.  Each draw is independent and the outcomes have equal probabilities.

Alice stops drawing numbers when she gets `K` or more points.  What is the probability that she has `N` or less points?

**Example 1:**

```text
Input: N = 10, K = 1, W = 10
Output: 1.00000
Explanation:  Alice gets a single card, then stops.
```

**Example 2:**

```text
Input: N = 6, K = 1, W = 10
Output: 0.60000
Explanation:  Alice gets a single card, then stops.
In 6 out of W = 10 possibilities, she is at or below N = 6 points.
```

**Example 3:**

```text
Input: N = 21, K = 17, W = 10
Output: 0.73278
```

**Note:**

1. `0 <= K <= N <= 10000`
2. `1 <= W <= 10000`
3. Answers will be accepted as correct if they are within `10^-5` of the correct answer.
4. The judging time limit has been reduced for this question.

### 分析

![](../.gitbook/assets/image%20%28155%29.png)

时间复杂度：O\(N - K + K\) ==&gt; O\(N\) 

空间复杂度：O\(K + W\)

### 代码

```java
class Solution {
    public double new21Game(int N, int K, int W) {
        // 先判断 K - 1 + W 是否在 N 的里面，如果在的话，说明肯定能赢得游戏，返回 1.0，也就是 100%
        if (N - K + 1 >= W) {
            return 1.0;
        }
        double[] dp = new double[K + W];
        // 将能赢得游戏的点数的概率设置为 1
        for (int i = K; i <= N; i++) {
            dp[i] = 1.0;
        }
        // 计算K + W 这几个点数的概率和
        double sumProb = N - K + 1;
        // 从 K - 1 开始计算，
        for (int i = K - 1; i >= 0; i--) {
            // 点数为 i 的赢得游戏的概率为 i + 1 ~ i + W 的概率和除以 W 
            dp[i] = sumProb / W;
            sumProb = sumProb - dp[i + W] + dp[i];
        }

        return dp[0];
    }
}
```

## \* 1057 Campus Bikes

### 原题

在由 2D 网格表示的校园里有 n 位工人（worker）和 m 辆自行车（bike），n &lt;= m。所有工人和自行车的位置都用网格上的 2D 坐标表示。

我们需要为每位工人分配一辆自行车。在所有可用的自行车和工人中，我们选取彼此之间曼哈顿距离最短的工人自行车对 \(worker, bike\) ，并将其中的自行车分配給工人。如果有多个 \(worker, bike\) 对之间的曼哈顿距离相同，那么我们选择工人索引最小的那对。类似地，如果有多种不同的分配方法，则选择自行车索引最小的一对。不断重复这一过程，直到所有工人都分配到自行车为止。

给定两点 p1 和 p2 之间的曼哈顿距离为 Manhattan\(p1, p2\) = \|p1.x - p2.x\| + \|p1.y - p2.y\|。

返回长度为 n 的向量 ans，其中 a\[i\] 是第 i 位工人分配到的自行车的索引（从 0 开始）。

On a campus represented as a 2D grid, there are `N` workers and `M` bikes, with `N <= M`. Each worker and bike is a 2D coordinate on this grid.

Our goal is to assign a bike to each worker. Among the available bikes and workers, we choose the \(worker, bike\) pair with the shortest Manhattan distance between each other, and assign the bike to that worker. \(If there are multiple \(worker, bike\) pairs with the same shortest Manhattan distance, we choose the pair with the smallest worker index; if there are multiple ways to do that, we choose the pair with the smallest bike index\). We repeat this process until there are no available workers.

The Manhattan distance between two points `p1` and `p2` is `Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|`.

Return a vector `ans` of length `N`, where `ans[i]` is the index \(0-indexed\) of the bike that the `i`-th worker is assigned to.

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_1_v2.png)

```text
Input: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]]
Output: [1,0]
Explanation: 
Worker 1 grabs Bike 0 as they are closest (without ties), and Worker 0 is assigned Bike 1. So the output is [1, 0].
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_2_v2.png)

```text
Input: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]]
Output: [0,2,1]
Explanation: 
Worker 0 grabs Bike 0 at first. Worker 1 and Worker 2 share the same distance to Bike 2, thus Worker 1 is assigned to Bike 2, and Worker 2 will take Bike 1. So the output is [0,2,1].
```

**Note:**

1. `0 <= workers[i][j], bikes[i][j] < 1000`
2. All worker and bike locations are distinct.
3. `1 <= workers.length <= bikes.length <= 1000`

### 分析

就是排序，利用pq取出曼哈顿距离。

### 代码

```java
class Solution {

    public int[] assignBikes(int[][] workers, int[][] bikes) {
        PriorityQueue<Manhattan> pQueue = new PriorityQueue<>();
        for (int i = 0; i < workers.length; i++) {
            for (int j = 0; j < bikes.length; j++) {
                int manhattanDis = Math.abs(workers[i][0] - bikes[j][0]) + Math.abs(workers[i][1] - bikes[j][1]);
                pQueue.offer(new Manhattan(i, j, manhattanDis));
            }
        }
        int[] ans = new int[workers.length];
        boolean[] visit = new boolean[workers.length];
        boolean[] bikeVisit = new boolean[bikes.length];
        int workerCount = 0;
        while (!pQueue.isEmpty() && workerCount <= workers.length) {
            Manhattan temp = pQueue.poll();
            if (!visit[temp.workerNum] && !bikeVisit[temp.bikeNum]) {
                ans[temp.workerNum] = temp.bikeNum;
                visit[temp.workerNum] = true;
                bikeVisit[temp.bikeNum] = true;
                workerCount++;
            }
        }
        return ans;
    }
}

class Manhattan implements Comparable<Manhattan> {
    int workerNum;
    int bikeNum;
    int manhatDistance;

    Manhattan(int workerNum, int bikeNum, int manhatDistance) {
        this.workerNum = workerNum;
        this.bikeNum = bikeNum;
        this.manhatDistance = manhatDistance;
    }

    @Override
    public int compareTo(Manhattan o) {
        if (this.manhatDistance == o.manhatDistance) {
            if (this.workerNum == o.workerNum) {
                return this.bikeNum - o.bikeNum;
            } else {
                return this.workerNum - o.workerNum;
            }
        } else {
            return this.manhatDistance - o.manhatDistance;
        }
    }
}
```

## 1066 Campus Bikes II

### 原题

在由 2D 网格表示的校园里有 n 位工人（worker）和 m 辆自行车（bike），n &lt;= m。所有工人和自行车的位置都用网格上的 2D 坐标表示。

我们为每一位工人分配一辆专属自行车，使每个工人与其分配到的自行车之间的曼哈顿距离最小化。

p1 和 p2 之间的曼哈顿距离为 Manhattan\(p1, p2\) = \|p1.x - p2.x\| + \|p1.y - p2.y\|。

返回每个工人与分配到的自行车之间的曼哈顿距离的最小可能总和。

On a campus represented as a 2D grid, there are `N` workers and `M` bikes, with `N <= M`. Each worker and bike is a 2D coordinate on this grid.

We assign one unique bike to each worker so that the sum of the Manhattan distances between each worker and their assigned bike is minimized.

The Manhattan distance between two points `p1` and `p2` is `Manhattan(p1, p2) = |p1.x - p2.x| + |p1.y - p2.y|`.

Return _the minimum possible sum of Manhattan distances between each worker and their assigned bike_.

**Example 1:**![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_1_v2.png)

```text
Input: workers = [[0,0],[2,1]], bikes = [[1,2],[3,3]]
Output: 6
Explanation: 
We assign bike 0 to worker 0, bike 1 to worker 1. The Manhattan distance of both assignments is 3, so the output is 6.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2019/03/06/1261_example_2_v2.png)

```text
Input: workers = [[0,0],[1,1],[2,0]], bikes = [[1,0],[2,2],[2,1]]
Output: 4
Explanation: 
We first assign bike 0 to worker 0, then assign bike 1 to worker 1 or worker 2, bike 2 to worker 2 or worker 1. Both assignments lead to sum of the Manhattan distances as 4.
```

**Example 3:**

```text
Input: workers = [[0,0],[1,0],[2,0],[3,0],[4,0]], bikes = [[0,999],[1,999],[2,999],[3,999],[4,999]]
Output: 4995
```

**Constraints:**

* `N == workers.length`
* `M == bikes.length`
* `1 <= N <= M <= 10`
* `workers[i].length == 2`
* `bikes[i].length == 2`
* `0 <= workers[i][0], workers[i][1], bikes[i][0], bikes[i][1] < 1000`
* All the workers and the bikes locations are **unique**.

### 分析

1）DFS 暴力回溯，The DFS solution is pretty straight forward, try assign each bike to each worker.  
Time Complexy: O\(n \* m !\), n is number of workers, m is number of bikes

Ususally, when input size &lt;= 10, O\(n!\) can be accepeted. When input size &lt;= 12, we probably need do some pruning. if the test case is not strong, or problem designer wants to allow this techonolgy \(dfs + pruning\) to pass. we can luckly get a AC.\(For my experenice in LeetCode, when problem is tagged as Medium, this kind solution can be passed\)

2）BFS，用PQ计算最短距离

3\) 匈牙利算法+二分图匹配

### 代码

1）DFS

```java
class Solution {
    public int assignBikes(int[][] workers, int[][] bikes) {
        int n = workers.length;
        int m = bikes.length;
        int[][] dp = new int[n + 1][1 << m];
        for (int[] d : dp) {
            Arrays.fill(d, Integer.MAX_VALUE / 2);
        }
        dp[0][0] = 0;
        int min = Integer.MAX_VALUE;
        for (int i = 1; i <= n; i++) {
            for (int s = 1; s < (1 << m); s++) {
                for (int j = 0; j < m; j++) {
                    if ((s & (1 << j)) == 0) {
                        continue;
                    }
                    int prev = s ^ (1 << j);
                    dp[i][s] = Math.min(dp[i - 1][prev] + dis(workers[i - 1], bikes[j]), dp[i][s]) ;
                    if (i == n) {
                        min = Math.min(min, dp[i][s]);
                    }
                }
            }
        }
        return min;
    }
  
    public int dis(int[] p1, int[] p2) {
        return Math.abs(p1[0] - p2[0]) + Math.abs(p1[1] - p2[1]);
    }
}
```

2）BFS with PQ

```java
class Solution {
    public int assignBikes(int[][] workers, int[][] bikes) {
        Queue<Node> pq = new PriorityQueue<>(1,(a,b)->(a.cost-b.cost));
        Set<String> seen = new HashSet<>();
        pq.offer(new Node(0,0,0));
        while (!pq.isEmpty()){
            Node curr = pq.poll();
            String key = "$"+curr.worker+"$"+curr.mask;
            // reason - you can skip if you have already seen this mask
            // is because this is a PQ - and lower cost has already been seen
            // with this exact mask (i.e., those bikes used in some order)
            // then there is no point to consider a higher cost one 
            if (seen.contains(key))
                continue;
            seen.add(key);
            // all workers have a bike if this is true
            if (curr.worker == workers.length)
                return curr.cost;
            // scan all bikes - and create new nodes into the PQ for next worker.
            for(int j = 0; j < bikes.length; j++){
                if ( (curr.mask & (1<<j)) == 0){
                    pq.offer( new Node(curr.worker+1, curr.mask | (1 << j), 
                                       curr.cost + getDist(bikes[j], workers[curr.worker]) ));
                }
            }
        }
        return -1;
    }
    private int getDist(int[] bikepos,int[] wpos){
        return Math.abs(bikepos[0]-wpos[0]) + Math.abs(bikepos[1]-wpos[1]);
    }
    static class Node {
        int worker;
        int mask;
        int cost;
        public Node(int w,int m,int cost){
            this.worker = w;
            this.mask = m;
            this.cost = cost;
        }
    }
}
```

## 911 Online Election

### 原题

在选举中，第 i 张票是在时间为 times\[i\] 时投给 persons\[i\] 的。

现在，我们想要实现下面的查询函数： TopVotedCandidate.q\(int t\) 将返回在 t 时刻主导选举的候选人的编号。

在 t 时刻投出的选票也将被计入我们的查询之中。在平局的情况下，最近获得投票的候选人将会获胜。

In an election, the `i`-th vote was cast for `persons[i]` at time `times[i]`.

Now, we would like to implement the following query function: `TopVotedCandidate.q(int t)` will return the number of the person that was leading the election at time `t`.  

Votes cast at time `t` will count towards our query.  In the case of a tie, the most recent vote \(among tied candidates\) wins.

**Example 1:**

```text
Input: ["TopVotedCandidate","q","q","q","q","q","q"], [[[0,1,1,0,0,1,0],[0,5,10,15,20,25,30]],[3],[12],[25],[15],[24],[8]]
Output: [null,0,1,1,0,0,1]
Explanation: 
At time 3, the votes are [0], and 0 is leading.
At time 12, the votes are [0,1,1], and 1 is leading.
At time 25, the votes are [0,1,1,0,0,1], and 1 is leading (as ties go to the most recent vote.)
This continues for 3 more queries at time 15, 24, and 8.
```

**Note:**

1. `1 <= persons.length = times.length <= 5000`
2. `0 <= persons[i] <= persons.length`
3. `times` is a strictly increasing array with all elements in `[0, 10^9]`.
4. `TopVotedCandidate.q` is called at most `10000` times per test case.
5. `TopVotedCandidate.q(int t)` is always called with `t >= times[0]`.

### 分析

![](../.gitbook/assets/image%20%28169%29.png)

* 时间复杂度：O\(N + Q \log^2 N\)，其中 N 是选票的个数，QQ 是询问的个数。
* 空间复杂度：O\(N\)

![](../.gitbook/assets/image%20%28148%29.png)

* 时间复杂度：O\(N+QlogN\)，其中 N 是选票个数，Q 是询问个数。
* 空间复杂度：O\(N\)

### 代码

1\)

```java
class TopVotedCandidate {
    List<List<Vote>> A;
    public TopVotedCandidate(int[] persons, int[] times) {
        A = new ArrayList();
        Map<Integer, Integer> count = new HashMap();
        for (int i = 0; i < persons.length; ++i) {
            int p = persons[i], t = times[i];
            int c = count.getOrDefault(p, 0) + 1;

            count.put(p, c);
            while (A.size() <= c)
                A.add(new ArrayList<Vote>());
            A.get(c).add(new Vote(p, t));
        }
    }

    public int q(int t) {
        // Binary search on A[i][0].time for smallest i
        // such that A[i][0].time > t
        int lo = 1, hi = A.size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(mi).get(0).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }
        int i = lo - 1;

        // Binary search on A[i][j].time for smallest j
        // such that A[i][j].time > t
        lo = 0; hi = A.get(i).size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(i).get(mi).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }
        int j = Math.max(lo-1, 0);
        return A.get(i).get(j).person;
    }
}

class Vote {
    int person, time;
    Vote(int p, int t) {
        person = p;
        time = t;
    }
}
```

2\)

```java
class TopVotedCandidate {
    List<Vote> A;
    public TopVotedCandidate(int[] persons, int[] times) {
        A = new ArrayList();
        Map<Integer, Integer> count = new HashMap();
        int leader = -1;  // current leader
        int m = 0;  // current number of votes for leader

        for (int i = 0; i < persons.length; ++i) {
            int p = persons[i], t = times[i];
            int c = count.getOrDefault(p, 0) + 1;
            count.put(p, c);

            if (c >= m) {
                if (p != leader) {  // lead change
                    leader = p;
                    A.add(new Vote(leader, t));
                }

                if (c > m) m = c;
            }
        }
    }

    public int q(int t) {
        int lo = 1, hi = A.size();
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (A.get(mi).time <= t)
                lo = mi + 1;
            else
                hi = mi;
        }

        return A.get(lo - 1).person;
    }
}

class Vote {
    int person, time;
    Vote(int p, int t) {
        person = p;
        time = t;
    }
}
```

## 394 Decode String

### 原题

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: k\[encoded\_string\]，表示其中方括号内部的 encoded\_string 正好重复 k 次。注意 k 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2\[4\] 的输入。

Given an encoded string, return its decoded string.

The encoding rule is: `k[encoded_string]`, where the encoded\_string inside the square brackets is being repeated exactly k times. Note that k is guaranteed to be a positive integer.

You may assume that the input string is always valid; No extra white spaces, square brackets are well-formed, etc.

Furthermore, you may assume that the original data does not contain any digits and that digits are only for those repeat numbers, k. For example, there won't be input like `3a` or `2[4]`.

**Example 1:**

```text
Input: s = "3[a]2[bc]"
Output: "aaabcbc"
```

**Example 2:**

```text
Input: s = "3[a2[c]]"
Output: "accaccacc"
```

**Example 3:**

```text
Input: s = "2[abc]3[cd]ef"
Output: "abcabccdcdcdef"
```

**Example 4:**

```text
Input: s = "abc3[cd]xyz"
Output: "abccdcdcdxyz"
```

### 分析

![](../.gitbook/assets/image%20%28171%29.png)

![](../.gitbook/assets/image%20%28152%29.png)

### 代码

1\)

```java
class Solution {
    public String decodeString(String s) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        LinkedList<Integer> stack_multi = new LinkedList<>();
        LinkedList<String> stack_res = new LinkedList<>();
        for(Character c : s.toCharArray()) {
            if(c == '[') {
                stack_multi.addLast(multi);
                stack_res.addLast(res.toString());
                multi = 0;
                res = new StringBuilder();
            }
            else if(c == ']') {
                StringBuilder tmp = new StringBuilder();
                int cur_multi = stack_multi.removeLast();
                for(int i = 0; i < cur_multi; i++) tmp.append(res);
                res = new StringBuilder(stack_res.removeLast() + tmp);
            }
            else if(c >= '0' && c <= '9') multi = multi * 10 + Integer.parseInt(c + "");
            else res.append(c);
        }
        return res.toString();
    }
}
```

2\)

```java
class Solution {
    public String decodeString(String s) {
        return dfs(s, 0)[0];
    }
    private String[] dfs(String s, int i) {
        StringBuilder res = new StringBuilder();
        int multi = 0;
        while(i < s.length()) {
            if(s.charAt(i) >= '0' && s.charAt(i) <= '9') 
                multi = multi * 10 + Integer.parseInt(String.valueOf(s.charAt(i))); 
            else if(s.charAt(i) == '[') {
                String[] tmp = dfs(s, i + 1);
                i = Integer.parseInt(tmp[0]);
                while(multi > 0) {
                    res.append(tmp[1]);
                    multi--;
                }
            }
            else if(s.charAt(i) == ']') 
                return new String[] { String.valueOf(i), res.toString() };
            else 
                res.append(String.valueOf(s.charAt(i)));
            i++;
        }
        return new String[] { res.toString() };
    } 
}
```

## 315 Count of Smaller Numbers After Self

[https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/segment-tree\#315-count-of-smaller-numbers-after-self](https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/segment-tree#315-count-of-smaller-numbers-after-self)

## 250 Count Univalue Subtrees

[https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/untitled-1\#250-count-univalue-subtrees](https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/untitled-1#250-count-univalue-subtrees)

## 53 - Maximum Subarray

[https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/array/zi-shu-zu-subarray\#53-maximum-subarray](https://app.gitbook.com/@guilindev/s/interview/v/master/leetcode/array/zi-shu-zu-subarray#53-maximum-subarray)

## 688 Knight Probability in Chessboard

### 原题

On an `N`x`N` chessboard, a knight starts at the `r`-th row and `c`-th column and attempts to make exactly `K` moves. The rows and columns are 0 indexed, so the top-left square is `(0, 0)`, and the bottom-right square is `(N-1, N-1)`.

A chess knight has 8 possible moves it can make, as illustrated below. Each move is two squares in a cardinal direction, then one square in an orthogonal direction.

![](https://assets.leetcode.com/uploads/2018/10/12/knight.png)

Each time the knight is to move, it chooses one of eight possible moves uniformly at random \(even if the piece would go off the chessboard\) and moves there.

The knight continues moving until it has made exactly `K` moves or has moved off the chessboard. Return the probability that the knight remains on the board after it has stopped moving.

**Example:**

```text
Input: 3, 2, 0, 0
Output: 0.0625
Explanation: There are two moves (to (1,2), (2,1)) that will keep the knight on the board.
From each of those positions, there are also two moves that will keep the knight on the board.
The total probability the knight stays on the board is 0.0625.
```

**Note:**

* `N` will be between 1 and 25.
* `K` will be between 0 and 100.
* The knight always initially starts on the board.

### 分析

BFS

### 代码

```java
class Solution {
    /*
    这道题给了一个大小为NxN国际象棋棋盘，上面有个骑士，相当于中国象棋中的马，能走‘日’字，给了我们一个起始位置，然后说允许我们走K步，问走完K步之后还能留在棋盘上的概率是多少。
    */
    int[][] moves = {{1,2}, {1,-2},{2,1},{2,-1},{-1,2},{-1,-2},{-2,1},{-2,-1}};
    public double knightProbability(int N, int K, int r, int c) {
        int len = N;
        double dp0[][] = new double[len][len];
        for (double[] row : dp0) {
            Arrays.fill(row, 1);
        }
        for (int l = 0; l < K; l++) {
            double[][] dp1 = new double[len][len];
            for (int i = 0; i < len; i++) {
                for (int j = 0; j < len; j++) {
                    for (int[] move : moves) {
                        int row = i + move[0];
                        int col = j + move[1];
                        if (isLegal(row, col, len)) {
                            dp1[i][j] += dp0[row][col];
                        }
                    }
                }
            }
            dp0 = dp1;
        }
        return dp0[r][c]/Math.pow(8, K);
    }
    
    private boolean isLegal(int r, int c, int len) {
        return r >= 0 && r < len && c >= 0 && c < len;
    }
}
```

## 57 Insert Interval

### 原题

给出一个_无重叠的 ，_按照区间起始端点排序的区间列表。

在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。

Given a set of _non-overlapping_ intervals, insert a new interval into the intervals \(merge if necessary\).

You may assume that the intervals were initially sorted according to their start times.

**Example 1:**

```text
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```

**Example 2:**

```text
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

**Example 3:**

```text
Input: intervals = [], newInterval = [5,7]
Output: [[5,7]]
```

**Example 4:**

```text
Input: intervals = [[1,5]], newInterval = [2,3]
Output: [[1,5]]
```

**Example 5:**

```text
Input: intervals = [[1,5]], newInterval = [2,7]
Output: [[1,7]]
```

### 分析

![](../.gitbook/assets/image%20%28164%29.png)

![](../.gitbook/assets/image%20%28172%29.png)

* 时间复杂度：\mathcal{O}\(N\)O\(N\)。我们只遍历了一次输入元素。
* 空间复杂度：\mathcal{O}\(N\)O\(N\)，输出答案所使用的空间。

### 代码

```java
class Solution {
  public int[][] insert(int[][] intervals, int[] newInterval) {
    // init data
    int newStart = newInterval[0], newEnd = newInterval[1];
    int idx = 0, n = intervals.length;
    LinkedList<int[]> output = new LinkedList<int[]>();

    // add all intervals starting before newInterval
    while (idx < n && newStart > intervals[idx][0])
      output.add(intervals[idx++]);

    // add newInterval
    int[] interval = new int[2];
    // if there is no overlap, just add the interval
    if (output.isEmpty() || output.getLast()[1] < newStart)
      output.add(newInterval);
    // if there is an overlap, merge with the last interval
    else {
      interval = output.removeLast();
      interval[1] = Math.max(interval[1], newEnd);
      output.add(interval);
    }

    // add next intervals, merge with newInterval if needed
    while (idx < n) {
      interval = intervals[idx++];
      int start = interval[0], end = interval[1];
      // if there is no overlap, just add an interval
      if (output.getLast()[1] < start) output.add(interval);
      // if there is an overlap, merge with the last interval
      else {
        interval = output.removeLast();
        interval[1] = Math.max(interval[1], end);
        output.add(interval);
      }
    }
    return output.toArray(new int[output.size()][2]);
  }
}
```

## 174 Dungeon Game

### 原题

一些恶魔抓住了公主（P）并将她关在了地下城的右下角。地下城是由 M x N 个房间组成的二维网格。我们英勇的骑士（K）最初被安置在左上角的房间里，他必须穿过地下城并通过对抗恶魔来拯救公主。

骑士的初始健康点数为一个正整数。如果他的健康点数在某一时刻降至 0 或以下，他会立即死亡。

有些房间由恶魔守卫，因此骑士在进入这些房间时会失去健康点数（若房间里的值为负整数，则表示骑士将损失健康点数）；其他房间要么是空的（房间里的值为 0），要么包含增加骑士健康点数的魔法球（若房间里的值为正整数，则表示骑士将增加健康点数）。

为了尽快到达公主，骑士决定每次只向右或向下移动一步。

编写一个函数来计算确保骑士能够拯救到公主所需的最低初始健康点数。

例如，考虑到如下布局的地下城，如果骑士遵循最佳路径 右 -&gt; 右 -&gt; 下 -&gt; 下，则骑士的初始健康点数至少为 7。

The demons had captured the princess \(**P**\) and imprisoned her in the bottom-right corner of a dungeon. The dungeon consists of M x N rooms laid out in a 2D grid. Our valiant knight \(**K**\) was initially positioned in the top-left room and must fight his way through the dungeon to rescue the princess.

The knight has an initial health point represented by a positive integer. If at any point his health point drops to 0 or below, he dies immediately.

Some of the rooms are guarded by demons, so the knight loses health \(_negative_ integers\) upon entering these rooms; other rooms are either empty \(_0's_\) or contain magic orbs that increase the knight's health \(_positive_ integers\).

In order to reach the princess as quickly as possible, the knight decides to move only rightward or downward in each step.

**Write a function to determine the knight's minimum initial health so that he is able to rescue the princess.**

For example, given the dungeon below, the initial health of the knight must be at least **7** if he follows the optimal path `RIGHT-> RIGHT -> DOWN -> DOWN`.

| -2 \(K\) | -3 | 3 |
| :--- | :--- | :--- |
| -5 | -10 | 1 |
| 10 | 30 | -5 \(P\) |

**Note:**

* The knight's health has no upper bound.
* Any room can contain threats or power-ups, even the first room the knight enters and the bottom-right room where the princess is imprisoned.

### 分析

DFS + memoinization

时间复杂度：_O\(m∗n\)，_m,n 为 dungeon 的行数和列数。

空间复杂度：O\(m∗n\)，m,n 为 dungeon 的行数和列数。

### 代码

朴素DFS

```java
class Solution {

    public int calculateMinimumHP(int[][] dungeon) {
        return dfs(dungeon, dungeon.length, dungeon[0].length, 0, 0);
    }

    private int dfs(int[][] dungeon, int m, int n, int i, int j) {
        // 到达终点，递归终止。
        if (i == m - 1 && j == n - 1) {
            return Math.max(1 - dungeon[i][j], 1);
        }
        // 最后一行，只能向右搜索。
        if (i == m - 1) {
            return Math.max(dfs(dungeon, m, n, i, j + 1) - dungeon[i][j], 1);
        }
        // 最后一列，只能向下搜索。
        if (j == n - 1) {
           return Math.max(dfs(dungeon, m, n, i + 1, j) - dungeon[i][j], 1);
        }
        // 向下搜索 + 向右搜索，得到(i, j)点的后续路径所要求的最低血量 Math.min(dfs(i + 1, j), dfs(i, j + 1))，
        // 又因为(i, j)点本身提供血量dungeon[i][j], 因此从(i, j)开始所需的最低血量为 Math.min(dfs(i + 1, j), dfs(i, j + 1)) - dungeon[i][j]
        // 因为骑士的血量不能小于1，因此要和1取个max。
        return Math.max(Math.min(dfs(dungeon, m, n, i + 1, j), dfs(dungeon, m, n, i, j + 1)) - dungeon[i][j], 1);
    }
}
```

DFS + memoinization

```java
class Solution {
    int[][] memo; // 定义记忆化数组
    public int calculateMinimumHP(int[][] dungeon) {
        memo = new int[dungeon.length][dungeon[0].length];
        return dfs(dungeon, dungeon.length, dungeon[0].length, 0, 0);
    }

    private int dfs(int[][] dungeon, int m, int n, int i, int j) {
        // 到达终点，递归终止。
        if (i == m - 1 && j == n - 1) {
            return Math.max(1 - dungeon[i][j], 1);
        }
        // 如果memo数组中有值，直接取出并返回，不进行后续的搜索。
        if (memo[i][j] > 0) {
            return memo[i][j];
        }
        // 同解法一，向右搜+向下搜
        int minRes = 0;
        if (i == m - 1) {
            minRes =  Math.max(dfs(dungeon, m, n, i, j + 1) - dungeon[i][j], 1);
        } else if (j == n - 1) {
            minRes = Math.max(dfs(dungeon, m, n, i + 1, j) - dungeon[i][j], 1);
        } else {
            minRes = Math.max(Math.min(dfs(dungeon, m, n, i + 1, j), dfs(dungeon, m, n, i, j + 1)) - dungeon[i][j], 1);
        }
        // 将结果存入memo数组
        return memo[i][j] = minRes;
    }
}
```

## 1140 Stone Game II

### 原题

亚历克斯和李继续他们的石子游戏。许多堆石子 排成一行，每堆都有正整数颗石子 piles\[i\]。游戏以谁手中的石子最多来决出胜负。

亚历克斯和李轮流进行，亚历克斯先开始。最初，M = 1。

在每个玩家的回合中，该玩家可以拿走剩下的 前 X 堆的所有石子，其中 1 &lt;= X &lt;= 2M。然后，令 M = max\(M, X\)。

游戏一直持续到所有石子都被拿走。

假设亚历克斯和李都发挥出最佳水平，返回亚历克斯可以得到的最大数量的石头。

Alex and Lee continue their games with piles of stones.  There are a number of piles **arranged in a row**, and each pile has a positive integer number of stones `piles[i]`.  The objective of the game is to end with the most stones. 

Alex and Lee take turns, with Alex starting first.  Initially, `M = 1`.

On each player's turn, that player can take **all the stones** in the **first** `X` remaining piles, where `1 <= X <= 2M`.  Then, we set `M = max(M, X)`.

The game continues until all the stones have been taken.

Assuming Alex and Lee play optimally, return the maximum number of stones Alex can get.

**Example 1:**

```text
Input: piles = [2,7,9,4,4]
Output: 10
Explanation:  If Alex takes one pile at the beginning, Lee takes two piles, then Alex takes 2 piles again. Alex can get 2 + 4 + 4 = 10 piles in total. If Alex takes two piles at the beginning, then Lee can take all three piles left. In this case, Alex get 2 + 7 = 9 piles in total. So we return 10 since it's larger. 
```

**Constraints:**

* `1 <= piles.length <= 100`
* `1 <= piles[i] <= 10 ^ 4`

### 分析

![](../.gitbook/assets/image%20%28153%29.png)

对于piles = \[2,7,9,4,4\]，我们可以得到下图所示的dp数组，结果为dp\[0\]\[1\]

![](../.gitbook/assets/image%20%28145%29.png)

### 代码

```java
class Solution {
    public int stoneGameII(int[] piles) {
        int len = piles.length, sum = 0;
        int[][] dp = new int[len][len + 1];
        for (int i = len - 1; i >= 0; i--) {
            sum += piles[i];
            for (int M = 1; M <= len; M++) {
                if (i + 2 * M >= len) {
                    dp[i][M] = sum;
                } else {
                    for (int x = 1; x <= 2 * M; x++) {
                        dp[i][M] = Math.max(dp[i][M], sum - dp[i + x][Math.max(M, x)]);
                    }
                }
            }
        }
        return dp[0][1];
    }
}
```

记忆化递归

我们是这样设计状态的（很重要，这样的状态设计会给转移带来一定方便）：当前做决策的人，拿石子的时候得分为正，留给下一轮队手选择的时候，得分为负，因此定义的分数是 相对分数； 这一轮可以选择的石子堆数，与上一轮相关，因此需要设置一个参数 MM（和题目中的 MM 意思一样），表示当前可以选择的石子堆数； 从叶子结点开始向上每一步进行选择和比较，这样的做法才叫做「假设亚历克斯和李都发挥出最佳水平」，即每个人都让自己的利益最大化，等价于「让他人利益最小化」，这是因为石子的总数是固定的。

由于拿的都是左边的连续的石子堆，需要先计算一下前缀和，然后计算区间和； 当前区间（或者说由左边界 begin 决定的剩下的区间，右边界一定是 len - 1）里的石子堆数小于等于 2M2M 的时候，全部拿走，是使得自己获利最多的做法； 否则的话，就需要枚举拿 1 堆、2 堆、3 堆的时候，选出一个当对手利益最大化的时候，自己利益最大化的选择； 由于 memo 或者说 dfs 定义的是「相对分数」，因此输出的时候，还要做一个小的转化。

```java
public class Solution {

    // 一开始把这道题和区间 dp 联系在一起，是不对的

    public int stoneGameII(int[] piles) {
        int len = piles.length;
        int[][] memo = new int[len][len + 1];

        // [i, j] 的前缀和 preSum[j + 1] - preSum[i]
        int[] preSum = new int[len + 1];
        for (int i = 0; i < len; i++) {
            preSum[i + 1] = preSum[i] + piles[i];
        }
//        x + y = preSum[len];
//        x - y = res;
        int res = dfs(piles, 0, 1, preSum, memo);
        // 由于得到的是相对分数，需要转换成为绝对分数
        return (preSum[len] + res) / 2;
    }

    /**
     * @param piles
     * @param begin 定义石子堆的起始下标，即在 [start, len - 1] 这个区间里取石子
     * @param M     当前先手可以拿 [1, 2 * M] 堆石子（如果石子数够的话）
     * @param memo
     * @return 当前玩家在区间 [start, len - 1] 这个区间里取石子，得到的「相对分数」
     */
    private int dfs(int[] piles, int begin, int M, int[] preSum, int[][] memo) {
        int len = piles.length;
        if (begin >= len) {
            return 0;
        }

        if (memo[begin][M] != 0) {
            return memo[begin][M];
        }
        // 当前区间 [begin, len - 1] 的元素个数 len - begin <= 2M 的时候，
        // 全部拿走是利益最大的，这是因为 1 <= piles[i] <= 10 ^ 4
        if (len - begin <= 2 * M) {
            memo[begin][M] = preSum[len] - preSum[begin];
            return preSum[len] - preSum[begin];
        }

        // 走到这里，可以取的石子堆数 1 <= X <= 2M
        // 区间 [begin, j] 的长度 j - begin + 1 >= 2 * M
        int minLen = Math.min(2 * M, len - begin);
        // 这个初始化很重要，因为有可能是负分，所以不能初始化为 0
        int res = Integer.MIN_VALUE;
        for (int X = 1; X <= minLen; X++) {
            // 区间 [begin, begin + X - 1] 的前缀和 = preSum[begin + X] - preSum[begin - 1]
            int chooseLeft = preSum[begin + X] - preSum[begin];
            res = Math.max(res, chooseLeft - dfs(piles, begin + X, Math.max(M, X), preSum, memo));
        }
        memo[begin][M] = res;
        return res;
    }

    public static void main(String[] args) {
        Solution solution = new Solution();
        int[] piles = new int[]{2, 7, 9, 4, 4};
        int res = solution.stoneGameII(piles);
        System.out.println(res);
    }
}
```

## 1255 Maximum Score Words Formed by Letters

### 原题

你将会得到一份单词表 words，一个字母表 letters （可能会有重复字母），以及每个字母对应的得分情况表 score。

请你帮忙计算玩家在单词拼写游戏中所能获得的「最高得分」：能够由 letters 里的字母拼写出的 任意 属于 words 单词子集中，分数最高的单词集合的得分。

单词拼写游戏的规则概述如下：

* 玩家需要用字母表 letters 里的字母来拼写单词表 words 中的单词。 
* 可以只使用字母表 letters 中的部分字母，但是每个字母最多被使用一次。 
* 单词表 words 中每个单词只能计分（使用）一次。 
* 根据字母得分情况表score，字母 'a', 'b', 'c', ... , 'z' 对应的得分分别为 score\[0\], score\[1\], ..., score\[25\]。 
* 本场游戏的「得分」是指：玩家所拼写出的单词集合里包含的所有字母的得分之和。

Given a list of `words`, list of  single `letters` \(might be repeating\) and `score` of every character.

Return the maximum score of **any** valid set of words formed by using the given letters \(`words[i]` cannot be used two or more times\).

It is not necessary to use all characters in `letters` and each letter can only be used once. Score of letters `'a'`, `'b'`, `'c'`, ... ,`'z'` is given by `score[0]`, `score[1]`, ... , `score[25]` respectively.

**Example 1:**

```text
Input: words = ["dog","cat","dad","good"], letters = ["a","a","c","d","d","d","g","o","o"], score = [1,0,9,5,0,0,3,0,0,0,0,0,0,0,2,0,0,0,0,0,0,0,0,0,0,0]
Output: 23
Explanation:
Score  a=1, c=9, d=5, g=3, o=2
Given letters, we can form the words "dad" (5+1+5) and "good" (3+2+2+5) with a score of 23.
Words "dad" and "dog" only get a score of 21.
```

**Example 2:**

```text
Input: words = ["xxxz","ax","bx","cx"], letters = ["z","a","b","c","x","x","x"], score = [4,4,4,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,5,0,10]
Output: 27
Explanation:
Score  a=4, b=4, c=4, x=5, z=10
Given letters, we can form the words "ax" (4+5), "bx" (4+5) and "cx" (4+5) with a score of 27.
Word "xxxz" only get a score of 25.
```

**Example 3:**

```text
Input: words = ["leetcode"], letters = ["l","e","t","c","o","d"], score = [0,0,1,1,1,0,0,0,0,0,0,1,0,0,1,0,0,0,0,1,0,0,0,0,0,0]
Output: 0
Explanation:
Letter "e" can only be used once.
```

**Constraints:**

* `1 <= words.length <= 14`
* `1 <= words[i].length <= 15`
* `1 <= letters.length <= 100`
* `letters[i].length == 1`
* `score.length == 26`
* `0 <= score[i] <= 10`
* `words[i]`, `letters[i]` contains only lower case English letters.

### 分析

DFS

1、再maxmaxScorewords方法当中对字母的数组进行一个数量的统计 定义一个int\[26\] 数组通过int\[c-'a'\]++ （c-'a'是字母表当中对应位置字母的索引）来存储字母的个数 

2、将统计了字母个数的数组传入另外一个数组（设置了单词索引，成立单词的值，数组）， 

3、在DFS数组当中对单词索引的大小和单词数组长度做一个判断避免索引越界 

4、将单词words\[index\]变成字符串数组，增强for循环遍历数组，然后同样通过 -'a'来得到索引，判断在字母个数数组当中该字符的个数是否大于00 

5、大于零则计算对应的socre值，小于0说明该单词没有，直接重新递归当前方法 

6、若一个成立则继续往下执行，将单词的值给成员方法sum 

7、单词有四个，可知没有遍历完单词 

8、再次递归DFS方法，num=num+sum0，传递上一个单词的值。 9、收尾

### 代码

```java
class Solution {
    int sum;
    String[] words;
    int[] score;

    public int maxScoreWords(String[] words, char[] letters, int[] score) {
        this.words = words;
        this.score = score;
        int[] charNum = new int[score.length];
        for (char ch : letters) {
            charNum[ch - 'a']++;
        }
        DFS(0, 0, charNum);
        return sum;
    }

    public void DFS(int index, int num, int[] charNum) {
        if (index >= words.length) {
            return;
        }
        int[] charNum0 = charNum.clone();//复制数组
        int sum0 = 0;
        for (char c : words[index].toCharArray()) {
            if (charNum[c - 'a'] > 0) {
                sum0 = sum0 + score[c - 'a'];
                charNum[c - 'a']--;//减去可以组成单词的字母
            } else {
                DFS(index + 1, num, charNum0);
                return;
            }
        }
        //计算一个值
        sum = Integer.max(sum, num + sum0);
        //sum=sum>num+sum0?sum:num+sum0;
        //System.out.println(num);

        //上面只能计算出一个单词的和
        DFS(index + 1, num + sum0, charNum);
        DFS(index + 1, num, charNum0);


    }
}
```

## 1499 Max Value of Equation

### 原题

满足不等式的最大值，给你一个数组 points 和一个整数 k 。数组中每个元素都表示二维平面上的点的坐标，并按照横坐标 x 的值从小到大排序。也就是说 points\[i\] = \[xi, yi\] ，并且在 1 &lt;= i &lt; j &lt;= points.length 的前提下， xi &lt; xj 总成立。

请你找出 yi + yj + \|xi - xj\| 的 最大值，其中 \|xi - xj\| &lt;= k 且 1 &lt;= i &lt; j &lt;= points.length。

题目测试数据保证至少存在一对能够满足 \|xi - xj\| &lt;= k 的点。

Given an array `points` containing the coordinates of points on a 2D plane, sorted by the x-values, where `points[i] = [xi, yi]` such that `xi < xj` for all `1 <= i < j <= points.length`. You are also given an integer `k`.

Find the _maximum value of the equation_ `yi + yj + |xi - xj|` where `|xi - xj| <= k` and `1 <= i < j <= points.length`. It is guaranteed that there exists at least one pair of points that satisfy the constraint `|xi - xj| <= k`.

**Example 1:**

```text
Input: points = [[1,3],[2,0],[5,10],[6,-10]], k = 1
Output: 4
Explanation: The first two points satisfy the condition |xi - xj| <= 1 and if we calculate the equation we get 3 + 0 + |1 - 2| = 4. Third and fourth points also satisfy the condition and give a value of 10 + -10 + |5 - 6| = 1.
No other pairs satisfy the condition, so we return the max of 4 and 1.
```

**Example 2:**

```text
Input: points = [[0,0],[3,0],[9,2]], k = 3
Output: 3
Explanation: Only the first two points have an absolute difference of 3 or less in the x-values, and give the value of 0 + 0 + |0 - 3| = 3.
```

**Constraints:**

* `2 <= points.length <= 10^5`
* `points[i].length == 2`
* `-10^8 <= points[i][0], points[i][1] <= 10^8`
* `0 <= k <= 2 * 10^8`
* `points[i][0] < points[j][0]` for all `1 <= i < j <= points.length`
* `xi` form a strictly increasing sequence.

### 分析

线段树，优先队列，红黑树，单调队列，四种解法，本质一样

使用双端队列，首先将原表达式化为yi-xi+yj+xj的形式，其中i&gt;j且xi-xj&lt;=k，我们在固定j的值后，问题就变成了寻找区间内的最大值，我们从大到小遍历j，那么区间就是一个滑动窗口，我们参考滑动窗口中的最大值中使用双端队列的解法，由于每个元素最多进一次和出一次队列，所以时间复杂度为O\(n\)。

### 代码

```java
class Solution {
    int deque[];
    int head, rear, res;

    public void push_que(int p, int[][] points) {
        while (head != rear && points[deque[rear - 1]][1] - points[deque[rear - 1]][0] < points[p][1] - points[p][0]) {
            rear--;
        }
        deque[rear++] = p;
    }

    public void delete_que(int p) {
        if (head != rear && p == deque[head]) head++;
    }

    public int get_max(int[][] points) {
        return points[deque[head]][1] - points[deque[head]][0];
    }

    public int findMaxValueOfEquation(int[][] points, int k) {
        deque = new int[100004];
        head = 0;
        rear = 0;
        res = -Integer.MAX_VALUE;
        int n = points.length - 1, now = n - 1;
        for (int i = n; i > 0; i--) {
            delete_que(i);
            int j = i - 1;
            while (j > now && points[i][0] - points[j][0] > k) {
                delete_que(j);
                j--;
            }
            if (now >= i) now = i - 1;
            while (now >= 0 && points[i][0] - points[now][0] <= k) {
                push_que(now, points);
                now--;
            }
            //System.out.println(deque[head]==i);
            if (rear != head) res = Math.max(res, get_max(points) + points[i][0] + points[i][1]);
        }
        return res;
    }
}
```

## 815 Bus Route

### 原题

我们有一系列公交路线。每一条路线 routes\[i\] 上都有一辆公交车在上面循环行驶。例如，有一条路线 routes\[0\] = \[1, 5, 7\]，表示第一辆 \(下标为0\) 公交车会一直按照 1-&gt;5-&gt;7-&gt;1-&gt;5-&gt;7-&gt;1-&gt;... 的车站路线行驶。

假设我们从 S 车站开始（初始时不在公交车上），要去往 T 站。 期间仅可乘坐公交车，求出最少乘坐的公交车数量。返回 -1 表示不可能到达终点车站。

We have a list of bus routes. Each `routes[i]` is a bus route that the i-th bus repeats forever. For example if `routes[0] = [1, 5, 7]`, this means that the first bus \(0-th indexed\) travels in the sequence 1-&gt;5-&gt;7-&gt;1-&gt;5-&gt;7-&gt;1-&gt;... forever.

We start at bus stop `S` \(initially not on a bus\), and we want to go to bus stop `T`. Travelling by buses only, what is the least number of buses we must take to reach our destination? Return -1 if it is not possible.

```text
Example:
Input: 
routes = [[1, 2, 7], [3, 6, 7]]
S = 1
T = 6
Output: 2
Explanation: 
The best strategy is take the first bus to the bus stop 7, then take the second bus to the bus stop 6.
```

**Constraints:**

* `1 <= routes.length <= 500`.
* `1 <= routes[i].length <= 10^5`.
* `0 <= routes[i][j] < 10 ^ 6`.

### 分析

构建图，BFS

我们将每一条公交路线（而不是每一个车站）看成图中的一个点，如果两条公交路线有交集，那么它们在图中对应的点之间就有一条边。此外，起点站 S 和终点站 T 也分别是图中的一个点，如果一条公交路线包含了 S 或 T，那么也需要和 S 或 T 对应的点连一条边。此时，在这个图上从 S 到 T 的最短路径长度即为答案，我们可以用广度优先搜索来找出最短路径。

在计算两条公交路线是否有交集时，可以用的方法有很多种。例如将公交路线放在集合中，检查两个集合的交集是否为空；或者将公交路线中的车站进行递增排序，并使用双指针的方法检查是否有相同的车站。

时间复杂度：设 N 为公交路线的总数，b\_i为第 i 条公交路线中的车站数目，在建图时，我们的时间集中在判断两条公交路线是否有交集上。在广度优先搜索时，包含 N 个点的图的边数最大可以达到 O\(N^2\)，因此时间复杂度为 O\(N^2\)。

空间复杂度：O\(N^2\)，用来存储图。

### 代码

```java
class Solution {
    public int numBusesToDestination(int[][] routes, int S, int T) {
        if (S == T) return 0;
        int N = routes.length;

        List<List<Integer>> graph = new ArrayList();
        for (int i = 0; i < N; ++i) {
            Arrays.sort(routes[i]);
            graph.add(new ArrayList());
        }
        Set<Integer> seen = new HashSet();
        Set<Integer> targets = new HashSet();
        Queue<Point> queue = new ArrayDeque();

        // Build the graph.  Two buses are connected if
        // they share at least one bus stop.
        for (int i = 0; i < N; ++i)
            for (int j = i + 1; j < N; ++j)
                if (intersect(routes[i], routes[j])) {
                    graph.get(i).add(j);
                    graph.get(j).add(i);
                }

        // Initialize seen, queue, targets.
        // seen represents whether a node has ever been enqueued to queue.
        // queue handles our breadth first search.
        // targets is the set of goal states we have.
        for (int i = 0; i < N; ++i) {
            if (Arrays.binarySearch(routes[i], S) >= 0) {
                seen.add(i);
                queue.offer(new Point(i, 0));
            }
            if (Arrays.binarySearch(routes[i], T) >= 0)
                targets.add(i);
        }

        while (!queue.isEmpty()) {
            Point info = queue.poll();
            int node = info.x, depth = info.y;
            if (targets.contains(node)) return depth + 1;
            for (Integer nei : graph.get(node)) {
                if (!seen.contains(nei)) {
                    seen.add(nei);
                    queue.offer(new Point(nei, depth + 1));
                }
            }
        }

        return -1;
    }

    public boolean intersect(int[] A, int[] B) {
        int i = 0, j = 0;
        while (i < A.length && j < B.length) {
            if (A[i] == B[j]) return true;
            if (A[i] < B[j]) i++;
            else j++;
        }
        return false;
    }
}
```

## 1062 Longest Repeating Substring

### 原题

 给定字符串 `S`，找出最长重复子串的长度。如果不存在重复子串就返回 `0`。

Given a string `S`, find out the length of the longest repeating substring\(s\). Return `0` if no repeating substring exists.

**Example 1:**

```text
Input: S = "abcd"
Output: 0
Explanation: There is no repeating substring.
```

**Example 2:**

```text
Input: S = "abbaba"
Output: 2
Explanation: The longest repeating substrings are "ab" and "ba", each of which occurs twice.
```

**Example 3:**

```text
Input: S = "aabcaabdaab"
Output: 3
Explanation: The longest repeating substring is "aab", which occurs 3 times.
```

**Example 4:**

```text
Input: S = "aaaaa"
Output: 4
Explanation: The longest repeating substring is "aaaa", which occurs twice.
```

**Constraints:**

* The string `S` consists of only lowercase English letters from `'a'` - `'z'`.
* `1 <= S.length <= 1500`

### 分析

由于数据范围的限制，我们必须找到一个时间复杂度低于暴力 O\(N^2\) 的方法。

1）包含子串的哈希集合

这种方法实现起来非常直接：

使用滑动窗口来遍历所有长度为 L 的子串； 检查当前子串是否已经出现在集合中： 如果已经出现，就说明找到了长度为 L 的重复子串； 如果没有出现，我们将当前子串加入集合中

缺点也很显然，直接将字符串存储在集合中会占用大量的空间

时间复杂度：在平均情况下，为 O\(NlogN\)，最坏情况下，L 为 N/2，可以达到 O\(N^2\)。

空间复杂度：O\(N^2\)，为哈希集合占用的空间。

2）包含子串哈希值的哈希集合

为了减少方法一中的空间复杂度，我们可以在哈希集合中存储字符串的哈希值，而不是字符串的本身，别的都一样。

3）Rabin-Karp 字符串哈希算法

![](../.gitbook/assets/image%20%28156%29.png)

![](../.gitbook/assets/image%20%28178%29.png)

时间复杂度：O\(NlogN\)，二分查找的时间复杂度为 O\(logN\)，Rabin-Karp 字符串哈希的时间复杂度为 O\(N\)。

空间复杂度：O\(N\)，为哈希集合占用的空间。

### 代码

1）包含子串的哈希集合

```java
class Solution {
    /*
        Search a substring of given length
        that occurs at least 2 times.
        Return start position if the substring exits and -1 otherwise.
    */
    public int search(int L, int n, String S) {
        HashSet<String> seen = new HashSet();
        String tmp;
        for (int start = 0; start < n - L + 1; ++start) {
            tmp = S.substring(start, start + L);
            if (seen.contains(tmp)) return start;
            seen.add(tmp);
        }
        return -1;
    }

    public int longestRepeatingSubstring(String S) {
        int n = S.length();
        // binary search, L = repeating string length
        int left = 1, right = n;
        int L;
        while (left <= right) {
            L = left + (right - left) / 2;
            if (search(L, n, S) != -1) left = L + 1;
            else right = L - 1;
        }

        return left - 1;
    }
}
```

2）包含子串哈希值的哈希集合

```java
class Solution {
    /*
        Search a substring of given length
        that occurs at least 2 times.
        Return start position if the substring exits and -1 otherwise.
    */
    public int search(int L, int n, String S) {
        HashSet<Integer> seen = new HashSet();
        String tmp;
        int h;
        for (int start = 0; start < n - L + 1; ++start) {
            tmp = S.substring(start, start + L);
            h = tmp.hashCode();
            if (seen.contains(h)) return start;
            seen.add(h);
        }
        return -1;
    }

    public int longestRepeatingSubstring(String S) {
        int n = S.length();
        // binary search, L = repeating string length
        int left = 1, right = n;
        int L;
        while (left <= right) {
            L = left + (right - left) / 2;
            if (search(L, n, S) != -1) left = L + 1;
            else right = L - 1;
        }

        return left - 1;
    }
}
```

3）Rabin-Karp 字符串哈希算法

```java
class Solution {
    /*
      Rabin-Karp with polynomial rolling hash.
      Search a substring of given length
      that occurs at least 2 times.
      Return start position if the substring exits and -1 otherwise.
    */

    public int search(int L, int a, long modulus, int n, int[] nums) {
        // compute the hash of string S[:L]
        long h = 0;
        for (int i = 0; i < L; ++i) h = (h * a + nums[i]) % modulus;

        // already seen hashes of strings of length L
        HashSet<Long> seen = new HashSet();
        seen.add(h);
        // const value to be used often : a**L % modulus
        long aL = 1;
        for (int i = 1; i <= L; ++i) aL = (aL * a) % modulus;

        for(int start = 1; start < n - L + 1; ++start) {
            // compute rolling hash in O(1) time
            h = (h * a - nums[start - 1] * aL % modulus + modulus) % modulus;
            h = (h + nums[start + L - 1]) % modulus;
            if (seen.contains(h)) return start;
            seen.add(h);
        }
        return -1;
    }

    public int longestRepeatingSubstring(String S) {
        int n = S.length();
        // convert string to array of integers
        // to implement constant time slice
        int[] nums = new int[n];
        for (int i = 0; i < n; ++i) nums[i] = (int)S.charAt(i) - (int)'a';
        // base value for the rolling hash function
        int a = 26;
        // modulus value for the rolling hash function to avoid overflow
        long modulus = (long)Math.pow(2, 24);

        // binary search, L = repeating string length
        int left = 1, right = n;
        int L;
        while (left <= right) {
            L = left + (right - left) / 2;
            if (search(L, a, modulus, n, nums) != -1) left = L + 1;
            else right = L - 1;
        }

        return left - 1;
    }
}
```

## 1377 Frog Position After T Seconds

### 原题

T 秒后青蛙的位置

给你一棵由 n 个顶点组成的无向树，顶点编号从 1 到 n。青蛙从 顶点 1 开始起跳。规则如下：

* 在一秒内，青蛙从它所在的当前顶点跳到另一个 未访问 过的顶点（如果它们直接相连）。 
* 青蛙无法跳回已经访问过的顶点。 
* 如果青蛙可以跳到多个不同顶点，那么它跳到其中任意一个顶点上的机率都相同。 
* 如果青蛙不能跳到任何未访问过的顶点上，那么它每次跳跃都会停留在原地。 无向树的边用数组 edges 描述，其中 edges\[i\] = \[fromi, toi\] 意味着存在一条直接连通 fromi 和 toi 两个顶点的边。

返回青蛙在 t 秒后位于目标顶点 target 上的概率。

Given an undirected tree consisting of `n` vertices numbered from 1 to `n`. A frog starts jumping from the **vertex 1**. In one second, the frog jumps from its current vertex to another **unvisited** vertex if they are directly connected. The frog can not jump back to a visited vertex. In case the frog can jump to several vertices it jumps randomly to one of them with the same probability, otherwise, when the frog can not jump to any unvisited vertex it jumps forever on the same vertex. 

The edges of the undirected tree are given in the array `edges`, where `edges[i] = [fromi, toi]` means that exists an edge connecting directly the vertices `fromi` and `toi`.

_Return the probability that after `t` seconds the frog is on the vertex `target`._

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/02/20/frog_2.png)

```text
Input: n = 7, edges = [[1,2],[1,3],[1,7],[2,4],[2,6],[3,5]], t = 2, target = 4
Output: 0.16666666666666666 
Explanation: The figure above shows the given graph. The frog starts at vertex 1, jumping with 1/3 probability to the vertex 2 after second 1 and then jumping with 1/2 probability to vertex 4 after second 2. Thus the probability for the frog is on the vertex 4 after 2 seconds is 1/3 * 1/2 = 1/6 = 0.16666666666666666. 
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2020/02/20/frog_3.png)

```text
Input: n = 7, edges = [[1,2],[1,3],[1,7],[2,4],[2,6],[3,5]], t = 1, target = 7
Output: 0.3333333333333333
Explanation: The figure above shows the given graph. The frog starts at vertex 1, jumping with 1/3 = 0.3333333333333333 probability to the vertex 7 after second 1. 
```

**Example 3:**

```text
Input: n = 7, edges = [[1,2],[1,3],[1,7],[2,4],[2,6],[3,5]], t = 20, target = 6
Output: 0.16666666666666666
```

**Constraints:**

* `1 <= n <= 100`
* `edges.length == n-1`
* `edges[i].length == 2`
* `1 <= edges[i][0], edges[i][1] <= n`
* `1 <= t <= 50`
* `1 <= target <= n`
* Answers within `10^-5` of the actual value will be accepted as correct.

### 分析

先建立树

1）BFS，注意到达目标点后还有时间和节点会让概率置0

2）DFS回溯，注意选择下一步时减去相应的概率，就会让跳过的点概率归0

### 代码

BFS

```java
class Solution {
    HashMap<Integer, List<Integer>> map = new HashMap<>();

    public double frogPosition(int n, int[][] edges, int t, int target) {
        for (int i = 0; i < edges.length; i++) {
            if (map.containsKey(edges[i][0])) {
                map.get(edges[i][0]).add(edges[i][1]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][1]);
                map.put(edges[i][0], ls);
            }
            if (map.containsKey(edges[i][1])) {
                map.get(edges[i][1]).add(edges[i][0]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][0]);
                map.put(edges[i][1], ls);
            }
        }
        int[] flag = new int[n + 1];

        Queue<Integer> queue = new LinkedList<>();
        double[] res = new double[n + 1];
        res[1] = 1.0;
        flag[1] = 1;
        queue.offer(1);
        while (!queue.isEmpty()) {
            if (res[target] != 0 || t == 0) {
                break;
            }
            int num = queue.size();
            for (int i = 0; i < num; i++) {
                int cur = queue.poll();
                List<Integer> ls = map.get(cur);
                if (ls != null) {
                    for (int x : ls) {
                        if (flag[x] == 0) {
                            if (cur != 1) {
                                res[x] = res[cur] * 1.0 / (double) (ls.size() - 1);
                            } else {
                                res[x] = res[cur] * 1.0 / (double) (ls.size());
                            }
                            flag[x] = 1;
                            queue.offer(x);
                        }
                    }
                }
            }
            t--;
        }
        if (t > 0) {
            List<Integer> ls = map.get(target);
            if (ls != null) {
                for (int m : ls) {
                    if (flag[m] == 0) {
                        return 0.0;
                    }
                }
            }
        }
        return res[target];
    }
}
```

DFS

```java
class Solution {

    int[] visited = new int[105];
    double[] res = new double[105];
    HashMap<Integer, List<Integer>> map = new HashMap<>();

    public double frogPosition(int n, int[][] edges, int t, int target) {

        for (int i = 0; i < edges.length; i++) {
            if (map.containsKey(edges[i][0])) {
                map.get(edges[i][0]).add(edges[i][1]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][1]);
                map.put(edges[i][0], ls);
            }
            if (map.containsKey(edges[i][1])) {
                map.get(edges[i][1]).add(edges[i][0]);
            } else {
                List<Integer> ls = new ArrayList<>();
                ls.add(edges[i][0]);
                map.put(edges[i][1], ls);
            }
        }
        visited[1] = 1;
        res[1] = 1.0;
        dfs(1, t);
        return res[target];
    }

    public void dfs(int i, int t) {
        if (t == 0) return;
        List<Integer> ls = map.get(i);
        if (ls == null) return;
        double p = i == 1 ? res[i] / ls.size() : res[i] / (ls.size() - 1);
        for (int next : ls) {
            if (visited[next] != 1) {
                visited[next] = 1;
                res[i] -= p;
                res[next] += p;
                dfs(next, t - 1);
                visited[next] = 0;
            }
        }

    }
}
```

## 375 Guess Number Higher or Lower II

### 原题

我们正在玩一个猜数游戏，游戏规则如下：

我从 1 到 n 之间选择一个数字，你来猜我选了哪个数字。

每次你猜错了，我都会告诉你，我选的数字比你的大了或者小了。

然而，当你猜了数字 x 并且猜错了的时候，你需要支付金额为 x 的现金。直到你猜到我选的数字，你才算赢得了这个游戏。

We are playing the Guessing Game. The game will work as follows:

1. I pick a number between `1` and `n`.
2. You guess a number.
3. If you guess the right number, **you win the game**.
4. If you guess the wrong number, then I will tell you whether the number I picked is **higher or lower**, and you will continue guessing.
5. Every time you guess a wrong number `x`, you will pay `x` dollars. If you run out of money, **you lose the game**.

Given a particular `n`, return _the minimum amount of money you need to **guarantee a win regardless of what number I pick**_.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/09/10/graph.png)

```text
Input: n = 10
Output: 16
Explanation: The winning strategy is as follows:
- The range is [1,10]. Guess 7.
    - If this is my number, your total is $0. Otherwise, you pay $7.
    - If my number is higher, the range is [8,10]. Guess 9.
        - If this is my number, your total is $7. Otherwise, you pay $9.
        - If my number is higher, it must be 10. Guess 10. Your total is $7 + $9 = $16.
        - If my number is lower, it must be 8. Guess 8. Your total is $7 + $9 = $16.
    - If my number is lower, the range is [1,6]. Guess 3.
        - If this is my number, your total is $7. Otherwise, you pay $3.
        - If my number is higher, the range is [4,6]. Guess 5.
            - If this is my number, your total is $7 + $3 = $10. Otherwise, you pay $5.
            - If my number is higher, it must be 6. Guess 6. Your total is $7 + $3 + $5 = $15.
            - If my number is lower, it must be 4. Guess 4. Your total is $7 + $3 + $5 = $15.
        - If my number is lower, the range is [1,2]. Guess 1.
            - If this is my number, your total is $7 + $3 = $10. Otherwise, you pay $1.
            - If my number is higher, it must be 2. Guess 2. Your total is $7 + $3 + $1 = $11.
The worst case in all these scenarios is that you pay $16. Hence, you only need $16 to guarantee a win.
```

**Example 2:**

```text
Input: n = 1
Output: 0
Explanation: There is only one possible number, so you can guess 1 and not have to pay anything.
```

**Example 3:**

```text
Input: n = 2
Output: 1
Explanation: There are two possible numbers, 1 and 2.
- Guess 1.
    - If this is my number, your total is $0. Otherwise, you pay $1.
    - If my number is higher, it must be 2. Guess 2. Your total is $1.
The worst case is that you pay $1.
```

**Constraints:**

* `1 <= n <= 200`

### 分析

1）暴力解（TLE） 时间复杂度: O\(N!\) 空间复杂度： O\(N\) 

我们首先在\(1, n\)中任意挑选一个数字i， 假设i是错误的（最坏情况）， 我们需要用最小代价去 猜到需要的数字。那么在一次尝试后，答案要么在i的左边， 要么在i的右边， 为了考虑最差的情况，我们 需要考虑两者的较大值。 cost\(1,n\) = i + max\( cost\(1, i - 1\), cost\(i + 1\), n\)\)

2）DP，从暴力解可以看出， 大区间的猜数字的成本求解， 可以由小区间计算出来。  
时间复杂度： O\(N ^ 3\), 空间复杂度O\(N ^ 2\)

![](../.gitbook/assets/image%20%28149%29.png)

### 代码

1）暴力

```java
class Solution {
    public int getMoneyAmount(int n){
        return cost(1, n);
    }
    public int cost(int l, int r){
        if(l >= r)  return 0;
        int min = Integer.MAX_VALUE;
        for(int i = l; i <= r; i++){
            int res = i + Math.max(cost(l, i - 1), cost(i + 1, r));
            min = Math.min(min, res);
        }
        return min;
    }
}
```

2）DP

```java
class Solution {
        public int getMoneyAmount(int n){
        int[][] dp = new int[n + 1][n + 1];
        for(int len = 2; len <= n; len ++){
            for(int i = 1; i + len - 1 <= n; i++){
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;
                for(int k = i; k <= j; k++){
                    dp[i][j] = Math.min(dp[i][j],
                            k + Math.max(k <= 1 ? 0 : dp[i][k - 1],
                                    k + 1 > j ? 0: dp[k + 1][j]));
                }
            }
        }
        return dp[1][n];
    }
}
```

## 1326 Minimum Number of Taps to Open to Water a Garden

### 原题

在 x 轴上有一个一维的花园。花园长度为 n，从点 0 开始，到点 n 结束。

花园里总共有 n + 1 个水龙头，分别位于 \[0, 1, ..., n\] 。

给你一个整数 n 和一个长度为 n + 1 的整数数组 ranges ，其中 ranges\[i\] （下标从 0 开始）表示：如果打开点 i 处的水龙头，可以灌溉的区域为 \[i - ranges\[i\], i + ranges\[i\]\] 。

请你返回可以灌溉整个花园的 最少水龙头数目 。如果花园始终存在无法灌溉到的地方，请你返回 -1 。

There is a one-dimensional garden on the x-axis. The garden starts at the point `0` and ends at the point `n`. \(i.e The length of the garden is `n`\).

There are `n + 1` taps located at points `[0, 1, ..., n]` in the garden.

Given an integer `n` and an integer array `ranges` of length `n + 1` where `ranges[i]` \(0-indexed\) means the `i-th` tap can water the area `[i - ranges[i], i + ranges[i]]` if it was open.

Return _the minimum number of taps_ that should be open to water the whole garden, If the garden cannot be watered return **-1**.

**Example 1:**![](https://assets.leetcode.com/uploads/2020/01/16/1685_example_1.png)

```text
Input: n = 5, ranges = [3,4,1,1,0,0]
Output: 1
Explanation: The tap at point 0 can cover the interval [-3,3]
The tap at point 1 can cover the interval [-3,5]
The tap at point 2 can cover the interval [1,3]
The tap at point 3 can cover the interval [2,4]
The tap at point 4 can cover the interval [4,4]
The tap at point 5 can cover the interval [5,5]
Opening Only the second tap will water the whole garden [0,5]
```

**Example 2:**

```text
Input: n = 3, ranges = [0,0,0,0]
Output: -1
Explanation: Even if you activate all the four taps you cannot water the whole garden.
```

**Example 3:**

```text
Input: n = 7, ranges = [1,2,1,0,2,1,0,1]
Output: 3
```

**Example 4:**

```text
Input: n = 8, ranges = [4,0,0,0,0,0,0,0,4]
Output: 2
```

**Example 5:**

```text
Input: n = 8, ranges = [4,0,0,0,4,0,0,0,4]
Output: 1
```

**Constraints:**

* `1 <= n <= 10^4`
* `ranges.length == n + 1`
* `0 <= ranges[i] <= 100`

### 分析

1）DP，O\(N ^ 2\)

![](../.gitbook/assets/image%20%28150%29.png)

2）DP小优化，`O(N * maxRange)`maxrange 为所有水龙头中最大的灌溉区域长度。

![](../.gitbook/assets/image%20%28168%29.png)

3）贪心

可以将问题转换一下，水龙头浇灌的范围是一个区间，花园的范围也是一个区间。求最小水龙头数，实质上就是求能够覆盖花园整个区间的最小区间数。 那么我们就可以利用贪心来求解问题了。我们的目的是在能够覆盖当前最左范围的所有水龙头中选择灌溉最右范围最大的水龙头。

1.将水龙头按照其能灌溉到的最右位置进行排序； 

2.从后往前查找第一个能够灌溉到花园最左边的水龙头，就是我们第一个选择的水龙头\(因为我们是按照最右位置的大小排序的\) 

3.更新左范围为当前所选水龙头最右位置，并删除当前水龙头之前的水龙头即可\(并不是真的删除，改一下可用水龙头的index即可\) 重复2，3即可。

排序了，O\(nlogn\)

### 代码

1）DP

```java
class Solution {

    class Interval {
        int start;
        int end;
        Interval(int start, int end){
            this.start = start;
            this.end = end;
        }
    }
    
    public int minTaps(int n, int[] ranges) {

        List<Interval> intervals = new ArrayList<>();
        int pos = 0;
        // 得到所有灌溉区间。
        for (int range: ranges){
            intervals.add(new Interval(pos - range, pos + range));
            pos += 1;
        }
        
        int size = intervals.size();
        // n + 1个点一共有 n 个区间。 dp矩阵表示前i个区间所需最小的taps
        int[] dp = new int[n + 1];
        dp[0] = 0;
        for (int i = 0; i < n; i++){
            dp[i + 1] = Integer.MAX_VALUE;
            // 枚举所有覆盖它的区间。 
            for (int j =0; j < size; j++){
                if (intervals.get(j).end < i + 1 || intervals.get(j).start > i){
                    continue;
                }
                // 满足：intervals.get(j).start <= i &&  intervals.get(j).end >= i + 1                
                int start = intervals.get(j).start >= 0 ? intervals.get(j).start : 0;
                
                if (dp[start] != Integer.MAX_VALUE){
                    dp[i + 1] = Math.min(dp[i + 1], dp[start] + 1);
                }

            }
        }
        return dp[n] == Integer.MAX_VALUE ? -1 : dp[n];
    }
}
```

2）DP优化

```java
class Solution {

    public int minTaps(int n, int[] ranges) {
        int[] dp = new int[n + 1];
        Arrays.fill(dp, Integer.MAX_VALUE);

        // dp[i] 表示为了覆盖区间 [0,i] 所需要 的最小taps 即前i个数为止的区间。
        dp[0] = 0; 

        // 遍历所有水龙头的位置
        for (int i = 0; i <= n; i++){
            // 找到当前处的tap能覆盖的最左和最右的位置
            int left = Math.max(i- ranges[i], 0);
            int right = Math.min(i + ranges[i], n);
            for (int j = left + 1; j <= right;j++){
                //查看如果用这个tap去覆盖这些位置,所用的水龙头的位置均为前【0，left】区间所需要的水龙头的数量 + 1
                if (dp[left] != Integer.MAX_VALUE){
                     dp[j] = Math.min(dp[j], dp[left] + 1);
                }
            }
        }
        return dp[n] == Integer.MAX_VALUE ? -1: dp[n];
    }
}
```

3）贪心

```java
class Solution {
	//水龙头范围
    class Range{
        public Range(int l, int r) {
        	this.l = l;
        	this.r = r;
        }
        int l;
        int r;
    }
	//计数君
	class IntHolder{
		int cnt = 0;
	}

	public int minTaps(int n, int[] ranges) {
    	Range[] Ranges = new Range[ranges.length];
    	for(int i = 0; i < ranges.length; i++) {
    		Range ran = new Range(i-ranges[i], i+ranges[i]);
    		Ranges[i] = ran;
    	}
    	
	//将水龙头按最右位置排序
    	Arrays.sort(Ranges, new Comparator<Range>() {
    		@Override
    		public int compare(Range r1, Range r2) {
    			return r1.r-r2.r;
    		}
    	});
    	
    	IntHolder intholder = new IntHolder();
    	
    	boolean flag = match(0, n, 0, Ranges, intholder);
    	
    	if(flag) return intholder.cnt;
    	return -1;
    }
    
	/**
     * 是否能够灌溉到当前左边界到右边界
     * 
     * @param lrange 左边界
     * @param rlange 右边界
     * @param lindex 可选水龙头的起始索引
     * @param Ranges 水龙头范围数组
     * @param intholder 计数君
     * @return 是否能够灌溉到当前lrange-rlange
     */
    public boolean match(int lrange, int rlange, int lindex, Range[] Ranges, IntHolder intholder) {
    	boolean isMatch = false;
    	for(int i = Ranges.length-1; i >= lindex; i--) {
    		//灌溉了整个区间
    		if(Ranges[i].l <= lrange && Ranges[i].r >= rlange) {
    			intholder.cnt++;
    			return true;
    		}
    		//能灌溉到左边界
    		if(Ranges[i].l <= lrange) {
    			intholder.cnt++;
    			isMatch = match(Ranges[i].r, rlange, i+1, Ranges, intholder);
    			break;
    		}
    	}
	//若不能灌溉到当前左边界，则返回false，能灌溉到则返回true
    	return isMatch;
    }
}
```

## 871 Minimum Number of Refueling Stops

### 原题

最低加油次数，汽车从起点出发驶向目的地，该目的地位于出发位置东面 target 英里处。

沿途有加油站，每个 station\[i\] 代表一个加油站，它位于出发位置东面 station\[i\]\[0\] 英里处，并且有 station\[i\]\[1\] 升汽油。

假设汽车油箱的容量是无限的，其中最初有 startFuel 升燃料。它每行驶 1 英里就会用掉 1 升汽油。

当汽车到达加油站时，它可能停下来加油，将所有汽油从加油站转移到汽车中。

为了到达目的地，汽车所必要的最低加油次数是多少？如果无法到达目的地，则返回 -1 。

注意：如果汽车到达加油站时剩余燃料为 0，它仍然可以在那里加油。如果汽车到达目的地时剩余燃料为 0，仍然认为它已经到达目的地。

A car travels from a starting position to a destination which is `target` miles east of the starting position.

Along the way, there are gas stations.  Each `station[i]` represents a gas station that is `station[i][0]` miles east of the starting position, and has `station[i][1]` liters of gas.

The car starts with an infinite tank of gas, which initially has `startFuel` liters of fuel in it.  It uses 1 liter of gas per 1 mile that it drives.

When the car reaches a gas station, it may stop and refuel, transferring all the gas from the station into the car.

What is the least number of refueling stops the car must make in order to reach its destination?  If it cannot reach the destination, return `-1`.

Note that if the car reaches a gas station with 0 fuel left, the car can still refuel there.  If the car reaches the destination with 0 fuel left, it is still considered to have arrived.

**Example 1:**

```text
Input: target = 1, startFuel = 1, stations = []
Output: 0
Explanation: We can reach the target without refueling.
```

**Example 2:**

```text
Input: target = 100, startFuel = 1, stations = [[10,100]]
Output: -1
Explanation: We can't reach the target (or even the first gas station).
```

**Example 3:**

```text
Input: target = 100, startFuel = 10, stations = [[10,60],[20,30],[30,30],[60,40]]
Output: 2
Explanation: 
We start with 10 liters of fuel.
We drive to position 10, expending 10 liters of fuel.  We refuel from 0 liters to 60 liters of gas.
Then, we drive from position 10 to position 60 (expending 50 liters of fuel),
and refuel from 10 liters to 50 liters of gas.  We then drive to and reach the target.
We made 2 refueling stops along the way, so we return 2.
```

**Note:**

1. `1 <= target, startFuel, stations[i][1] <= 10^9`
2. `0 <= stations.length <= 500`
3. `0 < stations[0][0] < stations[1][0] < ... < stations[stations.length-1][0] < target`

### 分析

一开始看到这个题， 多各油站， 要么加油，要么不加油， 油量要大于等于0， 这TM不是01背包问题吗 结果， 这个油量的取值范围是1e9， 搞了半天， 弄个暴搜出来TLE了 最后无奈无看题解， 这集合划分也实在太给力点。。。确实想不出来。。。

**思路**

dp\[i\] 为加 i 次油能走的最远距离，需要满足 dp\[i\] &gt;= target 的最小 i。

**算法**

依次计算每个 dp\[i\]，对于 dp\[0\]，就只用初始的油量 startFuel 看能走多远。

每多一个加油站 station\[i\] = \(location, capacity\)，如果之前可以通过加 t 次油到达这个加油站，现在就可以加 t+1 次油得到 capcity 的油量。

举个例子，原本加一次油可以行驶的最远距离为 15，现在位置 10 有一个加油站，有 30 升油量储备，那么显然现在可以加两次油行驶 45 距离。

![](../.gitbook/assets/image%20%28157%29.png)



* 时间复杂度： O\(N^2\)，其中 N 为加油站的个数。
* 空间复杂度： O\(N\)，`dp` 数组占用的空间。

### 代码

```java
class Solution {
        public int minRefuelStops(int target, int startFuel, int[][] stations){
        int n = stations.length;
        if(n == 0) return startFuel >= target ? 0: -1;
        int[] dp = new int[n + 1];
        dp[0] = startFuel;
        for(int i = 1; i <= n; i++){
            int used = -1;
            for(int k = 1; k <= n && stations[k - 1][0] <= dp[i - 1]; k++){
                if(dp[i - 1] + stations[k - 1][1] > dp[i]){
                    dp[i] = dp[i - 1] + stations[k - 1][1];
                    used = k;
                }
            }
            //如果这个加油站加过油了， 把油量置为0
            if(used != -1)  stations[used - 1][1] = 0;
        }
        int ans = n + 10;
        for(int i = 0; i <= n; i++){
            if(dp[i] >= target) ans = Math.min(ans, i);
        }
        return ans == n + 10 ? -1: ans;
    }
}
```

```java
class Solution {
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        int N = stations.length;
        long[] dp = new long[N + 1];
        dp[0] = startFuel;
        for (int i = 0; i < N; ++i)
            for (int t = i; t >= 0; --t)
                if (dp[t] >= stations[i][0])
                    dp[t+1] = Math.max(dp[t+1], dp[t] + (long) stations[i][1]);

        for (int i = 0; i <= N; ++i)
            if (dp[i] >= target) return i;
        return -1;
    }
}
```

## 420 Strong Password Checker

### 原题

一个强密码应满足以下所有条件：

1. 由至少6个，至多20个字符组成。 
2. 至少包含一个小写字母，一个大写字母，和一个数字。 
3. 同一字符不能连续出现三次 \(比如 "...aaa..." 是不允许的, 但是 "...aa...a..." 是可以的\)。

 编写函数 strongPasswordChecker\(s\)，s 代表输入字符串，如果 s 已经符合强密码条件，则返回0；否则返回要将 s 修改为满足强密码条件的字符串所需要进行修改的最小步数。

插入、删除、替换任一字符都算作一次修改。

A password is considered strong if below conditions are all met:

1. It has at least 6 characters and at most 20 characters.
2. It must contain at least one lowercase letter, at least one uppercase letter, and at least one digit.
3. It must NOT contain three repeating characters in a row \("...aaa..." is weak, but "...aa...a..." is strong, assuming other conditions are met\).

Write a function strongPasswordChecker\(s\), that takes a string s as input, and return the **MINIMUM** change required to make s a strong password. If s is already strong, return 0.

Insertion, deletion or replace of any one character are all considered as one change.

### 分析

就是先把一段段连续的长度放进优先队列，队列以len % 3的大小判断优先级，然后根据原来长度是小于6还是6-20还是20以上分别讨论。用了优先队列可以避免一些复杂操作。

### 代码

```java
class Solution {
    public int strongPasswordChecker(String s) {
        int len = s.length();
        if (len == 0) return 6;
        char[] ss = s.toCharArray();

        // 记录所需小写字母、大写字母和数字的个数
        // cnt记录重复序列的字符计数
        int low = 1, up = 1, num = 1, cnt = 1;
        if (Character.isLowerCase(ss[0])) low = 0;
        else if (Character.isUpperCase(ss[0])) up = 0;
        else if (Character.isDigit(ss[0])) num = 0;

        // 优先队列，x%3小的先出队，为方便需要删除操作时先处理段的序列
        Queue<Integer> queue = new PriorityQueue<>((a, b) -> a % 3 - b % 3);

        for (int i = 1; i < len; i ++) {
            if (low == 1 && Character.isLowerCase(ss[i])) low = 0;
            else if (up == 1 && Character.isUpperCase(ss[i])) up = 0;
            else if (num == 1 && Character.isDigit(ss[i])) num = 0;

            // 对连续相同的序列计数，并存入优先队列
            if (ss[i] != ss[i - 1]) {
                if (cnt >= 3) queue.add(cnt);
                cnt = 1;
            } else {
                cnt ++;
            }
        }
        if (cnt >= 3) queue.add(cnt);
        
        int ans = 0, all = low + up + num;

        // 长度不足，则根据是否有重复序列进行替换、加添操作
        if (len < 6) return Math.max(6 - len, all);

        // 删除操作，从最短的连续序列开始处理
        while (!queue.isEmpty() && len > 20) {
            int cur = queue.remove();
            ans ++;
            len --;
            if (-- cur >= 3) queue.add(cur);
        }

        // 解决完重复序列后，字符串仍然过长
        if (len > 20) return ans + len - 20 + all;

        // 未处理完重复序列就已经达到长度要求，继续处理重复序列
        // 此时就只考虑替换操作就好了
        int n = 0;
        while (!queue.isEmpty()) {
            n += queue.remove() / 3;
        }

        return ans + Math.max(n, all);
    }

}
```

## 609 Find Duplicate File in System

### 原题

在系统中查找重复文件

给定一个目录信息列表，包括目录路径，以及该目录中的所有包含内容的文件，您需要找到文件系统中的所有重复文件组的路径。一组重复的文件至少包括二个具有完全相同内容的文件。

输入列表中的单个目录信息字符串的格式如下：

"root/d1/d2/.../dm f1.txt\(f1\_content\) f2.txt\(f2\_content\) ... fn.txt\(fn\_content\)"

这意味着有 n 个文件（f1.txt, f2.txt ... fn.txt 的内容分别是 f1\_content, f2\_content ... fn\_content）在目录 root/d1/d2/.../dm 下。注意：n&gt;=1 且 m&gt;=0。如果 m=0，则表示该目录是根目录。

该输出是重复文件路径组的列表。对于每个组，它包含具有相同内容的文件的所有文件路径。文件路径是具有下列格式的字符串：

"directory\_path/file\_name.txt"

Given a list of directory info including directory path, and all the files with contents in this directory, you need to find out all the groups of duplicate files in the file system in terms of their paths.

A group of duplicate files consists of at least **two** files that have exactly the same content.

A single directory info string in the **input** list has the following format:

`"root/d1/d2/.../dm f1.txt(f1_content) f2.txt(f2_content) ... fn.txt(fn_content)"`

It means there are **n** files \(`f1.txt`, `f2.txt` ... `fn.txt` with content `f1_content`, `f2_content` ... `fn_content`, respectively\) in directory `root/d1/d2/.../dm`. Note that n &gt;= 1 and m &gt;= 0. If m = 0, it means the directory is just the root directory.

The **output** is a list of group of duplicate file paths. For each group, it contains all the file paths of the files that have the same content. A file path is a string that has the following format:

`"directory_path/file_name.txt"`

**Example 1:**

```text
Input:
["root/a 1.txt(abcd) 2.txt(efgh)", "root/c 3.txt(abcd)", "root/c/d 4.txt(efgh)", "root 4.txt(efgh)"]
Output:  
[["root/a/2.txt","root/c/d/4.txt","root/4.txt"],["root/a/1.txt","root/c/3.txt"]]
```

**Note:**

1. No order is required for the final output.
2. You may assume the directory name, file name and file content only has letters and digits, and the length of file content is in the range of \[1,50\].
3. The number of files given is in the range of \[1,20000\].
4. You may assume no files or directories share the same name in the same directory.
5. You may assume each given directory info represents a unique directory. Directory path and file info are separated by a single blank space.

 **Follow-up beyond contest:**

1. Imagine you are given a real file system, how will you search files? DFS or BFS?
2. If the file content is very large \(GB level\), how will you modify your solution?
3. If you can only read the file by 1kb each time, how will you modify your solution?
4. What is the time complexity of your modified solution? What is the most time-consuming part and memory consuming part of it? How to optimize?
5. How to make sure the duplicated files you find are not false positive?

### 分析

哈希表 首先我们通过字符串操作获取目录路径、文件名和文件内容。我们使用哈希映射（HashMap）来寻找重复文件，哈希映射中的键（key）是文件内容，值（value）是存储路径和文件名的列表。

我们遍历每一个文件，并把它加入哈希映射中。在这之后，我们遍历哈希映射，如果一个键对应的值列表的长度大于 1，说明我们找到了重复文件，可以把这个列表加入到答案中。

### 代码

```java
public class Solution {
    public List<List<String>> findDuplicate(String[] paths) {
        HashMap<String, List<String>> map = new HashMap<>();
        for (String path : paths) {
            String[] values = path.split(" ");
            for (int i = 1; i < values.length; i++) {
                String[] name_cont = values[i].split("\\(");
                name_cont[1] = name_cont[1].replace(")", "");
                List<String> list = map.getOrDefault(name_cont[1], new ArrayList<String>());
                list.add(values[0] + "/" + name_cont[0]);
                map.put(name_cont[1], list);
            }
        }
        List<List<String>> res = new ArrayList<>();
        for (String key : map.keySet()) {
            if (map.get(key).size() > 1)
                res.add(map.get(key));
        }
        return res;
    }
}
```

## 1292 Maximum Side Length of a Square with Sum Less than or Equal to Threshold

### 原题

给你一个大小为 m x n 的矩阵 mat 和一个整数阈值 threshold。

请你返回元素总和小于或等于阈值的正方形区域的最大边长；如果没有这样的正方形区域，则返回 0 。

Given a `m x n` matrix `mat` and an integer `threshold`. Return the maximum side-length of a square with a sum less than or equal to `threshold` or return **0** if there is no such square.

**Example 1:**![](https://assets.leetcode.com/uploads/2019/12/05/e1.png)

```text
Input: mat = [[1,1,3,2,4,3,2],[1,1,3,2,4,3,2],[1,1,3,2,4,3,2]], threshold = 4
Output: 2
Explanation: The maximum side length of square with sum less than 4 is 2 as shown.
```

**Example 2:**

```text
Input: mat = [[2,2,2,2,2],[2,2,2,2,2],[2,2,2,2,2],[2,2,2,2,2],[2,2,2,2,2]], threshold = 1
Output: 0
```

**Example 3:**

```text
Input: mat = [[1,1,1,1],[1,0,0,0],[1,0,0,0],[1,0,0,0]], threshold = 6
Output: 3
```

**Example 4:**

```text
Input: mat = [[18,70],[61,1],[25,85],[14,40],[11,96],[97,96],[63,45]], threshold = 40184
Output: 2
```

**Constraints:**

* `1 <= m, n <= 300`
* `m == mat.length`
* `n == mat[i].length`
* `0 <= mat[i][j] <= 10000`
* `0 <= threshold <= 10^5`

### 分析

1）时间复杂度：O\(min\(M, N\) \* M \* N\)，其中 M 为矩阵长度，N 为矩阵宽度。

![](../.gitbook/assets/image%20%28159%29.png)

2）时间复杂度：O\(log\(min\(M, N\)\) \* M \* N\)O\(log\(min\(M,N\)\)∗M∗N\)，其中 M 为矩阵长度，N 为矩阵宽度。

![](../.gitbook/assets/image%20%28176%29.png)

### 代码

1）

```java
class Solution {
    public int maxSideLength(int[][] mat, int threshold) {
        int m = mat.length, n = mat[0].length;
        int[][] dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                dp[i][j] = mat[i - 1][j - 1] + dp[i - 1][j] + dp[i][j - 1] - dp[i - 1][j - 1];
            }
        }
        int ans = 0;
        for (int k = 1; k <= Math.min(m, n); k++) {
            for (int i = 1; i <= m; i++) {
                for (int j = 1; j <= n; j++) {
                    if (i - k < 0 || j - k < 0) {
                        continue;
                    }
                    int tmp = dp[i][j] - dp[i - k][j] - dp[i][j - k] + dp[i - k][j - k];
                    if (tmp <= threshold) {
                        ans = Math.max(ans, k);
                    }
                }
            }
        }
        return ans;
    }
}
```

2）

```java
class Solution {
    int m, n;
    int[][] dp;
    public int maxSideLength(int[][] mat, int threshold) {
        m = mat.length;
        n = mat[0].length;
        dp = new int[m + 1][n + 1];
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                dp[i][j] = mat[i - 1][j - 1] + dp[i - 1][j] + dp[i][j - 1] - dp[i - 1][j - 1];
            }
        }
        int l = 0, h = Math.min(m, n);
        while (l <= h) {
            int mid = l + (h - l) / 2;
            if (l == h || l + 1 == h) {
                break;
            }
            if (help(mid, threshold)) {
                l = mid;
            } else {
                h = mid - 1;
            }
        }
        if (help(h, threshold)) {
            return h;
        } else {
            return l;
        }
    }
    public boolean help(int k, int threshold) {
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (i - k < 0 || j - k < 0) {
                    continue;
                }
                if (dp[i][j] - dp[i - k][j] - dp[i][j - k] + dp[i - k][j - k] <= threshold) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

## 968 Binary Tree Cameras

### 原题

监控二叉树

给定一个二叉树，我们在树的节点上安装摄像头。

节点上的每个摄影头都可以监视**其父对象、自身及其直接子对象。**

计算监控树的所有节点所需的最小摄像头数量。

Given a binary tree, we install cameras on the nodes of the tree. 

Each camera at a node can monitor **its parent, itself, and its immediate children**.

Calculate the minimum number of cameras needed to monitor all nodes of the tree.

**Example 1:**![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_01.png)

```text
Input: [0,0,null,0,0]
Output: 1
Explanation: One camera is enough to monitor all nodes if placed as shown.
```

**Example 2:**![](https://assets.leetcode.com/uploads/2018/12/29/bst_cameras_02.png)

```text
Input: [0,0,null,0,null,0,null,null,0]
Output: 2
Explanation: At least two cameras are needed to monitor all nodes of the tree. The above image shows one of the valid configurations of camera placement.
```

  
**Note:**

1. The number of nodes in the given tree will be in the range `[1, 1000]`.
2. **Every** node has value 0.

### 分析

DFS TopDown，叶子节点会返回1。如果一个节点的左右节点有叶子节点就会返回2，并且这个节点需要放camera，否则那个叶子节点就观测不到了。如果一个节点的左右节点有camera了，那么这个节点就返回0，然后继续从所谓的空节点开始。 需要注意的就是，叶子节点的左右子节点是空节点，返回0，左右子节点有camera的节点也返回0。

### 代码

DFS

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 * int val;
 * TreeNode left;
 * TreeNode right;
 * TreeNode() {}
 * TreeNode(int val) { this.val = val; }
 * TreeNode(int val, TreeNode left, TreeNode right) {
 * this.val = val;
 * this.left = left;
 * this.right = right;
 * }
 * }
 */
class Solution {
    int result = 0;

    public int minCameraCover(TreeNode root) {
        if (dfs(root) == 1) {
            result++;
        }
        return result;
    }

    //0:可被观测但无监控，上一层节点为1
    //1：不可被观测到，上一层节点为2
    //2：有摄像机，上一层节点为0
    private int dfs(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int leftStatus = dfs(root.left), rightStatus = dfs(root.right);
        if (leftStatus == 1 || rightStatus == 1) {
            result++;
            return 2;
        } else if (leftStatus == 2 || rightStatus == 2) {
            return 0;
        } else {
            return 1;
        }
    }
}
```

* 1，当前节点有相机
* 2，当前节点不需要相机（子节点有相机把它给覆盖了）
* 3，当前节点没有相机并且也没有被子节点给覆盖（那么他只能等他的父节点把它给覆盖了）

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 * int val;
 * TreeNode left;
 * TreeNode right;
 * TreeNode() {}
 * TreeNode(int val) { this.val = val; }
 * TreeNode(int val, TreeNode left, TreeNode right) {
 * this.val = val;
 * this.left = left;
 * this.right = right;
 * }
 * }
 */
class Solution {
    //NO_CAMERA表示的是子节点没有相机，当前节点也没放相机
    private final int NO_CAMERA = 0;
    //HAS_CAMERA表示当前节点有一个相机
    private final int HAS_CAMERA = 1;
    //NO_NEEDED表示当前节点没有相机，但他的子节点有一个相机，把它给
//覆盖了，所以它不需要了。或者他是一个空的节点也是不需要相机的
    private final int NO_NEEDED = 2;

    //全局的，统计有多少相机
    int res = 0;

    public int minCameraCover(TreeNode root) {
        //边界条件判断
        if (root == null)
            return 0;
        //如果最后返回的是NO_CAMERA，表示root节点的子节点也没有相机，
        //所以root节点要添加一个相机
        if (dfs(root) == NO_CAMERA)
            res++;
        //返回结果
        return res;
    }

    public int dfs(TreeNode root) {
        //如果是空的，就不需要相机了
        if (root == null)
            return NO_NEEDED;
        int left = dfs(root.left), right = dfs(root.right);
        //如果左右子节点有一个是NO_CAMERA，表示的是子节点既没相机，也没相机覆盖它，
        //所以当前节点需要有一个相机
        if (left == NO_CAMERA || right == NO_CAMERA) {
            //在当前节点放一个相机，统计相机的个数
            res++;
            return HAS_CAMERA;
        }
        //如果左右子节点只要有一个有相机，那么当前节点就不需要相机了，否则返回一个没有相机的标记
        return left == HAS_CAMERA || right == HAS_CAMERA ? NO_NEEDED : NO_CAMERA;
    }
}
```

## 347 Top K Frequent Elements

### 原题

 给定一个非空的整数数组，返回其中出现频率前 _**k**_ 高的元素。

Given a non-empty array of integers, return the **k** most frequent elements.

**Example 1:**

```text
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

**Example 2:**

```text
Input: nums = [1], k = 1
Output: [1]
```

**Note:**

* You may assume k is always valid, 1 ≤ k ≤ number of unique elements.
* Your algorithm's time complexity **must be** better than O\(n log n\), where n is the array's size.
* It's guaranteed that the answer is unique, in other words the set of the top k frequent elements is unique.
* You can return the answer in any order.

### 分析

排序就是暴力法

1）堆排序，时间复杂度：O\(Nlogk\)，堆的大小至多为 k；空间复杂度：O\(N\)O\(N\)。哈希表的大小为 O\(N\)，而堆的大小为 O\(k\)，共计为 O\(N\)。

首先遍历整个数组，并使用哈希表记录每个数字出现的次数，并形成一个「出现次数数组」。找出原数组的前 kk 个高频元素，就相当于找出「出现次数数组」的前 kk 大的值。

最简单的做法是给「出现次数数组」排序。但由于可能有 O\(N\)O\(N\) 个不同的出现次数（其中 NN 为原数组长度），故总的算法复杂度会达到 O\(N\log N\)O\(NlogN\)，不满足题目的要求。

在这里，我们可以利用堆的思想：建立一个小顶堆，然后遍历「出现次数数组」：

如果堆的元素个数小于 kk，就可以直接插入堆中。 如果堆的元素个数等于 kk，则检查堆顶与当前出现次数的大小。如果堆顶更大，说明至少有 kk 个数字的出现次数比当前值大，故舍弃当前值；否则，就弹出堆顶，并将当前值插入堆中。 遍历完成后，堆中的元素就代表了「出现次数数组」中前 kk 大的值。

2）基于快速排序，时间复杂度：时间复杂度：最坏O\(N^2\)，空间复杂度：O\(N\)O\(N\)

3）桶排序也可以， **时间复杂度**：O\(n\)， **空间复杂度**O\(n\)

### 代码

1

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> occurrences = new HashMap<Integer, Integer>();
        for (int num : nums) {
            occurrences.put(num, occurrences.getOrDefault(num, 0) + 1);
        }

        // int[] 的第一个元素代表数组的值，第二个元素代表了该值出现的次数
        PriorityQueue<int[]> queue = new PriorityQueue<int[]>(new Comparator<int[]>() {
            public int compare(int[] m, int[] n) {
                return m[1] - n[1];
            }
        });
        for (Map.Entry<Integer, Integer> entry : occurrences.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            if (queue.size() == k) {
                if (queue.peek()[1] < count) {
                    queue.poll();
                    queue.offer(new int[]{num, count});
                }
            } else {
                queue.offer(new int[]{num, count});
            }
        }
        int[] ret = new int[k];
        for (int i = 0; i < k; ++i) {
            ret[i] = queue.poll()[0];
        }
        return ret;
    }
}基于快速排序
```

2

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> occurrences = new HashMap<Integer, Integer>();
        for (int num : nums) {
            occurrences.put(num, occurrences.getOrDefault(num, 0) + 1);
        }

        List<int[]> values = new ArrayList<int[]>();
        for (Map.Entry<Integer, Integer> entry : occurrences.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            values.add(new int[]{num, count});
        }
        int[] ret = new int[k];
        qsort(values, 0, values.size() - 1, ret, 0, k);
        return ret;
    }

    public void qsort(List<int[]> values, int start, int end, int[] ret, int retIndex, int k) {
        int picked = (int) (Math.random() * (end - start + 1)) + start;
        Collections.swap(values, picked, start);
        
        int pivot = values.get(start)[1];
        int index = start;
        for (int i = start + 1; i <= end; i++) {
            if (values.get(i)[1] >= pivot) {
                Collections.swap(values, index + 1, i);
                index++;
            }
        }
        Collections.swap(values, start, index);

        if (k <= index - start) {
            qsort(values, start, index - 1, ret, retIndex, k);
        } else {
            for (int i = start; i <= index; i++) {
                ret[retIndex++] = values.get(i)[0];
            }
            if (k > index - start + 1) {
                qsort(values, index + 1, end, ret, retIndex, k - (index - start + 1));
            }
        }
    }
}
```

3 桶排序，注意方法的singature不一样

```java
//基于桶排序求解「前 K 个高频元素」
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        List<Integer> res = new ArrayList();
        // 使用字典，统计每个元素出现的次数，元素为键，元素出现的次数为值
        HashMap<Integer,Integer> map = new HashMap();
        for(int num : nums){
            if (map.containsKey(num)) {
               map.put(num, map.get(num) + 1);
             } else {
                map.put(num, 1);
             }
        }
        
        //桶排序
        //将频率作为数组下标，对于出现频率不同的数字集合，存入对应的数组下标
        List<Integer>[] list = new List[nums.length+1];
        for(int key : map.keySet()){
            // 获取出现的次数作为下标
            int i = map.get(key);
            if(list[i] == null){
               list[i] = new ArrayList();
            } 
            list[i].add(key);
        }
        
        // 倒序遍历数组获取出现顺序从大到小的排列
        for(int i = list.length - 1;i >= 0 && res.size() < k;i--){
            if(list[i] == null) continue;
            res.addAll(list[i]);
        }
        return res;
    }
}
```

## 1146 Snapshot Array

### 原题

实现支持下列接口的「快照数组」- SnapshotArray：

* SnapshotArray\(int length\) - 初始化一个与指定长度相等的 类数组 的数据结构。初始时，每个元素都等于 0。 
* void set\(index, val\) - 会将指定索引 index 处的元素设置为 val。 
* int snap\(\) - 获取该数组的快照，并返回快照的编号 snap\_id（快照号是调用 snap\(\) 的总次数减去 1）。 
* int get\(index, snap\_id\) - 根据指定的 snap\_id 选择快照，并返回该快照指定索引 index 的值。

Implement a SnapshotArray that supports the following interface:

* `SnapshotArray(int length)` initializes an array-like data structure with the given length.  **Initially, each element equals 0**.
* `void set(index, val)` sets the element at the given `index` to be equal to `val`.
* `int snap()` takes a snapshot of the array and returns the `snap_id`: the total number of times we called `snap()` minus `1`.
* `int get(index, snap_id)` returns the value at the given `index`, at the time we took the snapshot with the given `snap_id`

**Example 1:**

```text
Input: ["SnapshotArray","set","snap","set","get"]
[[3],[0,5],[],[0,6],[0,0]]
Output: [null,null,0,null,5]
Explanation: 
SnapshotArray snapshotArr = new SnapshotArray(3); // set the length to be 3
snapshotArr.set(0,5);  // Set array[0] = 5
snapshotArr.snap();  // Take a snapshot, return snap_id = 0
snapshotArr.set(0,6);
snapshotArr.get(0,0);  // Get the value of array[0] with snap_id = 0, return 5
```

**Constraints:**

* `1 <= length <= 50000`
* At most `50000` calls will be made to `set`, `snap`, and `get`.
* `0 <= index < length`
* `0 <= snap_id <` \(the total number of times we call `snap()`\)
* `0 <= val <= 10^9`

### 分析

1）利用treemap记录snapid对应的list中保存此snapid的索引

2）List&lt;Map&lt;Integer,Integer&gt;&gt;，题意是要构建一个带有不同版本信息的数组，一种方法是每个版本都保存一个数组，但是这样显然产生了太多的冗余 只需要记录每个版本之间有区别的值即可

### 代码

```java
class SnapshotArray {

    Node[] arr;
    int snapTimes;

    public SnapshotArray(int length) {
        this.arr = new Node[length];
        this.snapTimes = 0;
        for(int i = 0; i < length; ++i) 
            arr[i] = new Node(0);
    }
    
    public void set(int index, int val) {
        Node cur = arr[index];
        if(cur.snapId == snapTimes) {
            cur.val = val;
            cur.vals.set(cur.idx, val);
        } else {
            cur.val = val;
            cur.vals.add(val);
            cur.idx++;
            cur.snapId = snapTimes;
            cur.map.put(snapTimes, cur.idx);
        }
        return;
    }
    
    public int snap() {
        snapTimes++;
        return snapTimes - 1;
    }
    
    public int get(int index, int snap_id) {
        Node cur = arr[index];
        if(cur.map.containsKey(snap_id)) {
            int idx = cur.map.get(snap_id);
            return cur.vals.get(idx);
        } else {
            int key = cur.map.lowerKey(snap_id);
            return cur.vals.get(cur.map.get(key));
        }
    }
}

class Node {
    int val;
    int idx;
    int snapId;
    List<Integer> vals;
    TreeMap<Integer, Integer> map;

    Node(int v) {
        this.val = v;
        this.vals = new ArrayList();
        this.idx = 0;
        this.snapId = 0;
        this.map = new TreeMap();
        vals.add(v);
        
        map.put(0, idx);
    }
}

/**
 * Your SnapshotArray object will be instantiated and called as such:
 * SnapshotArray obj = new SnapshotArray(length);
 * obj.set(index,val);
 * int param_2 = obj.snap();
 * int param_3 = obj.get(index,snap_id);
 */

```

```java
class SnapshotArray {
    
    //list.get(index)应为数组下标为index处的值
    //每一个Map记录的是当前下标位置的值的变动，<版本号，新值>
    private List<Map<Integer,Integer>> list;
    private int snapId;

    public SnapshotArray(int length) {
        list = new ArrayList<>();
        for(int i = 0; i < length; i++){
            list.add(new HashMap<Integer,Integer>());
        }
        snapId = 0;
    }
    
    public void set(int index, int val) {
        //在index处放入此次版本更新的值
        list.get(index).put(this.snapId,val);
    }
    
    public int snap() {
        return snapId++;
    }
    
    public int get(int index, int snap_id) {
        //寻找snap_id版本前，最后一次更新的值
        for(int i = snap_id; i >= 0; i--) {
            if (list.get(index).containsKey(snap_id)) {
                return list.get(index).get(snap_id);
            }
            --snap_id;
        }
        return 0;
    }
}

/**
 * Your SnapshotArray object will be instantiated and called as such:
 * SnapshotArray obj = new SnapshotArray(length);
 * obj.set(index,val);
 * int param_2 = obj.snap();
 * int param_3 = obj.get(index,snap_id);
 */
```

## 1223 Dice Roll Simulation

### 原题

有一个骰子模拟器会每次投掷的时候生成一个 1 到 6 的随机数。

不过我们在使用它时有个约束，就是使得投掷骰子时，连续 掷出数字 i 的次数不能超过 rollMax\[i\]（i 从 1 开始编号）。

现在，给你一个整数数组 rollMax 和一个整数 n，请你来计算掷 n 次骰子可得到的不同点数序列的数量。

假如两个序列中至少存在一个元素不同，就认为这两个序列是不同的。由于答案可能很大，所以请返回 模 10^9 + 7 之后的结果。

A die simulator generates a random number from 1 to 6 for each roll. You introduced a constraint to the generator such that it cannot roll the number `i` more than `rollMax[i]` \(1-indexed\) **consecutive** times. 

Given an array of integers `rollMax` and an integer `n`, return the number of distinct sequences that can be obtained with exact `n` rolls.

Two sequences are considered different if at least one element differs from each other. Since the answer may be too large, return it modulo `10^9 + 7`.

**Example 1:**

```text
Input: n = 2, rollMax = [1,1,2,2,2,3]
Output: 34
Explanation: There will be 2 rolls of die, if there are no constraints on the die, there are 6 * 6 = 36 possible combinations. In this case, looking at rollMax array, the numbers 1 and 2 appear at most once consecutively, therefore sequences (1,1) and (2,2) cannot occur, so the final answer is 36-2 = 34.
```

**Example 2:**

```text
Input: n = 2, rollMax = [1,1,1,1,1,1]
Output: 30
```

**Example 3:**

```text
Input: n = 3, rollMax = [1,1,1,2,2,3]
Output: 181
```

**Constraints:**

* `1 <= n <= 5000`
* `rollMax.length == 6`
* `1 <= rollMax[i] <= 15`

### 分析

DP

### 代码

![](../.gitbook/assets/image%20%28167%29.png)

```java
class Solution {
    public int dieSimulator(int n) {
        int[][] dp = new int[n][6];
        // init, roll once, only one sequence end up by every number
        for (int i = 0; i < 6; i++) dp[0][i] = 1;
        // start dp
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < 6; j++) {
                dp[i][j] = Arrays.stream(dp[i-1]).sum();
            }
        }
        return Arrays.stream(dp[n-1]).sum();
    }
}
```

![](../.gitbook/assets/image%20%28143%29.png)

```java
class Solution {
    
    private static int MOD = 1000000007;
    
    public int dieSimulator(int n, int[] rollMax) {
        int[][] dp = new int[n][6];
        for (int i = 0; i < 6; i++) dp[0][i] = 1;
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < 6; j++) {
                dp[i][j] = Arrays.stream(dp[i-1]).reduce(0, (a, b) -> (a + b) % MOD);
                // 参见算法部分第2条
                if (i == rollMax[j]) dp[i][j]--;
                else if (i > rollMax[j]) {
                    for (int k = 0; k < 6; k++) {
                        if (j != k) dp[i][j] = (dp[i][j] - dp[i - rollMax[j] - 1][k] + MOD) % MOD;
                    }
                }
            }
        }
        return Arrays.stream(dp[n-1]).reduce(0, (a, b) -> (a + b) % MOD);
    }
}


```

## 1088 Confusing Number II

### 原题

本题我们会将数字旋转 180° 来生成一个新的数字。

比如 0、1、6、8、9 旋转 180° 以后，我们得到的新数字分别为 0、1、9、8、6。

2、3、4、5、7 旋转 180° 后，是 无法 得到任何数字的。

易混淆数（Confusing Number）指的是一个数字在整体旋转 180° 以后，能够得到一个和原来 不同 的数，且新数字的每一位都应该是有效的。（请注意，旋转后得到的新数字可能大于原数字）

给出正整数 N，请你返回 1 到 N 之间易混淆数字的数量。

We can rotate digits by 180 degrees to form new digits. When 0, 1, 6, 8, 9 are rotated 180 degrees, they become 0, 1, 9, 8, 6 respectively. When 2, 3, 4, 5 and 7 are rotated 180 degrees, they become invalid.

A _confusing number_ is a number that when rotated 180 degrees becomes a **different** number with each digit valid.\(Note that the rotated number can be greater than the original number.\)

Given a positive integer `N`, return the number of confusing numbers between `1` and `N` inclusive.

**Example 1:**

```text
Input: 20
Output: 6
Explanation: 
The confusing numbers are [6,9,10,16,18,19].
6 converts to 9.
9 converts to 6.
10 converts to 01 which is just 1.
16 converts to 91.
18 converts to 81.
19 converts to 61.
```

**Example 2:**

```text
Input: 100
Output: 19
Explanation: 
The confusing numbers are [6,9,10,16,18,19,60,61,66,68,80,81,86,89,90,91,98,99,100].
```

**Note:**

1. `1 <= N <= 10^9`

### 分析

DFS，一个问题，int值不要越界

### 代码

```java
class Solution {
    int[] a = new int[]{0, 1, 6, 8, 9};

    int[] change = new int[]{0, 1, 2, 3, 4, 5, 9, 7, 8, 6};
    int res = 0;

    public int confusingNumberII(int N) {
        for (int i = 1; i < 5 && a[i] <= N; i++) {
            dfs(N, a[i], 1, depth(N));
        }
        return res;
    }

    private void dfs(int N, int cur, int dep, int max) {
        if (max < dep) {
            return;
        }
        if (cur > N) {
            return;
        }
        if (check(cur)) {
            res++;
        }
        for (int i = 0; i < 5; i++) {
            if (cur > Integer.MAX_VALUE / 10) {
                continue;
            }
            dfs(N, cur * 10 + a[i], dep + 1, max);
        }
    }

    private int depth(int x) {
        int res = 0;
        while (x != 0) {
            x /= 10;
            res++;
        }
        return res;
    }

    private boolean check(int x) {
        if (x == 0 || x == 1 || x == 8) {
            return false;
        }
        int pre = x;
        int y = 0;
        while (x != 0) {
            y = y * 10 + change[x % 10];
            x /= 10;
        }
        return pre != y;
    }
}
```

## 351 Android Unlock Patterns

### 原题

我们都知道安卓有个手势解锁的界面，是一个 3 x 3 的点所绘制出来的网格。

给你两个整数，分别为 ​​m 和 n，其中 1 ≤ m ≤ n ≤ 9，那么请你统计一下有多少种解锁手势，是至少需要经过 m 个点，但是最多经过不超过 n 个点的。

先来了解下什么是一个有效的安卓解锁手势:

1. 每一个解锁手势必须至少经过 m 个点、最多经过 n 个点。 
2. 解锁手势里不能设置经过重复的点。 
3. 假如手势中有两个点是顺序经过的，那么这两个点的手势轨迹之间是绝对不能跨过任何未被经过的点。 
4. 经过点的顺序不同则表示为不同的解锁手势。

Given an Android **3x3** key lock screen and two integers **m** and **n**, where 1 ≤ m ≤ n ≤ 9, count the total number of unlock patterns of the Android lock screen, which consist of minimum of **m** keys and maximum **n** keys.

**Rules for a valid pattern:**

1. Each pattern must connect at least **m** keys and at most **n** keys.
2. All the keys must be distinct.
3. If the line connecting two consecutive keys in the pattern passes through any other keys, the other keys must have previously selected in the pattern. No jumps through non selected key is allowed.
4. The order of keys used matters.

```text

```

**Explanation:**

```text
| 1 | 2 | 3 |
| 4 | 5 | 6 |
| 7 | 8 | 9 |
```

**Invalid move:** `4 - 1 - 3 - 6`  
Line 1 - 3 passes through key 2 which had not been selected in the pattern.

**Invalid move:** `4 - 1 - 9 - 2`  
Line 1 - 9 passes through key 5 which had not been selected in the pattern.

**Valid move:** `2 - 4 - 1 - 3 - 6`  
Line 1 - 3 is valid because it passes through key 2, which had been selected in the pattern

**Valid move:** `6 - 5 - 4 - 1 - 9 - 2`  
Line 1 - 9 is valid because it passes through key 5, which had been selected in the pattern.

**Example:**

```text
Input: m = 1, n = 1
Output: 9
```

### 分析

DFS回溯

![](../.gitbook/assets/image%20%28144%29.png)

时间复杂度：O\(n!\)，空间复杂度：O\(n\)

### 代码

```java
public class Solution {

    private boolean used[] = new boolean[9];

    public int numberOfPatterns(int m, int n) {
        int res = 0;
        for (int len = m; len <= n; len++) {
            res += calcPatterns(-1, len);
            for (int i = 0; i < 9; i++) {
                used[i] = false;
            }
        }
        return res;
    }

    private boolean isValid(int index, int last) {
        if (used[index])
            return false;
        // first digit of the pattern    
        if (last == -1)
            return true;
        // knight moves or adjacent cells (in a row or in a column)	       
        if ((index + last) % 2 == 1)
            return true;
        // indexes are at both end of the diagonals for example 0,0, and 8,8          
        int mid = (index + last) / 2;
        if (mid == 4)
            return used[mid];
        // adjacent cells on diagonal  - for example 0,0 and 1,0 or 2,0 and //1,1
        if ((index % 3 != last % 3) && (index / 3 != last / 3)) {
            return true;
        }
        // all other cells which are not adjacent
        return used[mid];
    }

    private int calcPatterns(int last, int len) {
        if (len == 0)
            return 1;
        int sum = 0;
        for (int i = 0; i < 9; i++) {
            if (isValid(i, last)) {
                used[i] = true;
                sum += calcPatterns(i, len - 1);
                used[i] = false;
            }
        }
        return sum;
    }
}
```

## 1218 Longest Arithmetic Subsequence of Given Difference

### 原题

最长定差子序列，给你一个整数数组 arr 和一个整数 difference，请你找出 arr 中所有相邻元素之间的差等于给定 difference 的等差子序列，并返回其中最长的等差子序列的长度。

Given an integer array `arr` and an integer `difference`, return the length of the longest subsequence in `arr` which is an arithmetic sequence such that the difference between adjacent elements in the subsequence equals `difference`.

**Example 1:**

```text
Input: arr = [1,2,3,4], difference = 1
Output: 4
Explanation: The longest arithmetic subsequence is [1,2,3,4].
```

**Example 2:**

```text
Input: arr = [1,3,5,7], difference = 1
Output: 1
Explanation: The longest arithmetic subsequence is any single element.
```

**Example 3:**

```text
Input: arr = [1,5,7,8,5,3,4,2,1], difference = -2
Output: 4
Explanation: The longest arithmetic subsequence is [7,5,3,1].
```

**Constraints:**

* `1 <= arr.length <= 10^5`
* `-10^4 <= arr[i], difference <= 10^4`

### 分析

![](../.gitbook/assets/image%20%28154%29.png)

### 代码

```java
class Solution {
    public int longestSubsequence(int[] arr, int difference) {
        int ans = 1;
        Map<Integer, Integer> map = new HashMap<>();
        for (int i: arr) {
            int temp = map.getOrDefault(i - difference, 0) + 1;
            map.put(i, temp);
            ans = Math.max(ans, temp);
        }
        return ans;
    }
}
```

## 1125 Smallest Sufficient Team

### 原题

最小的必要团队，作为项目经理，你规划了一份需求的技能清单 req\_skills，并打算从备选人员名单 people 中选出些人组成一个「必要团队」（ 编号为 i 的备选人员 people\[i\] 含有一份该备选人员掌握的技能列表）。

所谓「必要团队」，就是在这个团队中，对于所需求的技能列表 req\_skills 中列出的每项技能，团队中至少有一名成员已经掌握。

我们可以用每个人的编号来表示团队中的成员：例如，团队 team = \[0, 1, 3\] 表示掌握技能分别为 people\[0\]，people\[1\]，和 people\[3\] 的备选人员。

请你返回 任一 规模最小的必要团队，团队成员用人员编号表示。你可以按任意顺序返回答案，本题保证答案存在。

In a project, you have a list of required skills `req_skills`, and a list of `people`.  The i-th person `people[i]` contains a list of skills that person has.

Consider a _sufficient team_: a set of people such that for every required skill in `req_skills`, there is at least one person in the team who has that skill.  We can represent these teams by the index of each person: for example, `team = [0, 1, 3]` represents the people with skills `people[0]`, `people[1]`, and `people[3]`.

Return **any** sufficient team of the smallest possible size, represented by the index of each person.

You may return the answer in any order.  It is guaranteed an answer exists.

**Example 1:**

```text
Input: req_skills = ["java","nodejs","reactjs"], people = [["java"],["nodejs"],["nodejs","reactjs"]]
Output: [0,2]
```

**Example 2:**

```text
Input: req_skills = ["algorithms","math","java","reactjs","csharp","aws"], people = [["algorithms","math","java"],["algorithms","math","reactjs"],["java","csharp","aws"],["reactjs","csharp"],["csharp","math"],["aws","java"]]
Output: [1,2]
```

**Constraints:**

* `1 <= req_skills.length <= 16`
* `1 <= people.length <= 60`
* `1 <= people[i].length, req_skills[i].length, people[i][j].length <= 16`
* Elements of `req_skills` and `people[i]` are \(respectively\) distinct.
* `req_skills[i][j], people[i][j][k]` are lowercase English letters.
* Every skill in `people[i]` is a skill in `req_skills`.
* It is guaranteed a sufficient team exists.

### 分析

DFS，回溯 + 剪枝

看到这个问题的时候，第一想法就是回溯，奈何一直超时，然后一路调试优化，从超时到2000+ms再到18ms最后到5ms，最终优化的逻辑如下 

1.由于技能req\_skills最多16个，则可以用二进制1，10，100...来表示从第一个到最后一个技能，由此可以将员工技能转换为对应第数字。 

2.若存在两个员工技能重复可以去重，或者一员工技能是另外员工技能真子集，则该员工必不在最优解中，去除该员工，从而提高回溯效率 

3.如果该员工集合中存在无交集员工（和其他所有员工技能不重复），那么该员工一定在最终解中，则可以得出无交集员工集合作为解的子集。 

4.判断无交集员工技能是否是解，若是，则肯定是最优解，否则说明还缺少其他员工，则可以根据该集合员工数量+1来作为最小回溯深度来进行回溯，从而降低回溯深度。 

5.对员工技能数peopleSkillNums进行排序，为了加快查找速度（主要为了对员工技能逐个匹配，例如匹配下一个技能员工时，只要已匹配技能中没有包含该技能，则添加） 

6.从最小回溯深度开始回溯，对技能数进行或运算，若有解，则肯定是最优解，直接结束，若无解，则回溯深度+1，继续回溯

### 代码

```java
class Solution {
    //成员技能对应二进制数字都数组下标
    private int[] peopleSubscript;

    //当前递归深度
    private int currentDepth = 0;

    public int[] smallestSufficientTeam(String[] req_skills, List<List<String>> people) {
        int[] result = null;
        int skillLen = req_skills.length, mustSkill = (1 << skillLen) - 1, peopleSize = people.size();
        //技能对应二进制，用1，10，100来表示
        Map<String, Integer> skillMap = new HashMap<String, Integer>(skillLen << 1);
        for (int i = 0; i < skillLen; i++) {
            skillMap.put(req_skills[i], 1 << i);
        }
        //员工技能对应二进制数
        int[] peopleSkillNums = new int[peopleSize];
        peopleSubscript = new int[1 << skillLen];
        for (int i = 0; i < people.size(); i++) {
            int skillNum = 0;
            List<String> skills = people.get(i);
            for (String skill : skills) {
                skillNum += skillMap.get(skill);
            }
            peopleSkillNums[i] = skillNum;
            peopleSubscript[skillNum] = i;
        }
        //技能或运算结果
        int comSkills = 0;
        //和其他所有员工没交集对应员工数量
        int aloneCount = 0;
        //和其他所有员工没交集对应员工数组下标
        int[] noIntersectionArr = new int[skillLen];
        for (int i = 0; i < peopleSize; i++) {
            if (peopleSkillNums[i] == 0) {
                continue;
            }
            //是否无交集
            boolean isNoIntersection = true;
            for (int j = 0; j < peopleSize; j++) {
                if (peopleSkillNums[j] == 0 || i == j) {
                    continue;
                }
                //重复的，去重
                if (peopleSkillNums[i] == peopleSkillNums[j]) {
                    peopleSkillNums[j] = 0;
                    continue;
                }
                //若一个员工技能是另外一个员工子集，则必定不在最优解中，去除
                if ((peopleSkillNums[i] | peopleSkillNums[j]) == peopleSkillNums[j]) {
                    peopleSkillNums[i] = 0;
                    isNoIntersection = false;
                    break;
                } else if ((peopleSkillNums[i] | peopleSkillNums[j]) == peopleSkillNums[i]) {
                    peopleSkillNums[j] = 0;
                    continue;
                }
                if ((peopleSkillNums[i] & peopleSkillNums[j]) != 0) {
                    isNoIntersection = false;
                    break;
                }
            }
            //无交集员工提前保存，方便之后回溯（降低后续回溯深度）
            if (isNoIntersection) {
                comSkills |= peopleSkillNums[i];
                noIntersectionArr[aloneCount] = peopleSubscript[peopleSkillNums[i]];
                peopleSkillNums[i] = 0;
                aloneCount++;
            }
        }
        //员工技能数字排序
        Arrays.sort(peopleSkillNums);
        //最小回溯深度，由小到大，则第一个得到结果就为最优解
        int minDepth = aloneCount;
        //若无交集员工技能组成等于必须技能，则输出结果，否则开始回溯深度+1
        if (comSkills == mustSkill) {
            result = new int[aloneCount];
            System.arraycopy(noIntersectionArr, 0, result, 0, aloneCount);
            return result;
        } else {
            minDepth++;
        }
        //从minDepth回溯深度开始回溯，noIntersectionArr肯定在该结果中，回溯深度从无交集员工数量开始
        for (int i = minDepth; i < skillLen; i++) {
            currentDepth = i;
            result = new int[i];
            System.arraycopy(noIntersectionArr, 0, result, 0, aloneCount);
            if (addNextPeople(mustSkill, comSkills, result, peopleSkillNums, aloneCount)) {
                break;
            }
        }
        return result;
    }

    private boolean addNextPeople(int mustSkill, int comSkills, int[] result, int[] peopleSkillNums, int count) {
        //判断是否为解
        if (mustSkill == comSkills) {
            return true;
        }
        //大于回溯深度，则不存在
        if (count >= currentDepth) {
            return false;
        }
        for (int i = peopleSkillNums.length - 1; i >= 0; i--) {
            int skillNum = peopleSkillNums[i];
            //由于排序，则技能为0则后续都为0，直接结束
            if (skillNum == 0) {
                break;
            }
            //组合技能已包含该技能，则跳过
            if ((comSkills | skillNum) == comSkills) {
                continue;
            }
            result[count] = peopleSubscript[peopleSkillNums[i]];
            peopleSkillNums[i] = 0;
            if (addNextPeople(mustSkill, comSkills | skillNum, result, peopleSkillNums, count + 1)) {
                return true;
            }
            peopleSkillNums[i] = skillNum;
        }
        return false;
    }
}
```

## 1466 Reorder Routes to Make All Paths Lead to the City Zero

### 原题

n 座城市，从 0 到 n-1 编号，其间共有 n-1 条路线。因此，要想在两座不同城市之间旅行只有唯一一条路线可供选择（路线网形成一颗树）。去年，交通运输部决定重新规划路线，以改变交通拥堵的状况。

路线用 connections 表示，其中 connections\[i\] = \[a, b\] 表示从城市 a 到 b 的一条有向路线。

今年，城市 0 将会举办一场大型比赛，很多游客都想前往城市 0 。

请你帮助重新规划路线方向，使每个城市都可以访问城市 0 。返回需要变更方向的最小路线数。

题目数据 保证 每个城市在重新规划路线方向后都能到达城市 0 。

There are `n` cities numbered from `0` to `n-1` and `n-1` roads such that there is only one way to travel between two different cities \(this network form a tree\). Last year, The ministry of transport decided to orient the roads in one direction because they are too narrow.

Roads are represented by `connections` where `connections[i] = [a, b]` represents a road from city `a` to `b`.

This year, there will be a big event in the capital \(city 0\), and many people want to travel to this city.

Your task consists of reorienting some roads such that each city can visit the city 0. Return the **minimum** number of edges changed.

It's **guaranteed** that each city can reach the city 0 after reorder.

**Example 1:**

![](https://assets.leetcode.com/uploads/2020/05/13/sample_1_1819.png)

```text
Input: n = 6, connections = [[0,1],[1,3],[2,3],[4,0],[4,5]]
Output: 3
Explanation: Change the direction of edges show in red such that each node can reach the node 0 (capital).
```

**Example 2:**

![](https://assets.leetcode.com/uploads/2020/05/13/sample_2_1819.png)

```text
Input: n = 5, connections = [[1,0],[1,2],[3,2],[3,4]]
Output: 2
Explanation: Change the direction of edges show in red such that each node can reach the node 0 (capital).
```

**Example 3:**

```text
Input: n = 3, connections = [[1,0],[2,0]]
Output: 0
```

**Constraints:**

* `2 <= n <= 5 * 10^4`
* `connections.length == n-1`
* `connections[i].length == 2`
* `0 <= connections[i][0], connections[i][1] <= n-1`
* `connections[i][0] != connections[i][1]`

### 分析

BFS

1. 同课程安排这个题，我们也是用HashMap的临接表形式存储图，也同样是用BFS来遍历。
2. 使用BFS的话，这道问题的关键就变成了保存一个无向图，而用其他方式来保存方向以计算。
3. 所以我们构建HashMap的时候，就一下子保存两个方向，因为我们是“忽略了”方向的，所以在邻接表中需要统计两个方向。（如果感觉绕的话……不妨看一下无向图的邻接表方法） 但是我们又不能真的忽略方向，因为这样BFS就没法计算需要变更的线路，所以我对反方向取了个负数。最后即可得出一个邻接表
4. 此时我们再进行bfs，该有的队列，访问数组依然都有，因为map中保存了可达路径，所以只要有关联，不管方向，都可以被遍历到。重点在于如果发现正数，我们就要调换方向（也就是让ans++）。

补邻接表： 可以看到无向图其实就是一种特殊的有向图，只不过相连的两个节点中有两个箭头指向 那么在本题中，如果考虑方向，就如左上及左下图所示。不考虑方向，就需要如右上及右下所示，能加的箭头都加上。

![](../.gitbook/assets/image%20%28142%29.png)

### 代码

```java
class Solution {
    public int minReorder(int n, int[][] connections) {
        //遍历整个数组,构建一个可达图，这里的可达的定义为联通即可，不在乎方向（但是这里我用负数代表了反方向，方便后续统计）
        HashMap<Integer, HashSet<Integer>> map = new HashMap<>();
        for (int[] val : connections) {
            HashSet<Integer> set = map.containsKey(val[0]) ? map.get(val[0]) : new HashSet<>();
            set.add(val[1]);
            map.put(val[0], set);
            HashSet<Integer> setReverse = map.containsKey(val[1]) ? map.get(val[1]) : new HashSet<>();
            setReverse.add(val[0] * -1);
            map.put(val[1], setReverse);
        }
        return mybfs(map, n);
    }

    private int mybfs(HashMap<Integer, HashSet<Integer>> map, int n) {
        boolean[] tBool = new boolean[n];
        LinkedList<Integer> queue = new LinkedList<>();
        int ans = 0;
        queue.offerLast(0);
        tBool[0] = true;
        while (!queue.isEmpty()) {
            for (Integer i : map.get(queue.pollFirst())) {
                //访问之后禁止再访问
                if (tBool[Math.abs(i)]) continue;
                //前面说到，我们用负数代表了反方向，这里就用作统计了，如果是正数，就说明方向需要调整，建议仔细品味下
                if (i > 0) ans++;
                tBool[Math.abs(i)] = true;
                queue.offerLast(Math.abs(i));
            }
        }
        return ans;
    }
}
```

## 855 Exam Room

### 原题

在考场里，一排有 N 个座位，分别编号为 0, 1, 2, ..., N-1 。

当学生进入考场后，他必须坐在能够使他与离他最近的人之间的距离达到最大化的座位上。如果有多个这样的座位，他会坐在编号最小的座位上。\(另外，如果考场里没有人，那么学生就坐在 0 号座位上。\)

返回 ExamRoom\(int N\) 类，它有两个公开的函数：其中，函数 ExamRoom.seat\(\) 会返回一个 int （整型数据），代表学生坐的位置；函数 ExamRoom.leave\(int p\) 代表坐在座位 p 上的学生现在离开了考场。每次调用 ExamRoom.leave\(p\) 时都保证有学生坐在座位 p 上。

In an exam room, there are `N` seats in a single row, numbered `0, 1, 2, ..., N-1`.

When a student enters the room, they must sit in the seat that maximizes the distance to the closest person.  If there are multiple such seats, they sit in the seat with the lowest number.  \(Also, if no one is in the room, then the student sits at seat number 0.\)

Return a class `ExamRoom(int N)` that exposes two functions: `ExamRoom.seat()` returning an `int` representing what seat the student sat in, and `ExamRoom.leave(int p)` representing that the student in seat number `p` now leaves the room.  It is guaranteed that any calls to `ExamRoom.leave(p)` have a student sitting in seat `p`.

**Example 1:**

```text
Input: ["ExamRoom","seat","seat","seat","seat","leave","seat"], [[10],[],[],[],[],[4],[]]
Output: [null,0,9,4,2,null,5]
Explanation:
ExamRoom(10) -> null
seat() -> 0, no one is in the room, then the student sits at seat number 0.
seat() -> 9, the student sits at the last seat number 9.
seat() -> 4, the student sits at the last seat number 4.
seat() -> 2, the student sits at the last seat number 2.
leave(4) -> null
seat() -> 5, the student sits at the last seat number 5.
```

​​​​​​​

**Note:**

1. `1 <= N <= 10^9`
2. `ExamRoom.seat()` and `ExamRoom.leave()` will be called at most `10^4` times across all test cases.
3. Calls to `ExamRoom.leave(p)` are guaranteed to have a student currently sitting in seat number `p`.

### 分析

维护有序的座位编号 

我们可以用有序集合（Java 中 TreeSet，C++ 中的 set）存储目前有学生的座位编号。当我们要调用 leave\(p\) 函数时，我们只需要把有序集合中的 p 移除即可。当我们要调用 seat\(\) 函数时，我们遍历这个有序集合，对于相邻的两个座位 i 和 j，如果选择在这两个座位之间入座，那么最近的距离 d 为 \(j - i\) / 2，选择的座位为 i + d。除此之外，我们还需要考虑坐在最左侧 0 和最右侧 N - 1 的情况。

### 代码

```java
class ExamRoom {
    int N;
    TreeSet<Integer> students;

    public ExamRoom(int N) {
        this.N = N;
        students = new TreeSet();
    }

    public int seat() {
        //Let's determine student, the position of the next
        //student to sit down.
        int student = 0;
        if (students.size() > 0) {
            //Tenatively, dist is the distance to the closest student,
            //which is achieved by sitting in the position 'student'.
            //We start by considering the left-most seat.
            int dist = students.first();
            Integer prev = null;
            for (Integer s: students) {
                if (prev != null) {
                    //For each pair of adjacent students in positions (prev, s),
                    //d is the distance to the closest student;
                    //achieved at position prev + d.
                    int d = (s - prev) / 2;
                    if (d > dist) {
                        dist = d;
                        student = prev + d;
                    }
                }
                prev = s;
            }

            //Considering the right-most seat.
            if (N - 1 - students.last() > dist)
                student = N - 1;
        }

        //Add the student to our sorted TreeSet of positions.
        students.add(student);
        return student;
    }

    public void leave(int p) {
        students.remove(p);
    }
}
```

## 684 Redundant Connection

### 原题

在本问题中, 树指的是一个连通且无环的无向图。

输入一个图，该图由一个有着N个节点 \(节点值不重复1, 2, ..., N\) 的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。

结果图是一个以边组成的二维数组。每一个边的元素是一对\[u, v\] ，满足 u &lt; v，表示连接顶点u 和v的无向图的边。

返回一条可以删去的边，使得结果图是一个有着N个节点的树。如果有多个答案，则返回二维数组中最后出现的边。答案边 \[u, v\] 应满足相同的格式 u &lt; v。

来源：力扣（LeetCode） 链接：[https://leetcode-cn.com/problems/redundant-connection](https://leetcode-cn.com/problems/redundant-connection) 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

In this problem, a tree is an **undirected** graph that is connected and has no cycles.

The given input is a graph that started as a tree with N nodes \(with distinct values 1, 2, ..., N\), with one additional edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of `edges`. Each element of `edges` is a pair `[u, v]` with `u < v`, that represents an **undirected** edge connecting nodes `u` and `v`.

Return an edge that can be removed so that the resulting graph is a tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array. The answer edge `[u, v]` should be in the same format, with `u < v`.

**Example 1:**  


```text
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given undirected graph will be like this:
  1
 / \
2 - 3
```

**Example 2:**  


```text
Input: [[1,2], [2,3], [3,4], [1,4], [1,5]]
Output: [1,4]
Explanation: The given undirected graph will be like this:
5 - 1 - 2
    |   |
    4 - 3
```

**Note:**  


The size of the input 2D-array will be between 3 and 1000.

Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

**Update \(2017-09-26\):**  
We have overhauled the problem description + test cases and specified clearly the graph is an **undirected** graph. For the **directed** graph follow up please see [**Redundant Connection II**](https://leetcode.com/problems/redundant-connection-ii/description/)\). We apologize for any inconvenience caused.

### 分析

![](../.gitbook/assets/image%20%28182%29.png)

![](../.gitbook/assets/image%20%28181%29.png)

### 代码

```java
class Solution {
    //缓存结果集
    int[] result = new int[2];

    public int[] findRedundantConnection(int[][] edges) {
        int[] father = new int[edges.length + 1];
        for (int i = 0; i < father.length; i++) {
            father[i] = i;
        }
        for (int[] edge : edges) {
            union(father, edge[0], edge[1]);
        }
        return result;
    }

    //路径压缩
    public int findXFather(int[] father, int x) {
        while (father[x]!=x){
            father[x] = father[father[x]];
            x = father[x];
        }
        return x;
    }

    //合并两个能连接上的点，father合为最后确定的father
    public void union(int[] father, int x, int y) {
        int xFather = findXFather(father, x);
        int yFather = findXFather(father, y);
        if (xFather != yFather) {
            father[xFather]=yFather;
        } else {
            //在发现两个点的连接已经存在时，就更新缓存，题目要最后一个，遍历到最后一个就是结果
            result[0] = x;
            result[1] = y;
        }
    }

}

作者：luma730
链接：https://leetcode-cn.com/problems/redundant-connection/solution/java-bing-cha-ji-by-luma730/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 685 Redundant Connection II

### 原题

在本问题中，有根树指满足以下条件的有向图。该树只有一个根节点，所有其他节点都是该根节点的后继。每一个节点只有一个父节点，除了根节点没有父节点。

输入一个有向图，该图由一个有着N个节点 \(节点值不重复1, 2, ..., N\) 的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。

结果图是一个以边组成的二维数组。 每一个边 的元素是一对 \[u, v\]，用以表示有向图中连接顶点 u 和顶点 v 的边，其中 u 是 v 的一个父节点。

返回一条能删除的边，使得剩下的图是有N个节点的有根树。若有多个答案，返回最后出现在给定二维数组的答案。

来源：力扣（LeetCode） 链接：[https://leetcode-cn.com/problems/redundant-connection-ii](https://leetcode-cn.com/problems/redundant-connection-ii) 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

In this problem, a rooted tree is a **directed** graph such that, there is exactly one node \(the root\) for which all other nodes are descendants of this node, plus every node has exactly one parent, except for the root node which has no parents.

The given input is a directed graph that started as a rooted tree with N nodes \(with distinct values 1, 2, ..., N\), with one additional directed edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of `edges`. Each element of `edges` is a pair `[u, v]` that represents a **directed** edge connecting nodes `u` and `v`, where `u` is a parent of child `v`.

Return an edge that can be removed so that the resulting graph is a rooted tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array.

**Example 1:**  


```text
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given directed graph will be like this:
  1
 / \
v   v
2-->3
```

**Example 2:**  


```text
Input: [[1,2], [2,3], [3,4], [4,1], [1,5]]
Output: [4,1]
Explanation: The given directed graph will be like this:
5 <- 1 -> 2
     ^    |
     |    v
     4 <- 3
```

**Note:**  


The size of the input 2D-array will be between 3 and 1000.

Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

### 分析

并查集

### 代码

```java
class Solution {
   
   int[] result = new int[2];
    int doubleRoot = 0;
    int[] hadRoot;
    int[][] rootResult = new int[2][2];

    public int[] findRedundantDirectedConnection(int[][] edges) {
        hadRoot = new int[edges.length + 1];
        int[] father = new int[edges.length + 1];
        for (int i = 0; i < father.length; i++) {
            father[i] = i;
        }

        for (int[] edge : edges) {
            hadRoot[edge[1]]++;
            if (hadRoot[edge[1]] == 2) {
                doubleRoot = edge[1];
                rootResult[1] = edge;
            } else {
                union(father, edge[1], edge[0]);
            }
        }
        if (doubleRoot != 0) {
            for (int[] edge : edges) {
                if (edge[1] == doubleRoot) {
                    rootResult[0] = edge;
                    break;
                }
            }
            int root = 0;
            for (int i = 1; i < father.length; i++) {
                if (root == 0) {
                    root = findXFather(father,i);
                }
                if (root != findXFather(father,i)) {
                    return rootResult[0];
                }
            }
            return rootResult[1];
        }
        return result;
    }

    public int findXFather(int[] father, int x) {
        while (father[x] != x) {
            father[x] = father[father[x]];
            x = father[x];
        }
        return x;
    }

    public void union(int[] father, int x, int y) {
        int xFather = findXFather(father, x);
        int yFather = findXFather(father, y);
        if (xFather != yFather) {
            father[xFather] = yFather;
        } else {
            result[0] = y;
            result[1] = x;
        }
    }


}
```

## 

### 原题

### 分析

### 代码



