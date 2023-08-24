---
author: zouzq7@163.com
created: "2019-02-21 08:27:19 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: 高并发 Nginx + lua是如何抗住的
updated: "2019-02-21 11:26:52 +0000"
---

<div>

[提到高并发或者抗压力，有这种高qps经验的同学第一反应大都是Nginx + lua +
Redis，网上也满天非那种高并发架构方案大都是这种，但是Nginx + lua
来做接入层到底是怎么抗住压力的呢？] 

 

<div>

[本篇顺序：] 

 

<div>

[1、Nginx
如何抗住的高并发，工作模式是怎样的，利用了哪些技术] 

 

<div>

[2、常见的IO模型及
异步非阻塞IO的优势] 

 

<div>

[3、epoll相对于其他模型为何这么强大] 

 

<div>

[第一阶段：] 

 

<div>

[Nginx 不同于 Apache 的一点就是，Nginx 采用单线程，非阻塞，异步 IO
的工作模型，并不会每一个新进程都会起一个新的进程或者线程来处请求，Nginx利用的是epoll模型。然后来看一下Nginx的工作模型：] 

 

:::  
[nginx
工作模型有两种实现方式：] 
:::

:::  
[[单工作进程是指，一个工作进程中有多个工作线程，只有一个工作进程 +
master进程] 
:::

:::  
[多工作进程是指，一个工作进程中只有一个工作线程，然后有多个工作进程 +
master进程] 
:::

:::  
[就是下面这种机遇epoll 模型构建的[单线程，非阻塞，异步 IO的
单线程处理多链接工作模型来抗住的压力，下面来仔细的看看Nginx的内部实现。] 
:::

:::  
<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/FAE08BDA-0E4B-4B5C-9773-2AC1B41CEC99.png) 
 \

 
:::

:::  
master
主要负责，接受管理员信号向worker发送指令，负责worker进程的生命周期，通知的类型有：worker
不再接受新请求、worker 退出&销毁等，可以把master 看作一个worker
的管理器，我们和master交互来间接管理worker。nginx一些无损重启或者reload配置文件等就是这么来实现的。
:::

:::  
worker 顾名思义是处理具体网络事件的，从初始化nginx开始讲：
:::

:::  
首先被创建的是master，在创建时先建立好需要listen的socket（listenfd），然后从master从fork出多个worker
，这样相当于master
listenfd就被继承过来了，所以说所有的worker会在新连接到来时变得可读，为保证只有一个进程处理链接，所有的worder进程在注册listenfd读事件发生时会先去抢占accept_mutex，抢到互斥锁的那个worker进程才会注册listenfd读事件，在读事件里面调用accept接受连接，然后就开始读取请求、解析请求、处理、产生响应、断开连接。处理过程中如果碰到了IO操作，就开始使用基于epoll的[非阻塞，异步
IO] 工作模式，发生IO时work会先把这个socket夯在哪里
去处理别的请求，等IO完成后再处理剩下的逻辑。nginx
就这么抗住了大量的连接并且充分利用cpu进行处理的。
:::

:::  
然后从网上盗两张图来看一下nginx 创建监听到accept的流程：
:::

:::  
![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/5E2D6918-FF8A-4AC8-B4FD-00E22AABDC6F.png) 
 \
:::

:::  
然后监听字ngx_event_accept 的处理流程：
:::

:::  
![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/D110DEBC-E41B-4FC9-AEA0-67259C57B55C.png) 
 \
:::

:::  
\
:::

:::  
第二阶段：
:::

:::  
所提到的异步 非阻塞IO 及常见几种IO的差别：
:::

:::  
两阶段式IO：
:::

<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/8482AB5B-E51F-4EAC-A69C-FFC44988ABD0.jpg) 


 

<div>

\

 

<div>

【阻塞 blocking IO】：

 

<div>

recvfrom -\> \[syscall -\> wait -\> copy -\>\] return OK!

 

<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/89C2F920-10CD-4F64-B1E4-645E1862208A.jpg) 


 

<div>

\

 

<div>

【非阻塞 nonblocking IO】：

 

<div>

recvfrom -\>\[syscall -\> wait -\> \] return no data ready

 

<div>

recvfrom -\>\[syscall -\> wait -\> \] return no data ready

 

<div>

recvfrom -\>\[syscall -\> wait -\> \] return data ready

 

<div>

recvfrom -\>\[syscall -\> copy -\> \] return OK!

 

<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/C97D2D04-53A9-4FBB-BEFD-D94F2EB66EC2.jpg) 


 

<div>

【多路复用IO multiplexingIO】

 

<div>

其中每个IO都是非阻塞IO，先使用poll/select
轮训IO句柄，如果有准备好的，开始第二阶段IO（阻塞读数据）

 

<div>

select/poll -\> \[syscall -\> wait -\> \] return readable

 

<div>

recvfrom -\> \[syscall -\> copy -\> \] return OK! 

 

<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/D34DBB0C-6327-4166-A95F-8C1FFC3087C5.jpg) 


 

<div>

\

 

<div>

【信号驱动IO signal driven IO】

 

<div>

首先构建一个信号处理器，然后第二阶段阻塞读数据

 

<div>

signal handle -\> \[syscal -\> wait -\> \] return

 

<div>

\[syscall -\> \] signal handle-\> recvfrom -\> \[syscall -\> copy -\> \]
return OK!

 

<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/169AFDEB-81AE-4D0D-BEF2-E40F10E9F8C8.jpg) 


 

<div>

\

 

<div>

【异步IO asynchronous IO】

 

<div>

两个阶段都是非阻塞的

 

<div>

aio_read -\> \[syscall -\> wait -\> \] return

 

<div>

\[syscall -\> copy -\> \] aio_readCallback

 

<div>

\

 

<div>

【对比】

 

<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/F7A25319-F585-481D-8C12-598FAA1C6415.jpg) 


 

<div>

![](%E9%AB%98%E5%B9%B6%E5%8F%91%20Nginx%20+%20lua%E6%98%AF%E5%A6%82%E4%BD%95%E6%8A%97%E4%BD%8F%E7%9A%84.resources/7B76E37C-9C9A-45A5-B39C-5498187D2C85.jpg) 


 

<div>

\

 

:::  
第三阶段：
:::

:::  
select /poll/ epoll 对比\
:::

:::  
[单个进程能够监视的文件描述符的数量存在最大限制，通常是1024，] 
:::

<div>

select不足的地方：

 

<div>

1 每次select都要把全部IO句柄复制到内核

 

<div>

2 内核每次都要遍历全部IO句柄，以判断是否数据准备好

 

<div>

3 select模式最大IO句柄数是1024，太多了性能下降明显

 

<div>

[poll：] 

 

<div>

[poll使用链表] 

 

<div>

\

 

<div>

[拿select模型为例，假设我们的服务器需要支持100万的并发连接，则在_FD_SETSIZE为1024的情况下，则我们至少需要开辟1k个进程才能实现100万的并发连接。除了进程间上下文切换的时间消耗外，从内核/用户空间大量的无脑内存拷贝、数组轮询等，是系统难以承受的。因此，基于select模型的服务器程序，要达到10万级别的并发访问，是一个很难完成的任务。] 

 

<div>

\

 

<div>

epoll的特点

 

<div>

1 每次新建IO句柄(epoll_create)才复制并注册(epoll_register)到内核

 

<div>

2 内核根据IO事件，把准备好的IO句柄放到就绪队列

 

<div>

3 应用只要轮询(epoll_wait)就绪队列，然后去读取数据

 

<div>

只需要轮询就绪队列（数量少），不存在select的轮询，也没有内核的轮询，不需要多次复制所有的IO句柄。因此，可以同时支持的IO句柄数轻松过百万。

 

<div>

详细的可以看一下这篇文章：

 

:::  
<https://zhuanlan.zhihu.com/p/39970630>\
:::
