---
description: >-
  位操作题目只需要关注简单题目和高频题目，包括判断奇偶，交换两数，变换符号，求绝对值，筛素数与空间压缩，高低位交换，二进制逆序，二进制中1的个数，缺失的数字等。
---

# 6. Bit Manipulation

[https://blog.csdn.net/hk\_john/article/details/70231705](https://blog.csdn.net/hk_john/article/details/70231705)

基本的位操作符有与、或、异或、取反、左移、右移这6种，它们的运算规则如下所示：

| 符号 |  描述 |  运算规则                       |
| --- | --- | --- | --- | --- | --- | --- |
| &       |  与 | 两个位都为1时，结果才为1 |
| \|   |  或     | 两个位都为0时，结果才为0 |
| ^     | 异或 | 两个位相同为0，相异为1 |
| ~    | 取反 | 0变1，1变0 |
| &lt;&lt;  | 左移 | 各二进位全部左移若干位，高位丢弃，低位补0 |
| &gt;&gt;  | 右移 | 各二进位全部右移若干位，对无符号数，高位补0，有符号数，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移） |

注意以下几点：

1．  在这6种操作符，只有~取反是单目操作符，其它5种都是双目操作符。

2．  位操作只能用于整形数据，对float和double类型进行位操作会被编译器报错。

3．  对于移位操作，在微软的VC6.0和VS2008编译器都是采取算术称位即算术移位操作，算术移位是相对于逻辑移位，它们在左移操作中都一样，低位补0即可，但在右移中逻辑移位的高位补0而算术移位的高位是补符号位。如下面代码会输出-4和3。

1. int a = -15, b = 15;  
2. printf\("%d %d\n", a &gt;&gt; 2, b &gt;&gt; 2\);  

因为15=0000 1111\(二进制\)，右移二位，最高位由符号位填充将得到0000 0011即3。-15 = 1111 0001\(二进制\)，右移二位，最高位由符号位填充将得到1111 1100即-4（见注1）。

4．  位操作符的运算优先级比较低，因为尽量使用括号来确保运算顺序，否则很可能会得到莫明其妙的结果。比如要得到像1，3，5，9这些2^i+1的数字。写成int a = 1 &lt;&lt; i + 1;是不对的，程序会先执行i + 1，再执行左移操作。应该写成int a = \(1 &lt;&lt; i\) + 1;

5．  另外位操作还有一些复合操作符，如&=、\|=、 ^=、&lt;&lt;=、&gt;&gt;=。





## 136 - Single Number

### 原题概述

### 题意与分析

### 代码

```java
class Solution {
    public int singleNumber(int[] nums) {
        int result = 0;
        for (int i = 0; i < nums.length; i++) {
            result ^= nums[i];//logical exclusive OR，相同的数就消掉,
        }
        return result;
    }
}
```

## 137 - Single Number II

### 原题概述

### 题意与分析

把每一位数都想成二进制的数，用一个32位的数组来记录每一位上面的1的count值。注意数组计数是从左往右走，而二进制数数是从右往左的；所以数组第0位的count就是二进制最低位上的count的。例如：4的二进制是100（当然作为32位就是前面还一堆了000...000100这样子），3个4的话按照每位相加的话，按照二进制表示法考虑就是300，当然存在数组里面就是003（A\[0\]=0;A\[1\]=0;A\[2\]=3，然后后面到A\[31\]都得0）。

然后对所有数按照二进制表示按位加好后，就要把他还原成所求的值。这里面的想法是，如果一个数字出现了3次，那么这个数字的每一位上面，如果有1那么累加肯定是得3的，如果是0，自然还是0。所以对每一位取余数，得的余数再拼接起来就是我们要找的那个single one。

这里还原的方法是，对32位数组从0开始，对3取余数，因为数组0位置其实是二进制的最低位，所以每次要向左移。用OR（\|）和 + 都可以拼接回来。

### 代码

```java
class Solution {
    public int singleNumber(int[] nums) {
        int[] count = new int[32];
        int result = 0;
        for (int i = 0; i < 32; i++) {
            for (int j = 0; j < nums.length; j++) {
                if (((nums[j] >> i) & 1) == 1) {//nums中当前元素的32位二进制形式，从右边的位开始，每位右移i位，来判断是否为1，然后在数组count中的i位置递增
                    count[i]++;
                }
            }
            //遍历完数组nums里面的所有元素后，除以3再左移还原
            result |= ((count[i] % 3) << i);
        }
        //余数为只出现一次的那个数
        return result;
    }
}
```

## 187 - Repeated DNA Sequence

### 原题概述

All DNA is composed of a series of nucleotides abbreviated as A, C, G, and T, for example: "ACGAATTCCG". When studying DNA, it is sometimes useful to identify repeated sequences within the DNA.

Write a function to find all the 10-letter-long sequences \(substrings\) that occur more than once in a DNA molecule.

**Example:**

```text
Input: s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"

Output: ["AAAAACCCCC", "CCCCCAAAAA"]
```

### 题意和分析

给一个DNA字符串，找到它的所有长度为10的子串，并且这些子串在原本的DNA字符串中出现了两次或多次；这道题比较大众的解法是用位操作，

 0xfffff的解释：前两位0x表示十六进制，也就是每个数用二进制表示都是4位，比如0x0 = 0000，0x6 = 0110，十六进制的的数字从0 - 15，其中F代表15，写成二进制位1111，所以0xFFFFF = 1111 1111 1111 1111 1111，如果说这样一句话“程序是从内存中的页面x0xA532到0xFEE2读取的，那意思就是下面的页面被加载了 - 1010 0101 0011 0010 到 1111 1110 1110 0010。

### 代码

```java
class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        List<String> result = new ArrayList<>();
        if (s == null || s.length() < 10) {
            return result;
        }

        Set<Integer> words = new HashSet<>();
        Set<Integer> doubleWords = new HashSet<>();
        char[] map = new char[26];

        //map['A' - 'A'] = 0;Java中整型数组的default值本身就是0
        map['C' - 'A'] = 1;
        map['G' - 'A'] = 2;
        map['T' - 'A'] = 3;

        for (int i = 0; i < s.length() - 9; i++) {
            int v = 0;
            for (int j = i; j < i + 10; j++) {
                v <<= 2; //v左移两位，例如01就变成0100了
                v |= map[s.charAt(j) - 'A']; //append两位， 例如0100 || 11 => 0111
            }

            if (!words.add(v) && doubleWords.add(v)) {
                result.add(s.substring(i , i + 10));
            }
        }
        return result;
    }
}
```

这道题也可以用滑动窗口的办法也可以解决，Set来判断是否重复子串（用HashMap判断是否重复则比较麻烦，需要把子串当作key，出现的次数作为value，来判断是否出现2次或以上），Time：O\(n\), Space O\(n\)

```java
class Solution {
    public List<String> findRepeatedDnaSequences(String s) {
        Set seen = new HashSet();
        Set repeated = new HashSet();
        for (int i = 0; i + 9 < s.length(); i++) {//到倒数第十个循环后就不用
            String ten = s.substring(i, i + 10);
            if (!seen.add(ten)) {//判断条件是说，子串在Set中存在，不能往里面加了
                repeated.add(ten);
            }
        }
        return new ArrayList(repeated);
    }
}
```



