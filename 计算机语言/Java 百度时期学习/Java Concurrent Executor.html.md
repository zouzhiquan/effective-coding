---
author: zouzq7@163.com
created: "2018-08-28 06:17:33 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent Executor
updated: "2019-02-25 07:21:11 +0000"
---

<div>

在说Executor前， 先来看一下线程创建的几种方式：

</div>

<div>

1、继承Thread类创建线程

</div>

<div>

2、 实现Runable接口创建线程

</div>

<div>

3、使用Callable和Future 创建线程

</div>

<div>

4、使用Executor线程池

</div>

<div>

这几种方式是存在一定程度上的差异，首先Thread则是最原始的，创建线程执行对应的业务代码，Runable是完成了一个任务的的执行，然后Callable和Runable类似，但是提供了Future来实现了一种回调方式。这三种方式总体来说是比较原始的，线程无法复用，线程及任务管理复杂。而Executor
了为了解决这些类似的问题而实现的。

</div>

<div>

Executor有这个几个经典的实现类：ThreadPoolExecutor、ScheduledThreadPoolExecutor。

</div>

<div>

看一下UML图应该就一目了然了。

</div>

<div>

最上层的Executor ：提供了可以接受Runable对象的executor方法。

</div>

<div>

ExecutorService ：主要提供了可返回Future对象的方法。

</div>

<div>

AbstractExecutorService：
前面ExecutorService提供的函数，在其中完成了实现。

</div>

<div>

ScheduledExecutorService：提供可定时调度任务的接口

</div>

<div>

ThreadPoolExecutor：这个就是我们最常使用的线程池啦

</div>

<div>

ScheduledThreadPoolExecutor：可定时调度的线程池

</div>

<div>

![](Java%20Concurrent%20Executor.resources/76EB6256-24BF-46B9-A56E-2AAC1DC95F5F.png) 
 \

</div>

<div>

ThreadPoolExecutor是这个体系中最核心的类也是我们最常使用的一个类了，首先看它的构造函数：

</div>

<div>

[public
] ([int
] 

</div>

<div>

[                          int
] 

</div>

<div>

[                          long
] 

</div>

<div>

[                          ] TimeUnit
unit[,] 

</div>

<div>

[                         
] BlockingQueue\<Runnable\> workQueue)

</div>

<div>

\

</div>

<div>

[public
] ([int
] 

</div>

<div>

[                          int
] 

</div>

<div>

[                          long
] 

</div>

<div>

[                          ] TimeUnit
unit[,] 

</div>

<div>

[                         
] BlockingQueue\<Runnable\>
workQueue[,] 

</div>

<div>

[                         
] ThreadFactory threadFactory)

</div>

<div>

\

</div>

<div>

[public
] ([int
] 

</div>

<div>

[                          int
] 

</div>

<div>

[                          long
] 

</div>

<div>

[                          ] TimeUnit
unit[,] 

</div>

<div>

[                         
] BlockingQueue\<Runnable\>
workQueue[,] 

</div>

<div>

[                         
] RejectedExecutionHandler handler)

</div>

<div>

\

</div>

<div>

[public
] ([int
] 

</div>

<div>

[                          int
] 

</div>

<div>

[                          long
] 

</div>

<div>

[                          ] TimeUnit
unit[,] 

</div>

<div>

[                         
] BlockingQueue\<Runnable\>
workQueue[,] 

</div>

<div>

[                         
] ThreadFactory
threadFactory[,] 

</div>

<div>

[                         
] RejectedExecutionHandler handler) 

</div>

<div>

1)corePoolSize： 线程池维护线程的最少数量 （core : 核心）

</div>

<div>

2)maximumPoolSize： 线程池维护线程的最大数量

</div>

<div>

3)keepAliveTime： 线程池维护线程所允许的空闲时间

</div>

<div>

4)unit： 线程池维护线程所允许的空闲时间的单位

</div>

<div>

5)workQueue： 线程池所使用的缓冲队列

</div>

<div>

6)handler： 线程池对拒绝任务的处理策略

</div>

<div>

7)ThreadFactory :指定的具体的线程工厂

</div>

<div>

Executors工具类中提供了创建的一系列线程池对象的方法，比如：newCachedThreadPool、newFixedThreadPool、newScheduledThreadPool、newSingleThreadPool，这些都其实就都是固定一些条件具有鲜明特点的ThreadPool。其实完全可以自己创建各种特点的Pool。

</div>

<div>

既然提到了，就说一下这几个线程池的特点：

</div>

<div>

newCachedThreadPool：可缓存线程池

</div>

<div>

newFixedThreadPool：具有固定长度的线程池，可以从一定程度上控制并发的数量，但是并发控制住了，被阻塞的请求就不一定了。

</div>

<div>

newScheduledThreadPool：支持定时执行的定长线程池，可以用来做定时任务。 

</div>

<div>

newSingleThreadPool：创建一个单线程化的线程池，它只会用唯一
的工作线程来执行任务，保证所有任务按照指定顺序（FIFO）执行，如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。感觉顺便保证了所有任务的执行顺序按照任务的提交顺序执行。

</div>

<div>

但是使用Executors来直接创建这种特性的线程池是不被推荐使用的，虽然在qps较小或者并发量非常小的场景不会出现什么问题，但是在qps下存在如下问题。具体原因，下面引用阿里巴巴开发手册中的一项：

</div>

<div>

【强制】线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor
的方式，这样
的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。
说明：Executors 返回的线程池对象的弊端如下： 

</div>

<div>

1）FixedThreadPool 和 SingleThreadPool: 允许的请求队列长度为
Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。

</div>

<div>

2）CachedThreadPool 和 ScheduledThreadPool: 允许的创建线程数量为
Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。

</div>

<div>

但为了更加正确的使用ThreadPoolExecutor，还是推荐深入理解这个类及对应的线程管理策略来实现线程池的构建及使用，而不是依赖于API去使用，API是没有问题的，但在某些环境下API可能就存在某些风险了。

</div>

<div>

关于线程池的并发安全控制策略，主要依赖于AtomicInteger、ReentrantLock、AQS
实现的。

</div>

<div>

\

</div>

<div>

然后说一下具体的线程池规则：

</div>

<div>

如果任务队列没有大小限制：

</div>

<div>

1、如果线程数量\<=核心线程数量，那么直接启动一个核心线程来执行任务，不会放入队列中。

</div>

<div>

2、如果线程数量\>核心线程数，但\<=最大线程数，并且任务队列是LinkedBlockingDeque的时候，超过核心线程数量的任务会放在任务队列中排队。

</div>

<div>

3、如果线程数量\>核心线程数，但\<=最大线程数，并且任务队列是SynchronousQueue的时候，线程池会创建新线程执行任务，这些任务也不会被放在任务队列中。这些线程属于非核心线程，在任务完成后，闲置时间达到了超时时间就会被清除。

</div>

<div>

4、如果线程数量\>核心线程数，并且\>最大线程数，当任务队列是LinkedBlockingDeque，会将超过核心线程的任务放在任务队列中排队。也就是当任务队列是LinkedBlockingDeque并且没有大小限制时，线程池的最大线程数设置是无效的，他的线程数最多不会超过核心线程数。

</div>

<div>

5、如果线程数量\>核心线程数，并且\>最大线程数，当任务队列是SynchronousQueue的时候，会因为线程池拒绝添加任务而抛出异常。

</div>

<div>

任务队列大小有限：

</div>

<div>

1、如果是LinkedBlockingDeque，塞满时，新增的任务会直接创建新线程来执行，当创建的线程数量超过最大线程数量时会抛异常。

</div>

<div>

2、如果是SynchronousQueue，因为没有数量限制，根本不保持这些任务，而是直接交给线程池去执行。当任务数量超过最大线程数时会直接抛异常。

</div>

<div>

\

</div>

<div>

以上是使用的内容，下面来看看Executor的两级调度模型：

</div>

<div>

![](Java%20Concurrent%20Executor.resources/7D132641-19FC-4F9B-BE6A-583713FB081A.png) 
 \

</div>

<div>

首先提交任务到Executor，然后Executor维护着一组Java
线程，而Java线程与操作系统线程之间的对应关系中间就是前面文章中提到的：

</div>

<div>

操作系统线程模型：

</div>

<div>

1）、线程实现在用户空间下

</div>

<div>

    当线程在用户空间下实现时，操作系统对线程的存在一无所知，操作系统只能看到进程，而不能看到线程。所有的线程都是在用户空间实现。在操作系统看来，每一个进程只有一个线程。过去的操作系统大部分是这种实现方式，这种方式的好处之一就是即使操作系统不支持线程，也可以通过库函数来支持线程。

</div>

<div>

    这种模式最致命的缺点也是由于操作系统不知道线程的存在，因此当一个进程中的某一个线程进行系统调用时，比如缺页中断而导致线程阻塞，此时操作系统会阻塞整个进程，即使这个进程中其它线程还在工作。还有一个问题是假如进程中一个线程长时间不释放CPU，因为用户空间并没有时钟中断机制，会导致此进程中的其它线程得不到CPU而持续等待。

</div>

<div>

2）、内核线程就是直接由操作系统内核（Kernel）支持的线程，这种线程由内核来完成线程切换，内核通过操纵调度器（Scheduler）对线程进行调度，并负责将线程的任务映射到各个处理器上。每个内核线程可以视为内核的一个分身，这样操作系统就有能力同时处理多件事情，支持多线程的内核就叫做多线程内核（Multi-Threads
Kernel）。

</div>

<div>

3）、在这种混合实现下，即存在用户线程，也存在轻量级进程。用户线程还是完全建立在用户空间中，因此用户线程的创建、切换、析构等操作依然廉价，并且可以支持大规模的用户线程并发。而操作系统提供支持的轻量级进程则作为用户线程和内核线程之间的桥梁，这样可以使用内核提供的线程调度功能及处理器映射，并且用户线程的系统调用要通过轻量级进程来完成，大大降低了整个进程被完全阻塞的风险。在这种混合模式中，用户线程与轻量级进程的数量比是不定的，即为N:M的关系。

</div>

<div>

\

</div>

<div>

\

</div>
