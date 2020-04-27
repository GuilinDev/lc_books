# Graph

## 269 Alien Dictionary

### 原题概述

There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of **non-empty** words from the dictionary, where **words are sorted lexicographically by the rules of this new language**. Derive the order of letters in this language.

**Example 1:**

```text
Input:
[
  "wrt",
  "wrf",
  "er",
  "ett",
  "rftt"
]

Output: "wertf"
```

**Example 2:**

```text
Input:
[
  "z",
  "x"
]

Output: "zx"
```

**Example 3:**

```text
Input:
[
  "z",
  "x",
  "z"
] 

Output: "" 

Explanation: The order is invalid, so return "".
```

**Note:**

1. You may assume all letters are in lowercase.
2. You may assume that if a is a prefix of b, then a must appear before b in the given dictionary.
3. If the order is invalid, return an empty string.
4. There may be multiple valid order of letters, return any one of them is fine.

### 题意和分析

这道题让给了我们一些按“字母顺序”排列的单词，但是这个字母顺序不是我们熟知的顺序，而是另类的顺序，让我们根据这些“有序”的单词来找出新的字母顺序，这实际上是一道有向图的问题。跟207 Couurse Schedule，210 Couurse Schedule II和630 Couurse ScheduleIII类似。

1\)DFS，建立一个二维的boolean数组g，然后把出现过的字母的对应的位置都赋为true，然后我们把可以推出的顺序的对应位置也赋为true，然后我们就开始递归调用，如果递归函数有返回false的，说明有冲突，则返回false，递归调用结束后结果res中存了入度不为0的字母，最后把入度为0的字母加到最后面，最后把结果result翻转一下即可；

2\) BFS

3\) Topological Sorting

### 代码

DFS，参考[这里](https://leetcode.com/problems/alien-dictionary/discuss/70115/3ms-Clean-Java-Solution-%28DFS%29)

```java
class Solution {
    private final int N = 26;
    public String alienOrder(String[] words) {
        boolean[][] adj = new boolean[N][N];
        int[] visited = new int[N];
        buildGraph(words, adj, visited);

        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < N; i++) {
            if(visited[i] == 0) {                 // unvisited
                if(!dfs(adj, visited, sb, i)) return "";
            }
        }
        return sb.reverse().toString();
    }

    public boolean dfs(boolean[][] adj, int[] visited, StringBuilder sb, int i) {
        visited[i] = 1;                            // 1 = visiting
        for(int j = 0; j < N; j++) {
            if(adj[i][j]) {                        // connected
                if(visited[j] == 1) return false;  // 1 => 1, cycle   
                if(visited[j] == 0) {              // 0 = unvisited
                    if(!dfs(adj, visited, sb, j)) return false;
                }
            }
        }
        visited[i] = 2;                           // 2 = visited
        sb.append((char) (i + 'a'));
        return true;
    }

    public void buildGraph(String[] words, boolean[][] adj, int[] visited) {
        Arrays.fill(visited, -1);                 // -1 = not even existed
        for(int i = 0; i < words.length; i++) {
            for(char c : words[i].toCharArray()) visited[c - 'a'] = 0;
            if(i > 0) {
                String w1 = words[i - 1], w2 = words[i];
                int len = Math.min(w1.length(), w2.length());
                for(int j = 0; j < len; j++) {
                    char c1 = w1.charAt(j), c2 = w2.charAt(j);
                    if(c1 != c2) {
                        adj[c1 - 'a'][c2 - 'a'] = true;
                        break;
                    }
                }
            }
        }
    }
}
```

BFS，参考[这里](https://leetcode.com/problems/alien-dictionary/discuss/70119/Java-AC-solution-using-BFS)

```java
class Solution {
    public String alienOrder(String[] words) {
        Map<Character, Set<Character>> map=new HashMap<Character, Set<Character>>();
        Map<Character, Integer> degree=new HashMap<Character, Integer>();
        String result="";
        if(words==null || words.length==0) return result;
        for(String s: words){
            for(char c: s.toCharArray()){
                degree.put(c,0);
            }
        }
        for(int i=0; i<words.length-1; i++){
            String cur=words[i];
            String next=words[i+1];
            int length=Math.min(cur.length(), next.length());
            for(int j=0; j<length; j++){
                char c1=cur.charAt(j);
                char c2=next.charAt(j);
                if(c1!=c2){
                    Set<Character> set=new HashSet<Character>();
                    if(map.containsKey(c1)) set=map.get(c1);
                    if(!set.contains(c2)){
                        set.add(c2);
                        map.put(c1, set);
                        degree.put(c2, degree.get(c2)+1);
                    }
                    break;
                }
            }
        }
        Queue<Character> q=new LinkedList<Character>();
        for(char c: degree.keySet()){
            if(degree.get(c)==0) q.add(c);
        }
        while(!q.isEmpty()){
            char c=q.remove();
            result+=c;
            if(map.containsKey(c)){
                for(char c2: map.get(c)){
                    degree.put(c2,degree.get(c2)-1);
                    if(degree.get(c2)==0) q.add(c2);
                }
            }
        }
        if(result.length()!=degree.size()) return "";
        return result;
    }
}
```

Topological Sorting，参考[这里](https://leetcode.com/problems/alien-dictionary/discuss/70169/My-Concise-JAVA-solution-based-on-Topological-Sorting)

```java
class Solution {
    public String alienOrder(String[] words) {
        List<Point> pairs = new LinkedList<Point>(); // Adjacency list: pair = (node, node's predecessor)
        Set<Character>chs = new HashSet<Character>();// All distinct characters

        // 1. Convert characters to a graph: Adjacency lists 
        for (int i = 0; i < words.length; i++) {
            String word = words[i];
            boolean alreadySet = false;// Only set one pair where the characters at the same position differs in two neighbor rows. e.g. "wrtk" < "wrfp"=> 't' < 'f'
            for (int j = 0; j < words[i].length(); j++) {
                if (!alreadySet && i > 0 && j < words[i-1].length() && words[i].charAt(j) != words[i-1].charAt(j)) {// Set dependency of two characters by comparing two neighbor rows. 
                    pairs.add(new Point(words[i].charAt(j), words[i-1].charAt(j)));
                    alreadySet = true;
                }
                chs.add(word.charAt(j));// Add distinct character to chs set
            }
        }

        // 2. Topological sorting: keep adding elements whose in-degree is 0
        String res = "";
        int indegree[] = new int[256];
        Arrays.fill(indegree, Integer.MIN_VALUE);

        for (Character ch : chs) indegree[ch] = 0;// Initialize in-degree of the distinct characters in the words list
        for (int i = 0; i < pairs.size(); i++)// Increase in-degree according to the dependency of pairs list
            indegree[pairs.get(i).x]++;

        Queue<Character> queue = new LinkedList<Character>();
        for (int i = 0; i < 256; i++) {
            if (indegree[i] == 0) {// Add the character whose in-degree = 0, which means it doesn't have any predecessor 
                res += (char) i;
                queue.offer((char) i);
            }
        }

        while (!queue.isEmpty()) {
            Character predecessor = queue.poll(); // Dequeue the character whose in-degree = 0 from queue

            for (int i = 0; i < pairs.size(); i++) {
                if (pairs.get(i).y == predecessor) {// Update in-degree: decrease 1 to the successors of the character whose in-degree = 0
                    indegree[pairs.get(i).x]--;
                    if (indegree[pairs.get(i).x] == 0) {// If in-degree = 0, add the character to the queue, and append it to the result string
                        res += (char) pairs.get(i).x;
                        queue.offer((char) pairs.get(i).x);
                    }
                }
            }
        }
        return res.length() == chs.size() ? res : "";// NOTE: res.length should equal the size of distinct characters, otherwise a cycle must exist 
    }
}
```



```java
class Solution {
    public String alienOrder(String[] words) {
        if (words == null || words.length == 0) {
            return "";
        }
        StringBuilder sb = new StringBuilder();
        HashMap<Character, Set<Character>> suc = new HashMap<Character, Set<Character>>();
        HashMap<Character, Integer> pre = new HashMap<Character, Integer> ();
        HashSet<Character> charSet = new HashSet<Character>();
        String previousWord = new String("");
        for (String currentWord : words) {
            for (Character c : currentWord.toCharArray()) {
                charSet.add(c);
            }
            for (int i = 0; i < Math.min(previousWord.length(), currentWord.length()); ++i) {
                if (previousWord.charAt(i) != currentWord.charAt(i)) {
                    if (!pre.containsKey(currentWord.charAt(i))) { //----section_pre----
                        pre.put(currentWord.charAt(i), 1);
                    } else {
                        //ERROR1: 过滤掉重复的关系 ["za","zb","ca","cb"], 从输入得出的中间结论，"a->b"会重复出现两次，需要滤重。为了实现滤重，重新调整了section_pre和section_suc部分的顺序。
                        if (suc.containsKey(previousWord.charAt(i)) &&
                                suc.get(previousWord.charAt(i)).contains(currentWord.charAt(i))) {
                            continue;
                        }
                        pre.put(currentWord.charAt(i), pre.get(currentWord.charAt(i)) + 1);
                    }
                    if (!suc.containsKey(previousWord.charAt(i))) { //----section_suc----
                        suc.put(previousWord.charAt(i), new HashSet<Character> ());
                    }
                    suc.get(previousWord.charAt(i)).add(currentWord.charAt(i));
                    break; //the BEST part of this question
                }
            }
            previousWord = currentWord;
        }
        Queue<Character> queue = new LinkedList<Character>();
        for (Character each : charSet) {
            if (!pre.containsKey(each)) {
                queue.add(each);
            }
        }


        while (!queue.isEmpty()) {
            Character currentChar = queue.poll();
            sb.append(currentChar);
            //ERROR2 should check containsKey
            if (!suc.containsKey(currentChar)) {
                continue;
            }
            for (Character each : suc.get(currentChar)) {
                pre.put(each, pre.get(each) - 1);
                if (pre.get(each) == 0) {
                    queue.offer(each);
                }
            }
        }
        return sb.length() == charSet.size() ? sb.toString() : "";
    }
}
```

