# Trie前缀树

## 208 Implement Trie \(Prefix Tree\)

### 题目

Implement a trie with `insert`, `search`, and `startsWith` methods.

**Example:**

```text
Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // returns true
trie.search("app");     // returns false
trie.startsWith("app"); // returns true
trie.insert("app");   
trie.search("app");     // returns true
```

**Note:**

* You may assume that all inputs are consist of lowercase letters `a-z`.
* All inputs are guaranteed to be non-empty strings.

### 分析

因为只有小写字符，所以用一个大小为26的Trie\[\]数组来存储所有字符，并用一个标记来记录某个单词是否在前缀树中。

设计前缀树里面的三个方法比较类似，都是将传进来的字符串参数转换成字符数组（不转用charAt\(\)也可以），然后对每个字符进行操作，如果Trie中没有该字符的节点做相应处理，最后移动root索引一步，直至将传进来的字符串中的字符全部处理。

### 代码

```java
class Trie {
    
    private boolean is_string; //记录到某个位置是否是一个单词
    private Trie[] next;

    /** Initialize your data structure here. */
    public Trie() {
        is_string = false;
        next = new Trie[26];
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        Trie root = this;
        char[] w = word.toCharArray();
        for (char x : w) {
            if (root.next[x - 'a'] == null) { // 当前节点的下一个节点不存在，说明是新来的，新建一个
                root.next[x - 'a'] = new Trie();
            }
            // 移动索引到当前节点
            root = root.next[x - 'a'];
        }
        root.is_string = true;
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        Trie root = this;
        char[] w = word.toCharArray();
        for (char x : w) { // 挨个节点查找
            if (root.next[x - 'a'] == null) { // 没找到
                return false;
            }
            root = root.next[x - 'a']; // 移动索引并准备检查下一个节点
        }
        return root.is_string;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        Trie root = this;
        char[] p = prefix.toCharArray();
        for (char x : p) {
            if (root.next[x - 'a'] == null) { // 没找到
                return false;
            }
            root = root.next[x - 'a'];
        }
        return true;
    }
}

/**
 * Your Trie object will be instantiated and called as such:
 * Trie obj = new Trie();
 * obj.insert(word);
 * boolean param_2 = obj.search(word);
 * boolean param_3 = obj.startsWith(prefix);
 */
```

## 212 Word Search II

### 题目

Given a 2D board and a list of words from the dictionary, find all words in the board.

Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

**Example:**

```text
Input: 
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath","pea","eat","rain"]

Output: ["eat","oath"]
```

### 分析

这道题意思就是从二维数组中某个字符出发，可以向左向右向上向下移动，走过的路径构成一个字符串，判断是否能走出给定字符串的 word ，走过的字符不能够再走第二次。比如 eat，从第二行最后一列的 e 出发，向左移动，再向左移动，就走出了 eat。题目中给一个 word 列表，要求找出里面哪些单词可以由 board 生成。

1\) [第79 题Word Search](https://app.gitbook.com/@guilindev/s/interview/leetcode/backtracking#79-word-search)是判断某个单词能否在 board 中生成，所以暴力解法可以是遍历这道题给出的words的所有单词，每个单词都用79题的DFS的办法来看能不能被生成。

2\) Trie前缀树，[这里是LC的思路](https://leetcode.com/problems/word-search-ii/discuss/59780/Java-15ms-Easiest-Solution-%28100.00%29)。

上面的解法的核心思想是从 words 中依次`选定一个单词 -> 从图中的每个位置出发，看能否找到这个单词`。其实可以倒过来：`从二维数组中的每个位置出发 -> 看遍历过程中是否遇到了 words 中的某个单词`。

想知道遍历过程中判断是否遇到了某个单词，可以事先把所有单词存到前缀树中。这样的话，如果当前走的路径不是前缀树的前缀，就可以提前结束了，而如果是前缀树的中的单词，就将其存到结果中。

关于实现，可以在遍历过程中，将当前路径的单词传进函数，然后判断当前路径构成的单词是否是在前缀树中出现，这个想法可行，但不够好（是上面方法1把DFS换成了Trie），因为每次都从前缀树中判断当前路径的单词，会带来重复的判断。比如先判断了 pan 存在于前缀树中，接下来假如路径变成 panda ，判断它在不在前缀树中，又需要判断一遍 pan 。

因此，可以把前缀树用递归中来传递前缀树的节点，判断当前节点的孩子是否为 null，如果是 null 说明当前的前缀不存在，可以提前结束。如果不是 null，再判断当前节点是否是单词的结尾，如果是结尾直接将当前单词加入。由于递归过程中没有加路径，所以我们改造一下前缀树的节点，将单词直接存入节点，这样的话就可以直接取到了。

### 代码

79题代码套过来的暴力解法，可以accept

```java
class Solution {
    public List<String> findWords(char[][] board, String[] words) {
        List<String> results = new ArrayList<>();
        for (String word : words) {
            if (exist(board, word)) {
                results.add(word);
            }
        }
        return results;
    }
    
    // 下面的代码是把79题的代码抄过来，判断每个单词是否能够被生成
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0) {
            return false;
        }
        int rows = board.length;
        int cols = board[0].length;
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(board, i, j, word, 0)) {
                    return true;
                }
            }
        }
        return false;
    }
    
    private boolean dfs(char[][] board, int i, int j, String word, int index) {
        if (index == word.length()) {
            return true;
        }
        int m = board.length;
        int n = board[0].length;
        if (i < 0 || i >= m || j < 0 || j >= n || board[i][j] != word.charAt(index)) {
            return false;
        }
        char temp = board[i][j];
        board[i][j] = '#';
        boolean result = dfs(board, i + 1, j, word, index + 1) ||
            dfs(board, i - 1, j, word, index + 1) ||
            dfs(board, i, j + 1, word, index + 1) ||
            dfs(board, i, j - 1, word, index + 1);
        board[i][j] = temp; // 这步不能忘记，检查完当前起点为止的字符串后要恢复访问记录        
        return result;
    }
}
```

Trie解法，具体步骤：

* 首先构造前缀树，使用模板，要熟悉，这一步骤是208 - Implement Trie \(Prefix Tree\)的题解
* 构造前缀树，将其作为全局变量
* 遍历矩阵的每一个点作为起点，做回溯backtrack
  * 回溯方法的参数是row\(行坐标\),col\(纵坐标\),stringbuilder\(存储backtrack之前的字符串\),is\_visit\(记忆哪些点已经访问过\)
  * 回溯的终止条件 - 如果到达矩阵的边界或者当前节点遍历过\(is\_visit\)就直接return
  * stringbuilder加入当前点的字符，并将is\_visit置为true
  * 如果前缀树包含此字符串，且此字符串在前缀树是最终字符串，就加入结果集\(同时去重复\)
  * 如果前缀树搜索含有这个前缀，就继续上下左右搜索
  * 最后删除stringbuilder的最后一个字符，并将is\_visit置为false。

```java
class Solution {
    // ------------------
    class Trie {
        private boolean is_word;
        private Trie[] next;


        /** Initialize your data structure here. */
        public Trie() {
            next = new Trie[26];
            is_word = false;
        }

        /** Inserts a word into the trie. */
        public void insert(String word) {
            Trie root = this;
            for (int i = 0; i < word.length(); i++) {
                char ch = word.charAt(i);
                if (root.next[ch - 'a'] == null) {
                    root.next[ch - 'a'] = new Trie();
                }
                root = root.next[ch - 'a'];
            }
            root.is_word = true;
        }

        /** Returns if the word is in the trie. */
        public boolean search(String word) {

            if (find(word) == null) {
                return false;
            }

            return find(word).is_word;
        }

        /** Returns if there is any word in the trie that starts with the given prefix. */
        public boolean startsWith(String prefix) {

            return find(prefix) == null ? false : true;
        }

        private Trie find(String str) {
            Trie root = this;
            for (int i = 0; i < str.length(); i++) {
                char ch = str.charAt(i);
                if (root.next[ch - 'a'] == null) {
                    return null;
                }
                root = root.next[ch - 'a'];
            }
            return root;
        }
    }

    /**
     * Your Trie object will be instantiated and called as such:
     * Trie obj = new Trie();
     * obj.insert(word);
     * boolean param_2 = obj.search(word);
     * boolean param_3 = obj.startsWith(prefix);
     */ 
    //------------------- 以上是构建Trie的代码
    //O(m * n * h) - h为Trie的高度
    Trie root = new Trie();
    List<String> results;
    public List<String> findWords(char[][] board, String[] words) {
        results= new ArrayList<>();
       if (board == null || board.length == 0 || board[0].length == 0 || words == null) {
           return results;
       }
        
        int rows = board.length, cols = board[0].length;
        // 1. 构建Trie并加入单词
        for (String word : words) {
            root.insert(word);
        }
        
        // 2. 对board中的每一个字符进行dfs，查找以该字符为起始是否是一个单词        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                StringBuilder sb = new StringBuilder();
                // 这里额外空间可以传入一个记录visited的board，下面不用改成'#'也行
                dfs(i, j, board, sb);
            }
        }
        return results;
    }
    
    private void dfs(int i, int j, char[][] board, StringBuilder sb) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] == '#') {
            return;
        }
        sb.append(board[i][j]);
        char temp = board[i][j];
        board[i][j] = '#';
        
        if (root.search(sb.toString())) {
            if (!results.contains(sb.toString())) {
                results.add(sb.toString());
            }            
        }
        
        if (root.startsWith(sb.toString())) {
            // board中四个方向的dfs
            dfs(i - 1, j, board, sb);
            dfs(i + 1, j, board, sb);
            dfs(i, j - 1, board, sb);
            dfs(i, j + 1, board, sb);
        }
        sb.deleteCharAt(sb.length() - 1); //Trie中的路径
        board[i][j] = temp;
    }
}
```

## 211 Add and Search Word - Data Structure Design

### 题目

Design a data structure that supports the following two operations:

```text
void addWord(word)
bool search(word)
```

search\(word\) can search a literal word or a regular expression string containing only letters `a-z` or `.`. A `.` means it can represent any one letter.

**Example:**

```text
addWord("bad")
addWord("dad")
addWord("mad")
search("pad") -> false
search("bad") -> true
search(".ad") -> true
search("b..") -> true
```

**Note:**  
You may assume that all words are consist of lowercase letters `a-z`.

### 分析

 设计一个数据结构，实现 `add` 方法添加字符串，`search` 查找字符串，所查找的字符串可能含有 `.` ，代表任意的一个字符。

暴力方法，可以用 HashSet 存入所有的字符串。当查找字符串的时候，我们首先判断 set 中是否存在，如果存在的话直接返回 true 。不存在的话，因为待查找的字符串中可能含有 . ，接下来我们需要遍历 set ，一个一个的进行匹配。

优化的办法是用Trie，通过208题实现的前缀树进行存储，这样查找字符串就不用依赖于字符串的数量了， 对于字符串中的 `.` ，通过递归去查找。

### 代码

```java
class WordDictionary {
    
    /**
    Trie的实现，不用实现insert(),search()和startWith()
    */
    class TrieNode {
        TrieNode[] children;
        boolean flag;

        public TrieNode() {
            children = new TrieNode[26];
            flag = false;
            for (int i = 0; i < 26; i++) {
                children[i] = null;
            }
        }

    }
    
    TrieNode root;


    /** Initialize your data structure here. */
    public WordDictionary() {
        root = new TrieNode();
    }
    
    /** Adds a word into the data structure. */
    public void addWord(String word) { // 将新来的单词存入Trie中
        char[] array = word.toCharArray();
        TrieNode cur = root;
        for (int i = 0; i < array.length; i++) {
            // 当前孩子是否存在
            if (cur.children[array[i] - 'a'] == null) {
                cur.children[array[i] - 'a'] = new TrieNode();
            }
            cur = cur.children[array[i] - 'a'];
        }
        // 当前节点代表结束
        cur.flag = true;
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    public boolean search(String word) {
        return recursion(word, root);
    }
    
    private boolean recursion(String word, TrieNode root) {
        char[] array = word.toCharArray();
        TrieNode cur = root;
        for (int i = 0; i < array.length; i++) {
            // 对于 . , 递归的判断所有不为空的孩子
            if(array[i] == '.'){
                for(int j = 0; j < 26; j++){
                    if(cur.children[j] != null){
                        if(recursion(word.substring(i + 1),cur.children[j])){
                            return true;
                        }
                    }
                }
                return false;
            }
            // 不含有当前节点
            if (cur.children[array[i] - 'a'] == null) {
                return false;
            }
            cur = cur.children[array[i] - 'a'];
        }
        // 当前节点是否为是某个单词的结束
        return cur.flag;
    }
}

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary obj = new WordDictionary();
 * obj.addWord(word);
 * boolean param_2 = obj.search(word);
 */
```

使用正则表达式，Python解法可以AC， 用 `#` 分割不同单词，以及查找的时候查找 `# + word + #`

```python
import re
class WordDictionary:

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.words = '#'
        

    def addWord(self, word: str) -> None:
        """
        Adds a word into the data structure.
        """
        self.words += word + '#'

    def search(self, word: str) -> bool:
        """
        Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter.
        """
        return bool(re.search('#' + word + '#', self.words))


# Your WordDictionary object will be instantiated and called as such:
# obj = WordDictionary()
# obj.addWord(word)
# param_2 = obj.search(word)
```

