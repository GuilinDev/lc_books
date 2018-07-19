---
description: 面试中应该知道的基础知识
---

# 基础知识

## 操作系统

### 进程Process与线程Thread

![&#x6CE8;&#x610F;&#x5185;&#x5B58;&#x662F;&#x903B;&#x8F91;&#x5185;&#x5B58;&#xFF0C;&#x6BCF;&#x4E2A;&#x8FDB;&#x7A0B;&#x6709;&#x4E00;&#x4E2A;&#x72EC;&#x7ACB;&#x7684;&#x903B;&#x8F91;&#x5185;&#x5B58;&#xFF0C;&#x5927;&#x5C0F;&#x8DDF;&#x7269;&#x7406;&#x5185;&#x5B58;&#x65E0;&#x5173;&#xFF0C;&#x8DDF;&#x8BA1;&#x7B97;&#x673A;&#x7684;32&#x4F4D;&#x6216;64&#x4F4D;&#x6709;&#x5173;&#xFF1B; &#x5728;&#x4E00;&#x4E2A;&#x8FDB;&#x7A0B;&#x4E2D;&#xFF0C;&#x6240;&#x6709;&#x7EBF;&#x7A0B;&#x662F;&#x5171;&#x4EAB;&#x540C;&#x4E00;&#x4E2A;&#x5730;&#x5740;&#x7A7A;&#x95F4;&#x7684;&#x3002;](.gitbook/assets/image%20%2818%29.png)

![PC - Programming Counter&#xFF0C;&#x7A0B;&#x5E8F;&#x8BA1;&#x6570;&#x5668;&#xFF0C;&#x7528;&#x4E8E;&#x6307;&#x793A;&#x7A0B;&#x5E8F;&#x5E8F;&#x5217;&#x4F4D;&#x7F6E;&#x7684;&#x5BC4;&#x5B58;&#x5668;&#xFF1B;TLS - Thread Local Storage&#x7EBF;&#x7A0B;&#x672C;&#x5730;&#x5B58;&#x50A8;&#xFF0C;&#x4E3A;&#x8FDB;&#x7A0B;&#x4E2D;&#x7684;&#x591A;&#x4E2A;&#x7EBF;&#x7A0B;&#x5B58;&#x50A8;&#x5168;&#x5C40;&#x6216;&#x9759;&#x6001;&#x53D8;&#x91CF;&#x7684;&#x5185;&#x5B58;&#x3002;](.gitbook/assets/image%20%285%29.png)

### 存储Storage和寻址Address 

![&#x6CE8;&#x610F;&#x5BC4;&#x5B58;&#x5668;&#x65AD;&#x7535;&#x5C31;&#x4F1A;&#x4E22;&#x5931;&#x5B58;&#x50A8;](.gitbook/assets/image%20%284%29.png)

![&#x6240;&#x4EE5;32&#x4F4D;&#x673A;&#x6BCF;&#x4E2A;&#x8FDB;&#x7A0B;&#x6700;&#x591A;&#x7528;&#x5230;4G&#x7684;&#x903B;&#x8F91;&#x5185;&#x5B58;](.gitbook/assets/image%20%2833%29.png)

![&#x5206;&#x9875; - &#x5728;&#x7269;&#x7406;&#x5185;&#x5B58;&#x4E2D;&#x627E;&#x5230;&#x7A7A;&#x95F2;&#x7684;&#x5757;&#x4EA4;&#x6362;&#x5230;&#x865A;&#x62DF;&#x5185;&#x5B58;&#x7684;&#x8FC7;&#x7A0B;](.gitbook/assets/image%20%282%29.png)

### 进程间通信

常见的进程通信：

* 文件，一个进程写文件，另外的进程来读；
* signal，比如Linux中`kill -2 pid`表示中断，`kill -9 pid`表示强行退出等；
* 消息队列；
* 管道PIPO（单向）/命名管道FIFO（单向或者双向），比如`cat test.log | wc grep -e "ERROR" --color | wc`，中间的`|`就是管道，负责不同进程间的通信，共同生成一个结果；
* 共享内存，包括开辟出一个共享文件，通过文件地址去访问这个文件；
* 一些同步机制，比如信号量Semaphore；
* Socket，这是最常用最重要的一种，在机器上开一个端口做一个服务器，让users来连接，比如浏览器进程联系远端服务器的进程，通过TCP或者UDP协议；上面几种只能一台机器上进程通信，socket则不限；

### 操作系统常见问题

* 进程和线程区别；
* 简述寻址的过程；
* 32位和64位的操作系统的区别；
* 说说进程间通信的机制；
* 简述中断的概念和流程；

### 线程池

### Socket编程

### 异步IO

## 网络

![](.gitbook/assets/image%20%288%29.png)

![&#x4E03;&#x5C42;&#x534F;&#x8BAE;&#xFF0C;&#x56FE;&#x91CC;&#x53EA;&#x5217;&#x4E86;&#x4E94;&#x5C42;](.gitbook/assets/image%20%2811%29.png)



有篇很喜欢的文章来讲网络协议，包括HTTPS，中文[https://www.oschina.net/translate/https-explained-with-carrier-pigeons](https://www.oschina.net/translate/https-explained-with-carrier-pigeons) 英文原文 [https://medium.freecodecamp.org/https-explained-with-carrier-pigeons-7029d2193351](https://medium.freecodecamp.org/https-explained-with-carrier-pigeons-7029d2193351)

## 公开课

只刷题不上课肯定是不行的，根据大神[李小白](https://zhuanlan.zhihu.com/p/24774857)的推荐，更新了一下链接，选择打牢基础的五门课程

## Udacity: Introduction to Computer Science（计算机科学导论）

链接在[这里](https://cn.udacity.com/course/intro-to-computer-science--cs101)（[https://cn.udacity.com/course/intro-to-computer-science--cs101](https://cn.udacity.com/course/intro-to-computer-science--cs101)）

> Udacity的CS入门级别课，讲解的非常简单。这门课面向的对象是高中毕业生，所以不要畏难。这是Udacity上比较早期的课程。如果是CS科班的同学，其实可以跳过这么课，因为实在太简单了。但是对于编程技术比较薄弱（或者零基础）的同学来说，这绝对是非常适合入门的课。课程的语言为python，属于码农必会语言。现在硅谷的面试中，也有很多同学直接使用python进行面试。
>
> 课程虽然简单，但是这并不影响课程的价值。主要是快速入门，同时培养对计算机的兴趣。北美学校基本都会使用python作为入门语言，这样可以快速让学生上手，同时对CS产生强烈的兴趣。国内学校一般都采用C语言作为计算机入门语言，上完之后很多同学再也不想碰编程了。

## CS 61A: The Structure and Interpretation of Computer Programs

链接在[这里](https://cs61a.org/)（[https://cs61a.org/](https://cs61a.org/)）

> Berkeley的经典CS入门课。虽然是入门，确是非常有深度的一门课。学完之后，可以让你的python水平大涨，让你学会很多python的奇巧淫技。同时也会让你对编程语言有比较深层次的理解。虽然是python授课，但是里面的topic同样适合其他语言。上完这么课，我觉得我第一次懂得了什么是recursion，第一次懂了什么是function call。。。
>
> 我只听过John DeNero的版本，因为他准备的资料非常丰富。大神John DeNero，不但研究搞得好，而且工业界\(Google Scientist\)的经验也非常丰富。更难能可贵的是，他把所有课程的资料都放到了网上，并且每年都在更新。在这个物欲横流的社会，真的是一股清流。
>
> 这门课用的教材是SICP的改版。Berkeley的老师把SICP的书改写成了Python的版本，使得这本经典的CS教材更符合现代码农的需要。SICP这本书，是计算机界绝对经典的著作之一（top10当之无愧），恰巧也是最薄的。跟人聊天的时候，只要提到SICP这个词，绝对让你逼格提高八度（码农界的一种装逼大法就是甩各种你没有听过的名词）。很久很久以前，我曾经尝试着读过，不是很读的下去。。。但是后来跟着这门课同步学习了这本书，受益匪浅！

## CS61B: Data Structures

最新的Sping 2018链接在[这里](https://sp18.datastructur.es/)（[https://sp18.datastructur.es/](https://sp18.datastructur.es/)）

> 如果说非要选一门对找工作最重要的课，那么就是这一门了。如果时间不允许，只能上一门课的话，那么就上这门课了。上完这门课，你就可以刷题了。这门课选用的语言是Java，相对于C++而言，比较容易上手。
>
>  Josh经常会手把手教你好多东西。课程有两个大的project，一个是实现一个简单的Editor，一个是BearMaps\(实现一个简单的Google Maps\)。对新手来说，确实有一点难度，但是却可以作为简历上比较有分量的Project。这也是出了CMU之后，我第一次见到过这么接地气的Project.

## Coursera: Princeton Algorithm

链接在[这里](https://zh.coursera.org/learn/algorithms-part1)（[https://zh.coursera.org/learn/algorithms-part1](https://zh.coursera.org/learn/algorithms-part1)）

> 采用java语言，Princeton神级教授倾力讲授。**我比较推荐的是这门课的教材：**[**红书**](https://link.zhihu.com/?target=https%3A//www.amazon.cn/%25E7%25AE%2597%25E6%25B3%2595-%25E5%25A1%259E%25E5%25A5%2587%25E5%25A8%2581%25E5%2585%258B-%25E9%259F%25A6%25E6%2581%25A9/dp/B01DN6FIRM/ref%3Dsr_1_1%3Fie%3DUTF8%26qid%3D1483864496%26sr%3D8-1%26keywords%3Dalgorithms)**。**我买了一本，带去了美国。Josh Hug 的cs61b也参考了这本课本，因为Josh Hug是Princeton出去的大神。  
>
>
> **首先，入门选手千万不要读算法导论！入门选手千万不要读算法导论！入门选手千万不要读算法导论！有多少人因为算法导论，从此对算法望而生畏。**书当然是超级好的书，但是这本书更适合研究算法的人！而且如果没有人给你画重点，你几乎可以深陷其中，不能自拔。
>
> 与大部分的算法课不同是，这么课并没有过多注重于数学，而是一直在教你如何正确的implement一个算法，这也是我喜欢这门课的原因。其实这门课实现的算法都属于比较复杂的算法，对大部分人来说，如果能够轻松处理这些算法，那么其实工作和刷题中很少会遇到比这些更复杂的算法。老师的视频里面有各种动画，可以很好的帮你理解各种算法。这么课分[part I ](https://link.zhihu.com/?target=https%3A//zh.coursera.org/learn/algorithms-part1)和part II。Part I里面的所有内容都是非常重要的。Par II我只看了Graph和String。
>
> 红书最强之处在于，它的编程风格非常简洁明了，对我的**代码风格**影响很大。书中对于每个算法的实现（merge sort, quick sort, graph algorithm）都是我见过的最好的。我曾经在面试的时候被人要求手写PriorityQueue，然后我写出了红书中的那种简洁的代码。我之后的代码几乎都在模仿这本书。其实代码的风格很重要，对于一个新手来说，越早的养成自己的风格，就意味着越早能写出bug free的代码。

## CMU 15213 CSAPP

链接在[这里](https://courses.cs.washington.edu/courses/cse351/18sp/videos.html)（[https://courses.cs.washington.edu/courses/cse351/18sp/videos.html](https://courses.cs.washington.edu/courses/cse351/18sp/videos.html)）

> 我认为这是CMU Top 1 的神课。网上比较全的版本是UW的一个公开课的版本，UW的版本比CMU的版本简单了一点，但是也够用了。这门课原本是在Cousera上提供的，可惜Coursera改版之后被删了。现在可以在UW的[官网](https://link.zhihu.com/?target=https%3A//courses.cs.washington.edu/courses/cse351/16sp/videos.html)上看到。这么课神就神在，它基本上把CS界的那些经典领域都不深不浅的introduce了一遍，所以这么课是cmu很多高阶课的prerequisite。而且这么课的作业非常的神，每个作业都设计的非常有趣，篇幅有限，不摊开讲了。这么课的教材\(CSAPP\)也是CS界的经典著作之一，有空一定要读读。这门课选用的语言是C语言。
>
> 当时UW讲这门课的Prof Gaetano Borriello 已经在2015年去世了，可是他的课程依然影响着千千万万的人。



