# tag0

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

BFS前序遍历，第二种格式

```java
class Codec {

    // Encodes a tree to a single string.
    public String serialize(Node root) {
        if (root == null) {
            return "";
        }
        StringBuilder sb = new StringBuilder();
        Queue<Node> queue = new LinkedList<Node>();
        queue.offer(root);
        sb.append(root.val).append(",").append(root.children.size()).append(",");
        while (!queue.isEmpty()) {
            Node cur = queue.poll();
            for (Node node : cur.children) {
                queue.offer(node);
                sb.append(node.val).append(",").append(node.children.size()).append(",");
            }
        }
        return sb.toString(); 
    }

    // Decodes your encoded data to tree.
    public Node deserialize(String data) {
        if (data.length() == 0) {
            return null;
        }
        String[] nodes = data.split(",");
        Queue<Node> queue = new LinkedList<Node>();
        Queue<Integer> childQueue = new LinkedList<Integer>();
        Node root = new Node(Integer.valueOf(nodes[0]));
        queue.offer(root);
        childQueue.offer(Integer.valueOf(nodes[1]));
        int i = 2;
        while (!queue.isEmpty()) {
            Node cur = queue.poll();
            cur.children = new ArrayList<>();
            int n = childQueue.poll();
            for (int j = 0; j < n; j++) {
                Node child = new Node(Integer.valueOf(nodes[i++]));
                childQueue.offer(Integer.valueOf(nodes[i++]));
                queue.offer(child);
                cur.children.add(child);
            }
        }
        return root;
    }
}
```

JS直接使用API

```java
/**
 * // Definition for a Node.
 * function Node(val, children) {
 *    this.val = val;
 *    this.children = children;
 * };
 */

class Codec {
  constructor() {}

  /**
   * @param {Node} root
   * @return {string}
   */
  // Encodes a tree to a single string.
  serialize = function (root) {
    return JSON.stringify(root)
  }

  /**
   * @param {string} data
   * @return {Node}
   */
  // Decodes your encoded data to tree.
  deserialize = function (data) {
    return JSON.parse(data)
  }
}

// Your Codec object will be instantiated and called as such:
// Codec codec = new Codec();
// codec.deserialize(codec.serialize(root));
```

## 1438 Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit 

### 原题

给你一个整数数组 nums ，和一个表示限制的整数 limit，请你返回最长连续子数组的长度，该子数组中的任意两个元素之间的绝对差必须小于或者等于 limit 。

如果不存在满足条件的子数组，则返回 0 。

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

## 

