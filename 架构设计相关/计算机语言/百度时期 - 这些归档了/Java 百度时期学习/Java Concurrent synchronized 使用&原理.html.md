---
author: zouzq7@163.com
created: "2018-08-03 07:31:57 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent synchronized 使用&原理
updated: "2019-02-25 12:33:38 +0000"
---

<div>

sychronized 用法：

</div>

<div>

sychronized 是Java语法层面的同步策略，可以用来修饰instance变量、object
reference（对象引用）、static函数和class literals(类名称字面常量)。

</div>

<div>

1、当非static
元素被sychronized修饰时，当前线程都会取得该对象锁，该对象的其他线程均无法访问任何被sychronized修饰的变量或方法。即一个类如果有n个方法被sychronized修饰时，a线程取得对象锁之后，其他线程除a线程正在使用的方法无法使用外，其他需要对象锁的方法均无法使用。即一个对象仅有一个对象锁，一个线程取得后，其他线程都无法获得，其他线程都要阻塞。

</div>

<div>

2、不同的对象实例的 synchronized方法是不相干扰的。

</div>

<div>

3、当static
元素被sychronize修饰时，可以防止多个线程同时访问这个类中的synchronized
static 方法。它可以对类的所有对象实例起作用。

</div>

<div>

注意：synchronized都是会阻塞线程的，就是说会发生上下文切换，从用户态切换到内核态，所以由sychronized实现对象锁代价较高（新的JDK版本已经优化的较好，但这种方式代价仍然不小），并且使用sychronized涉及对象锁如果在两个以上很容易造成死锁，谨慎使用同步策略，避免无谓的取锁。

</div>

<div>

\

</div>

<div>

很显然sychronized是一种独占锁，也就是悲观锁，默认一定会发生资源争用，所以每次都默认取锁。

</div>

<div>

\

</div>

<div>

sychronized 原理：

</div>

<div>

同步是使用monitorenter和monitorexit指令实现的，monitorenter尝试获取对象的锁，如果该对象没被锁定或者当前线程已经获取了锁，则把锁的计数器+1，同样monitorexit把锁的计数器-1。所以synchronized对于同一个线程是可重入的。

</div>

<div>

Sychronized是基于监视器实现的，对象锁为对象实例的监视器，class锁为class监视器，锁住的其实对应的监视器。

</div>

<div>

当出现n个线程请求对象监视器时，监视器会设置这样几个状态来表示所请求的线程。

</div>

<div>

contentionlist：存放所有线程的竞争队列；

</div>

<div>

entrylist：存放复合条件的候选竞争队列；

</div>

<div>

ondeck：任何时刻仅有一个线程可以直接竞争锁，这个被称为ondeck。

</div>

<div>

owner：对象锁的持有者，正在执行的线程

</div>

<div>

waitlist：执行过程中，因wait方法被阻塞的线程。

</div>

<div>

\

</div>

<div>

![](Java%20Concurrent%20synchronized%20%E4%BD%BF%E7%94%A8&%E5%8E%9F%E7%90%86.resources/B3C19FC1-50E1-4BAD-93EF-BB667DA7D421.png) 
 \

</div>

<div>

\

</div>

<div>

首先来竞争的线程会进入contentionlist，如果entrylist为空时，从contentionlist中取得，然后依次竞争锁然后执行，被wait阻塞进入waitset或者执行完成unlock释放锁。当被阻塞线程被notify或者notifyAll唤醒时，重新进入enterylist，等待获得竞争锁的权利。

</div>

<div>

1、其中contentionlist并不是真正的queue，而是一种虚拟队列，因为ContentionList仅由Node及其next指针逻辑构成，并不存在一个Queue的数据结构，准确来说就是一个普通list，仅仅是采用FIFO操作而已，每次新节点时都会在队首进行，通过CAS操作（后续会有单独篇幅介绍CAS）改变第一个节点的的指针为新增节点，同时设置新增节点的next指向后续节点，而取得操作则发生在队尾，且只有一个线程会去，所以这里的CAS操作不会产生ABA问题。显然，该结构其实是个Lock-Free的队列。

</div>

<div>

ps：

</div>

<div>

lock-free:需要取得锁的线程在有限步骤或时间内就可以成功，多数线程都会成功，一些可能失败。

</div>

<div>

wait-free:需要取得锁的线程在有限步骤或时间内就可以成功，任意线程都会成功，语义更加强烈。

</div>

<div>

2、entrylist也是等待队列，因为contentionlist会被线程并发访问，为了降低对contentionlist队尾争用，所以产生了entrylist结构。

</div>

<div>

3、当entry为空时，会将contention中的线程迁移到entrylist，并且会指定其中的某个线程（通常是第一个）为ondeck，并不是直接给他锁，而是给它竞争锁的权利，ondeck需要重新竞争锁，这样牺牲了一定的公平性，但极大的提高了整体吞吐量，Hotspot中把OnDeck的选择行为称之为"竞争切换"。

</div>

<div>

ps：阻塞操作由操作系统完成，在Linxu下通过pthread_mutex_lock函数实现。

</div>

<div>

 

</div>

<div>

说到这里，sychronized主流程已经清楚了，但还有两个概念，自旋锁、自适应性自旋锁。

</div>

<div>

1、自旋锁就是monitor并不把线程阻塞放入排队队列，而是去执行空转，空转后看看是否锁已释放并直接进行竞争获得cpu，如果竞争不到继续自旋，循环过程中线程的状态一直处于running状态。明显自旋锁使得synchronized的对象锁方式在线程之间引入了不公平。但是这样可以保证大吞吐率和执行效率。自旋锁方式省去了阻塞线程的时间和空间（队列的维护等）开销，但是长时间自旋也是很低效的。所以自旋的次数一般控制在一个范围内，例如10,50等，在超出这个范围后，线程就进入排队队列。

</div>

<div>

2、自适应自旋锁，就是自旋的次数是通过JVM在运行时收集的统计信息，动态调整自旋锁的自旋次数上界。

</div>

<div>

自旋的发生时机：进入contentionlist前，先尝试自旋获得锁，若失败，再去排队。

</div>
