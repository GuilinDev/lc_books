# 递归

## 779 K-th Symbol in Grammar

### 题目

On the first row, we write a `0`. Now in every subsequent row, we look at the previous row and replace each occurrence of `0` with `01`, and each occurrence of `1` with `10`.

Given row `N` and index `K`, return the `K`-th indexed symbol in row `N`. \(The values of `K` are 1-indexed.\) \(1 indexed\).

```text
Examples:
Input: N = 1, K = 1
Output: 0

Input: N = 2, K = 1
Output: 0

Input: N = 2, K = 2
Output: 1

Input: N = 4, K = 5
Output: 1

Explanation:
row 1: 0
row 2: 01
row 3: 0110
row 4: 01101001
```

**Note:**

1. `N` will be an integer in the range `[1, 30]`.
2. `K` will be an integer in the range `[1, 2^(N-1)]`.

### 分析

在第一行我们写上一个 0。接下来的每一行，将前一行中的0替换为01，1替换为10。给定行数 N 和序数 K，要求返回第 N 行中第 K个字符。（K从1开始）。

可以分解成子问题，选择递归，观察递归书，如下规律

```text
/*        0
      /       \
     0          1
   /   \      /    \
   0     1    1      0
 / \     / \   / \   / \
 0  1   1   0  1  0  0  1
*/
```

首先，可以发现每行的前缀完全相同。 不难理解。 因为每2 \* L个首字母由相同的L个首字母生成。知道了每一行都有相同长序列的开始部分。 此外，题目保证K为\[1，2 ^\(N-1\)\]范围内的整数。，因此结果仅取决于值K。

假设2 ^ L &lt; K &lt;= 2 ^\(L + 1\)。 2 ^ L是小于K的最大2的幂。 从K-2 ^ L生成Kth数 Kth数也不同于K-2 ^ L 因此我们通过减去2 ^ L将K切换为K-2 ^ L

重复此过程，直到将K变为1， 这意味着总共需要减去K-1次。可以以二进制形式传输K - 1， 这样就可以轻松找到需要转换的次数。

### 代码

```java
class Solution {
    public int kthGrammar(int N, int K) {
        if(N == 1) {
            return 0;
        }
        if(K % 2 == 0){
            if (kthGrammar(N-1, K / 2) == 0) { // 递归
                return 1;
            }else {
                return 0;
            }
        }else{
            if(kthGrammar(N-1,(K+1) / 2) == 0) { // 递归
                return 0;
            } else {
                return 1;
            }
        }
    }
}
```

更加简短的写法

```java
class Solution {
    public int kthGrammar(int N, int K) {
        K--;
        int count = 0;
        // Kth number in N row, 由K/2th number in N-1 row决定. 如果 K % 2 == 0,那就与前面相同, 否则相异。 所以Kth会转换x次，x则等于K中bit 1的数量.
        while (K != 0) {
            K = K & (K - 1); // 消除最后一个1
            count++;
        }
        return count % 2;
    }
}
```

```java
class Solution {
    public int kthGrammar(int N, int K) {
        return Integer.bitCount(K - 1) & 1;
    }
}
```

