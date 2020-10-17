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

![](../.gitbook/assets/image%20%28150%29.png)

![](../.gitbook/assets/image%20%28144%29.png)

![](../.gitbook/assets/image%20%28155%29.png)

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

![](../.gitbook/assets/image%20%28156%29.png)

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

![](../.gitbook/assets/image%20%28154%29.png)

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

![](../.gitbook/assets/image%20%28148%29.png)

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

![](../.gitbook/assets/image%20%28147%29.png)

3\) 状态压缩为以为DP

dp 数组中的每一个位置的值只依赖于上一行。因此不需要将整个矩阵的结果全部存储，只需要保留上一行的结果即可。而对于横向的 dp 数组，由于其不依赖于上一行，上一行的结果也可以不存储。这样可以达到节省空间的效果。

![](../.gitbook/assets/image%20%28152%29.png)

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

![](../.gitbook/assets/image%20%28146%29.png)

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

![](../.gitbook/assets/image%20%28142%29.png)

![](../.gitbook/assets/image%20%28153%29.png)

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

![](../.gitbook/assets/image%20%28157%29.png)

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

![](../.gitbook/assets/image%20%28151%29.png)

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

![](../.gitbook/assets/image%20%28149%29.png)

* 时间复杂度：O\(C^2\)。其中 C 是给定数组中所有字符串的字符总数目。
* 空间复杂度：O\(C\)

2）

![](../.gitbook/assets/image%20%28145%29.png)

* 时间复杂度： O\(ClogC\)，其中 C 是给定数组所有单词的总字符数。该算法时间主要耗费在排序上。
* 空间复杂度： O\(C\)

3\) 

![](../.gitbook/assets/image%20%28143%29.png)

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

## 

### 原题

### 分析

### 代码

## 

### 原题

### 分析

### 代码

## 

### 原题

### 分析

### 代码

## 

### 原题

### 分析

### 代码

## 

### 原题

### 分析

### 代码

## 

### 原题

### 分析

### 代码

