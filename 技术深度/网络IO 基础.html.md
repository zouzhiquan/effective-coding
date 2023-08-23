---
author: zouzq7@163.com
created: "2018-09-27 03:39:58 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: 网络IO 基础
updated: "2023-04-10 17:49:00 +0000"
---

<div>

相对于传统软件行业，互联网行业存在的最大技术挑战之一应该就是高并发了。最初我对高并发的理解，就是服务器存在压力，然后堆机器，很low，但是没准很有效，当然也存在一些问题。

</div>

<div>

机器资源固然重要，但是更需要使用专业的工具和不断完善的策略来解决高并发问题。

</div>

<div>

\

</div>

<div>

工具方面：

</div>

<div>

在看到高并发之后，第一反应是前面挡一层nginx，扛扛压力，做做负载均衡，该放缓存的放缓存，redis现在蛮常用的，看看web服务器能扛住多大压力，可以异步且非紧急的该扔消息队列就扔过去。

</div>

<div>

\

</div>

<div>

那么nginx 为什么能扛住这么大压力，当前应用很广泛的 nginx + lua
是怎么做的？

</div>

<div>

redis 是如何做到这么高效的？

</div>

<div>

Java 中最常使用的tomcat能处理多少的qps？

</div>

<div>

类似的这一系列的问题搞清楚后，才敢动手处理问题，要不一堆"高大上"技术的堆叠，只会导致心里没底和现场的尴尬。

</div>

<div>

\

</div>

<div>

先说nginx，nignx本质上是一个反向代理服务器，可以作为web服务器和负载均衡服务器，相对apache
，nginx更擅长处理静态请求，cpu内存使用率极低，并发数相对于其他web服务器要高不少能到50000并发连接数，qps比这个要更高。

</div>

<div>

那nginx是如何做到的，它描述的这些在生产环境中是否有效并且如何才能发挥出来？

</div>

<div>

在说后续问题时，先看一下一些操作系统知识：

</div>

<div>

\

</div>

<div>

关于epoll、poll、select

</div>

<div>

单个进程能够监视的文件描述符的数量存在最大限制，通常是1024，

</div>

<div>

select不足的地方：

</div>

<div>

1 每次select都要把全部IO句柄复制到内核

</div>

<div>

2 内核每次都要遍历全部IO句柄，以判断是否数据准备好

</div>

<div>

3 select模式最大IO句柄数是1024，太多了性能下降明显

</div>

<div>

\

</div>

<div>

poll：

</div>

<div>

poll使用链表保存文件描述符，因此没有了监视文件数量的限制，但其他三个缺点依然存在。

</div>

<div>

\

</div>

<div>

拿select模型为例，假设我们的服务器需要支持100万的并发连接，则在_FD_SETSIZE为1024的情况下，则我们至少需要开辟1k个进程才能实现100万的并发连接。除了进程间上下文切换的时间消耗外，从内核/用户空间大量的无脑内存拷贝、数组轮询等，是系统难以承受的。因此，基于select模型的服务器程序，要达到10万级别的并发访问，是一个很难完成的任务。

</div>

<div>

\

</div>

<div>

epoll的特点

</div>

<div>

\

</div>

<div>

1 每次新建IO句柄(epoll_create)才复制并注册(epoll_register)到内核

</div>

<div>

2 内核根据IO事件，把准备好的IO句柄放到就绪队列

</div>

<div>

3 应用只要轮询(epoll_wait)就绪队列，然后去读取数据

</div>

<div>

只需要轮询就绪队列（数量少），不存在select的轮询，也没有内核的轮询，不需要多次复制所有的IO句柄。因此，可以同时支持的IO句柄数轻松过百万。

</div>

<div>

\

</div>

<div>

关于线程和进程（这个好像说过好多次了）

</div>

<div>

1.定义

</div>

<div>

进程是具有一定独立功能的程序关于某个数据集合上的一次运行活动,进程是系统进行资源分配和调度的一个独立单位.

</div>

<div>

线程是进程的一个实体,是CPU调度和分派的基本单位,它是比进程更小的能独立运行的基本单位.线程自己基本上不拥有系统资源,只拥有一点在运行中必不可少的资源(如程序计数器,一组寄存器和栈),但是它可与同属一个进程的其他的线程共享进程所拥有的全部资源.

</div>

<div>

2.关系

</div>

<div>

一个线程可以创建和撤销另一个线程;同一个进程中的多个线程之间可以并发执行.

</div>

<div>

相对进程而言，线程是一个更加接近于执行体的概念，它可以与同进程中的其他线程共享数据，但拥有自己的栈空间，拥有独立的执行序列。

</div>

<div>

3.区别

</div>

<div>

进程和线程的主要差别在于它们是不同的操作系统资源管理方式。进程有独立的地址空间，一个进程崩溃后，在保护模式下不会对其它进程产生影响，而线程只是一个进程中的不同执行路径。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉，所以多进程的程序要比多线程的程序健壮，但在进程切换时，耗费资源较大，效率要差一些。但对于一些要求同时进行并且又要共享某些变量的并发操作，只能用线程，不能用进程。

</div>

<div>

1\) 简而言之,一个程序至少有一个进程,一个进程至少有一个线程.

</div>

<div>

2\) 线程的划分尺度小于进程，使得多线程程序的并发性高。

</div>

<div>

3\)
另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

</div>

<div>

4\)
线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。

</div>

<div>

5\)
从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

</div>

<div>

4.优缺点

</div>

<div>

线程和进程在使用上各有优缺点：线程执行开销小，但不利于资源的管理和保护；而进程正相反。同时，线程适合于在SMP机器上运行，而进程则可以跨机器迁移。

</div>

<div>

\

</div>

<div>

关于IO模型

</div>

<div>

两阶段式IO：

</div>

<div>

![](%E7%BD%91%E7%BB%9CIO%20%E5%9F%BA%E7%A1%80.resources/NSFileHandle_5.jpg) 
 

</div>

<div>

\

</div>

<div>

【阻塞 blocking IO】：

</div>

<div>

recvfrom -\> \[syscall -\> wait -\> copy -\>\] return OK!

</div>

<div>

![](%E7%BD%91%E7%BB%9CIO%20%E5%9F%BA%E7%A1%80.resources/NSFileHandle_2.jpg) 
 \

</div>

<div>

\

</div>

<div>

【非阻塞 nonblocking IO】：

</div>

<div>

recvfrom -\>\[syscall -\> wait -\> \] return no data ready

</div>

<div>

recvfrom -\>\[syscall -\> wait -\> \] return no data ready

</div>

<div>

recvfrom -\>\[syscall -\> wait -\> \] return data ready

</div>

<div>

recvfrom -\>\[syscall -\> copy -\> \] return OK!

</div>

<div>

![](%E7%BD%91%E7%BB%9CIO%20%E5%9F%BA%E7%A1%80.resources/NSFileHandle_1.jpg) 
 \

</div>

<div>

【多路复用IO multiplexingIO】

</div>

<div>

其中每个IO都是非阻塞IO，先使用poll/select
轮训IO句柄，如果有准备好的，开始第二阶段IO（阻塞读数据）

</div>

<div>

select/poll -\> \[syscall -\> wait -\> \] return readable

</div>

<div>

recvfrom -\> \[syscall -\> copy -\> \] return OK! 

</div>

<div>

![](%E7%BD%91%E7%BB%9CIO%20%E5%9F%BA%E7%A1%80.resources/NSFileHandle_6.jpg) 
 \

</div>

<div>

\

</div>

<div>

【信号驱动IO signal driven IO】

</div>

<div>

首先构建一个信号处理器，然后第二阶段阻塞读数据

</div>

<div>

signal handle -\> \[syscal -\> wait -\> \] return

</div>

<div>

\[syscall -\> \] signal handle-\> recvfrom -\> \[syscall -\> copy -\> \]
return OK!

</div>

<div>

![](%E7%BD%91%E7%BB%9CIO%20%E5%9F%BA%E7%A1%80.resources/NSFileHandle_4.jpg) 
 \

</div>

<div>

\

</div>

<div>

【异步IO asynchronous IO】

</div>

<div>

两个阶段都是非阻塞的

</div>

<div>

aio_read -\> \[syscall -\> wait -\> \] return

</div>

<div>

\[syscall -\> copy -\> \] aio_readCallback

</div>

<div>

\

</div>

<div>

【对比】

</div>

<div>

![](%E7%BD%91%E7%BB%9CIO%20%E5%9F%BA%E7%A1%80.resources/NSFileHandle.jpg) 
 \

</div>

<div>

![](%E7%BD%91%E7%BB%9CIO%20%E5%9F%BA%E7%A1%80.resources/NSFileHandle_3.jpg) 
 \

</div>

<div>

\

</div>

<div>

\

</div>

<div>

后面会有单独对于nginx、redis、IO模型的使用场景、reactor、netty等一系列工具进行分析，然后是处理高并发的策略，这个战线会拉的很长，学到什么写什么啦，随缘更新啦，中间将大量穿插别的。

</div>

<div>

另外上个Java
JUC系列的，其实已经写完了，公众号还没来得及排版，博客上已经都有了
[zouzq7.com](http://zouzq7.com/)

</div>

<div>

 

</div>
