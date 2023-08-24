---
author: zouzq7@163.com
created: "2018-11-27 02:37:31 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: JVM 监控 1
updated: "2018-11-27 12:53:06 +0000"
---

<div>

服务监控是一个服务中非常重要的组成部分，直接决定了问题的发现及排查速度，并且从一定程度上提高服务一个周期内的可用性。在Java服务中，除了对于
业务、接口耗时&性能的监控之外还需要对Java
所依赖的JVM进行一定的监控策略。对于JVM的合理监控可以帮助我们更加全面的发现问题：比如说内部接口耗时忽然上升、oom频出这类问题，并且合理的JVM监控及分析策略，能够以此为依据进行服务所使用JVM的调优，从而提升稳定性及性能。

</div>

<div>

JVM 自带了一些工具，已经基本能够全面的分析JVM的使用情况了：

</div>

<div>

jstat：JVM 内存监视工具，可以看到任何堆内存、非堆内存的使用情况及young
GC发生的时间&次数、full GC的时间&次数、GC总耗时等，

</div>

<div>

jmap：jmap的监控分析也是针对内存的，不过和jstat相比较而言，jstat
主要从实际内存使用情况来看，更像是一种性能监控工具（比如说心产生了多少新对象，发生了GC回收了多少、解析了多少class文件到内存），而jmap
更像是站在对象的角度来看问题，可以简单理解为内存快照分析工具（jmap可以打印指定java进程的heap快照,主要是用于分析对象回收情况排查内存泄漏等）

</div>

<div>

jps：可以输出并修改运行似的java 进程的opts

</div>

<div>

jconsle：一个Java
GUI监视工具能够连接远程的服务器的JVM来进行监控，但是在实际生产环境中，这个并不适用。

</div>

<div>

jstack：jstack是一个线程分析工具，主要用于死锁的排查及解析，线程行为的分析。

</div>

<div>

我们经常用的主要是：jstat（性能分析）、jmap（内存快照分析）、jstack（线程分析）

</div>

<div>

Jstat
的使用是最频繁的，因为对于JVM来说最常见的调优就是关于GC和JIT的，而如何去进行GC的调优通常依赖的工具就是Jstat

</div>

<div>

![](JVM%20%E7%9B%91%E6%8E%A7%201.resources/0BFE4863-659C-4DC2-82A6-9D5178C2782B.png) 
 \

</div>

:::  
<div>

[常见命令参数：] 

</div>
:::

<div>

[-class：统计class
loader行为信息 ] 

</div>

<div>

[-compile：统计编译行为信息 ] 

</div>

<div>

[-gc：统计jdk
gc时heap信息 ] 

</div>

<div>

[-gccapacity：统计不同的generations（不知道怎么翻译好，包括新生区，老年区，permanent区）相应的heap容量情况 ] 

</div>

<div>

[-gccause：统计gc的情况，（同-gcutil）和引起gc的事件 ] 

</div>

<div>

[-gcnew：统计gc时，新生代的情况 ] 

</div>

<div>

[-gcnewcapacity：统计gc时，新生代heap容量 ] 

</div>

<div>

[-gcold：统计gc时，老年区的情况 ] 

</div>

<div>

[-gcoldcapacity：统计gc时，老年区heap容量 ] 

</div>

<div>

[-gcpermcapacity：统计gc时，permanent区heap容量 ] 

</div>

<div>

[-gcutil：统计gc时，heap情况 ] 

</div>

<div>

比如： 我们查一下目标Java 的pid，然后假如是12345

</div>

<div>

jstat -gc 12345

</div>

<div>

之后通常会得到下面的一点信息

</div>

<div>

![](JVM%20%E7%9B%91%E6%8E%A7%201.resources/D4768E47-11C8-4734-8BD2-454301DA3B3D.png) 
 \

</div>

<div>

然后来看一下各项参数的含义：

</div>

<div>

S0开头的  --- Heap上的 Survivor space 0 区已使用空间的百分比

</div>

<div>

S0C：S0当前容量的大小

</div>

<div>

S0U：S0已经使用的大小

</div>

<div>

\

</div>

<div>

S1开头的  --- Heap上的 Survivor space 1 区已使用空间的百分比

</div>

<div>

S1C：S1当前容量的大小

</div>

<div>

S1U：S1已经使用的大小

</div>

<div>

\

</div>

<div>

E开头的   --- Heap上的 Eden space 区已使用空间的百分比

</div>

<div>

EC：Eden space当前容量的大小

</div>

<div>

EU：Eden space已经使用的大小

</div>

<div>

\

</div>

<div>

O开头的   --- Heap上的 Old space 区已使用空间的百分比

</div>

<div>

OC：Old space当前容量的大小

</div>

<div>

OU：Old space已经使用的大小

</div>

<div>

\

</div>

<div>

P开头的   --- Perm space 区已使用空间的百分比

</div>

<div>

OC：Perm space当前容量的大小

</div>

<div>

OU：Perm space已经使用的大小

</div>

<div>

\

</div>

<div>

YGC --- 从应用程序启动到采样时发生 Young GC 的次数

</div>

<div>

YGCT-- 从应用程序启动到采样时 Young GC 所用的时间(单位秒)

</div>

<div>

FGC --- 从应用程序启动到采样时发生 Full GC 的次数

</div>

<div>

FGCT-- 从应用程序启动到采样时 Full GC 所用的时间(单位秒)

</div>

<div>

GCT ---
从应用程序启动到采样时用于垃圾回收的总时间(单位秒)，它的值等于YGC+FGC

</div>

<div>

\

</div>

<div>

知道了参数的含义之后，我们需要对着这些数据来分析得到对应JVM参数的调节策略，说一点比较常见的，其实我们需要根据业务场景来具体的对于数量及变化情况进行评估

</div>

<div>

通常来说我们关心的有FGC的数量，这个是会导致stop
world，然后再就是FGC之后各个内存区域变化情况，看看老年代、新生代等设置是否合理。

</div>

<div>

。比如Eden区及Old区虽然使用情况会出现一些波动，但还是会维持在一定数值上。如果出现持续增长，GC似乎已经没有效果，就要考虑要不要拉一下jmap
看看是否存在内存泄漏，以免产生OOM，影响服务的可用性。除此之外根据日常heap
稳定的数值来确定各个区域的最大值、常规值也是非常重要的，需要根据数值设定并且确定面对突发流量时的最大值设置。

</div>

<div>

具体的调优策略会在JVM系列的JVM参数介绍时说明，这里仅仅是介绍对应的工具，及我们能用这些工具做什么。

</div>

<div>

个人感觉jstat
更像是提供了一种解决问题的样例方案，我们可以在Java代码中直接实现与jstat类似的功能，然后直接监控stat日志就可以了，当然也可以写一个监控脚本直接使用jstat，当需要一些定制化需求的时候自己实现也是很靠谱的。

</div>

<div>

\

</div>
