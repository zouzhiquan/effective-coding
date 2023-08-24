---
author: zouzq7@163.com
created: "2018-08-03 06:23:17 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent volatile
updated: "2019-03-04 06:49:47 +0000"
---

<div>

volatile 是一个Java
中的关键字，一个提供基础同步属性的关键字。针对JVM重排序在并发场景下的问题，被vlolatile修饰的关键词，编译器不会将该变量的操作与其他内存操作进行重排序。

</div>

<div>

\

</div>

<div>

1、重排序：如果熟悉JVM的话，应该就很清楚Java的重排序了。重排序主要分为：编译器优化的重排序、指令级别并行的重排序、内存系统的重排序。其中编译器重排序：在不改变代码语义的情况下，优化性能而改变了代码执行顺序。指令并行的重排序：处理器采用并行技术使多条指令重叠执行，在不存在数据依赖的情况下，改变机器指令的执行顺序。内存系统的重排序：使用缓存和读写缓冲区时，加载和存储可能是乱序执行。

</div>

<div>

2、JMM：java
内存模型（一种语言级别的内存模型），Java内存模型分为一个工作线程、线程私有的工作内存、共享的主存。

</div>

<div>

![](Java%20Concurrent%20volatile.resources/6338BFC5-D7AC-43B9-AF0B-1DD6AAA2FF7C.png) 
 \

</div>

<div>

3、as-if-serial：无论怎么排，要保证和串行执行的结果一致。对于单线程来说，对存在依赖关系的操作进行重排序，不会改变最后的执行结果，在多线程程序中，对存在依赖关系的操作进行重排序，可能会改变最后的执行结果。就需要我们自己来保证as-if-serial了。

</div>

<div>

4、hanpens-before：指前一个操作对后一个操作可见。（注意一点：不是前一个操作必须在后一个操作之前执行，不是串行执行）

</div>

<div>

看完这三个前提，就可以去理解volatile了，

</div>

<div>

\

</div>

<div>

volatile更像是语言层面，对于JVM执行过程中对于重排序的限制，是一种内存屏障，这个屏障就像是一堵墙，阻止了前后的重排序操作（具体屏障：LoadLoad屏障和LoadStore屏障）

</div>

<div>

![](Java%20Concurrent%20volatile.resources/02F9C110-28E3-4D85-96CA-1DF993BC945E.png) 
 \

</div>

<div>

StoreStore、StoreLoad

</div>

<div>

![](Java%20Concurrent%20volatile.resources/B3FFCBC5-64C9-467E-8FBF-0E6D48D472D5.png) 
 \

</div>

<div>

volatile
保证了一种可见性，什么意思呢，就是说volatile修饰的变量，产生变化时，立马写入主存。保证其他线程能发现其变化，保证了正确性，也就是happens-before。

</div>

<div>

\

</div>

<div>

常见问题：

</div>

<div>

1、volatile不是锁，仅仅是一种程序执行过程中的指令级的同步策略，保证了可见性、避免重排序

</div>

<div>

2、volatile能够维持被修饰变量单个的原子操作。所以说，对于volatile double
进行单个写操作在多线程并发环境下是完全正常的。这里注意一点，一定是单个操作，像是i++；
i\--；之类的这明显是复合操作哈，别较真当作单个操作。

</div>

<div>

\

</div>

<div>

使用场景：

</div>

<div>

1、保证long、double等的单个操作的原子性，不至于写完一半被读走。

</div>

<div>

2、作为基础信号量，发生变化时，能够其他线程周知，但记住一点复合操作可不保证原子性，读后写什么的在多线程并发环境下还是进行同步操作吧。

</div>

<div>

\

</div>

<div>

volatile
差不多就这些，真正的使用体验可以尝试自己写demo，比如说通过信号量改变打断死循环，多线程疯狂i++操作等。看concurrent包中的源码时，volatile是必须要理解的。

</div>
