# 位操作基本知识

[https://blog.csdn.net/hk\_john/article/details/70231705](https://blog.csdn.net/hk_john/article/details/70231705)

基本的位操作符有与、或、异或、取反、左移、右移这6种，它们的运算规则如下所示：

| 符号 |  描述 |  运算规则                       |
| :--- | :--- | :--- |
| &       |  与 | 两个位都为1时，结果才为1；可以用&来判断奇数和偶数，奇数二进制表示法末尾总是为1，偶数则为0，例如315 & 1等于1，1000 & 1等于0，等同于n % 2 |
| \|   |  或     | 两个位都为0时，结果才为0 |
| ^     | 异或 | 两个位相同为0，相异为1 |
| ~    | 取反 | 0变1，1变0 |
| &lt;&lt;  | 左移 | 各二进位全部左移若干位，高位丢弃，低位补0，左移&lt;&lt;1，相当于除以2，同样&lt;&lt;2相当于除以4，等等 |
| &gt;&gt;  | 右移 | 各二进位全部右移若干位，对无符号数，高位补0，有符号数，各编译器处理方法不一样，有的补符号位（算术右移），有的补0（逻辑右移），右移&gt;&gt;1，相当于乘以2，同样&gt;&gt;2相当于乘以4，等等。 |

注意以下几点：

1．  在这6种操作符，只有~取反是单目操作符，其它5种都是双目操作符。

2．  位操作只能用于整形数据，对float和double类型进行位操作会被编译器报错。

3．  对于移位操作，在微软的VC6.0和VS2008编译器都是采取算术称位即算术移位操作，算术移位是相对于逻辑移位，它们在左移操作中都一样，低位补0即可，但在右移中逻辑移位的高位补0而算术移位的高位是补符号位。如下面代码会输出-4和3。

1. int a = -15, b = 15;  
2. printf\("%d %d\n", a &gt;&gt; 2, b &gt;&gt; 2\);  

因为15=0000 1111\(二进制\)，右移二位，最高位由符号位填充将得到0000 0011即3。-15 = 1111 0001\(二进制\)，右移二位，最高位由符号位填充将得到1111 1100即-4（见注1）。

4．  位操作符的运算优先级比较低，因为尽量使用括号来确保运算顺序，否则很可能会得到莫明其妙的结果。比如要得到像1，3，5，9这些2^i+1的数字。写成int a = 1 &lt;&lt; i + 1;是不对的，程序会先执行i + 1，再执行左移操作。应该写成int a = \(1 &lt;&lt; i\) + 1;

5．  另外位操作还有一些复合操作符，如&=、\|=、 ^=、&lt;&lt;=、&gt;&gt;=。


