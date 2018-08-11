# 操作系统

### 进程Process与线程Thread

![&#x6CE8;&#x610F;&#x5185;&#x5B58;&#x662F;&#x903B;&#x8F91;&#x5185;&#x5B58;&#xFF0C;&#x6BCF;&#x4E2A;&#x8FDB;&#x7A0B;&#x6709;&#x4E00;&#x4E2A;&#x72EC;&#x7ACB;&#x7684;&#x903B;&#x8F91;&#x5185;&#x5B58;&#xFF0C;&#x5927;&#x5C0F;&#x8DDF;&#x7269;&#x7406;&#x5185;&#x5B58;&#x65E0;&#x5173;&#xFF0C;&#x8DDF;&#x8BA1;&#x7B97;&#x673A;&#x7684;32&#x4F4D;&#x6216;64&#x4F4D;&#x6709;&#x5173;&#xFF1B; &#x5728;&#x4E00;&#x4E2A;&#x8FDB;&#x7A0B;&#x4E2D;&#xFF0C;&#x6240;&#x6709;&#x7EBF;&#x7A0B;&#x662F;&#x5171;&#x4EAB;&#x540C;&#x4E00;&#x4E2A;&#x5730;&#x5740;&#x7A7A;&#x95F4;&#x7684;&#x3002;](../.gitbook/assets/image%20%2820%29.png)

![PC - Programming Counter&#xFF0C;&#x7A0B;&#x5E8F;&#x8BA1;&#x6570;&#x5668;&#xFF0C;&#x7528;&#x4E8E;&#x6307;&#x793A;&#x7A0B;&#x5E8F;&#x5E8F;&#x5217;&#x4F4D;&#x7F6E;&#x7684;&#x5BC4;&#x5B58;&#x5668;&#xFF1B;TLS - Thread Local Storage&#x7EBF;&#x7A0B;&#x672C;&#x5730;&#x5B58;&#x50A8;&#xFF0C;&#x4E3A;&#x8FDB;&#x7A0B;&#x4E2D;&#x7684;&#x591A;&#x4E2A;&#x7EBF;&#x7A0B;&#x5B58;&#x50A8;&#x5168;&#x5C40;&#x6216;&#x9759;&#x6001;&#x53D8;&#x91CF;&#x7684;&#x5185;&#x5B58;&#x3002;](../.gitbook/assets/image%20%286%29.png)

### 存储Storage和寻址Address 

![&#x6CE8;&#x610F;&#x5BC4;&#x5B58;&#x5668;&#x65AD;&#x7535;&#x5C31;&#x4F1A;&#x4E22;&#x5931;&#x5B58;&#x50A8;](../.gitbook/assets/image%20%285%29.png)

![&#x6240;&#x4EE5;32&#x4F4D;&#x673A;&#x6BCF;&#x4E2A;&#x8FDB;&#x7A0B;&#x6700;&#x591A;&#x7528;&#x5230;4G&#x7684;&#x903B;&#x8F91;&#x5185;&#x5B58;](../.gitbook/assets/image%20%2837%29.png)

![&#x5206;&#x9875; - &#x5728;&#x7269;&#x7406;&#x5185;&#x5B58;&#x4E2D;&#x627E;&#x5230;&#x7A7A;&#x95F2;&#x7684;&#x5757;&#x4EA4;&#x6362;&#x5230;&#x865A;&#x62DF;&#x5185;&#x5B58;&#x7684;&#x8FC7;&#x7A0B;](../.gitbook/assets/image%20%283%29.png)

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

