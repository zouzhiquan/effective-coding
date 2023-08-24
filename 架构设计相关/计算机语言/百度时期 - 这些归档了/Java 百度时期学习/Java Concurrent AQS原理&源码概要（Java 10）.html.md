---
author: zouzq7@163.com
created: "2018-08-06 12:22:05 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent AQS原理&源码概要（Java 10）
updated: "2019-03-04 06:30:43 +0000"
---

<div>

开始说AQS之前，继续说上一篇没说完的建议，相对于看一些不知道时效性的blog，说实话，理解一个知识点最简便的方式就是看论文及源码实现了，解决一个问题最好的方式就是看官方文档及源码，没有什么答案是在源码里找不到的，对着blog排查问题只会导致抱着一个黑盒子在原地踏步，偶尔运气好时间短过去了，运气不好，花很长时间不说，下次问题稍微变动一下就又不会了。切身体会，关于Concurrent的论文，The
java.util.concurrent Synchronizer Framewor，大家如果有需要可以私信。

</div>

<div>

AQS维护了一个volatile int
state（可以理解为锁状态）和一个FIFO线程等待队列（多线程争用资源被阻塞时会进入此队列）。

</div>

<div>

放一张示意图：

</div>

<div>

![](Java%20Concurrent%20AQS%E5%8E%9F%E7%90%86&%E6%BA%90%E7%A0%81%E6%A6%82%E8%A6%81%EF%BC%88Java%2010%EF%BC%89.resources/88F281F5-2F62-48FD-898F-445F0F0EEBAD.png) 
 \

</div>

<div>

AQS的设计使用了一个解决争用问题的经典经常，有的称为惊群效应，在AQS实现思路之前：如果存在n个（数量非常多）的线程等待竞争锁，当锁释放时会唤醒所有线程去竞争锁，但最后肯定仅有一个锁竞争成功。其他线程只能再次等待唤醒再次竞争，这导致资源的大量浪费。所以我们需要一种策略来唤醒某个特定的线程避免过多的唤醒及响应争用时的资源浪费。AQS维护了一个FIFO的队列，每个节点仅关心前一个节点的状态，唤醒也仅仅是唤醒首节点。这种实现方式不仅仅是AQS，更经典的是Zookeeper中分布式锁的实现。

</div>

<div>

AQS定义两种资源共享方式：Exclusive（独占，只有一个线程能执行，如ReentrantLock）和Share（共享，多个线程可同时执行，如Semaphore/CountDownLatch）。不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。

</div>

<div>

AQS
全称AbstractQueuedSynchronizer，是CountDownLatch/FutureTask/ReentrantLock/RenntrantReadWriteLock/Semaphore的基础，因此AbstractQueuedSynchronizer是Lock/Executor实现的前提，然后因为AQS是一个抽象类，如若想要使用AQS继承就可，不过日常场景中大多是使用它的上层应用。还有另一个类叫做AbstractQueuedLongSynchronizer，这是不知道为什么之前看过的所有blog都没有提到，看了下官方文档这个是Java
8之后的实现，一个64位版本。其结构和功能与AbstractQueuedSynchronizer一致。

</div>

<div>

下面开始看代码：

</div>

<div>

\

</div>

<div>

[/\*\*] 

</div>

<div>

[\*] 
[\@since] 
[1.6] 

</div>

<div>

[\*] 
[\@author] 
[Doug Lea] 

</div>

<div>

[\*/] 

</div>

<div>

[public abstract class] 
AbstractQueuedLongSynchronizer

</div>

<div>

    [extends] 
AbstractOwnableSynchronizer

</div>

<div>

    [implements]  java.io.Serializable
{

</div>

<div>

\

</div>

<div>

    [private static final long] 
[serialVersionUID] 
=
[7373984972572414692L] 

</div>

<div>

\

</div>

<div>

它实现了序列化接口，然后继承自AbstractOwnableSynchronizer。

</div>

<div>

AbstractOwnableSynchronizer：

</div>

<div>

独占线程 为transient的，private transient Thread
exclusiveOwnerThread，然后是独占线程的setter和getter方法，就是将独占线程的使用抽离出AQS的使用中。

</div>

<div>

\

</div>

<div>

下面看AQS持有的对象成员，我把注释翻译一下，可能就很清晰了，如果不正确，肯请指正，我能及时修改。

</div>

<div>

[/\*\*] 

</div>

<div>

[\* Head of the wait queue, lazily initialized.  Except
for] 

</div>

<div>

[\* initialization, it is modified only via method setHead. 
Note:] 

</div>

<div>

[\* If head exists, its waitStatus is guaranteed not to
be] 

</div>

<div>

[\*等待队列的头部，延迟初始化。除了[初始化，仅通过方法setHead进行修改。] 

</div>

<div>

[\*] 

</div>

<div>

[\*/] 

</div>

<div>

[private transient volatile]  Node
[head] 

</div>

<div>

\

</div>

<div>

[/\*\*] 

</div>

<div>

[\* Tail of the wait queue, lazily initialized.  Modified only
via] 

</div>

<div>

[\* method enq to add new wait
node.] 

</div>

<div>

[\*[等待队列的尾部，延迟初始化。仅通过方法enq修改以添加新的等待节点。] 

</div>

<div>

[\*/] 

</div>

<div>

[private transient volatile]  Node
[tail] 

</div>

<div>

\

</div>

<div>

[/\*\*] 

</div>

<div>

[\* The synchronization
state.] 

</div>

<div>

[\*具体的同步状态] 

</div>

<div>

[\*/] 

</div>

<div>

[private volatile long] 
[state] 

</div>

<div>

\

</div>

<div>

然后下面几个函数是这几个成员的setter、getter，其中Set、compareAndSet等函数与Java
8的实现不同的是，使用VarHandle实现，具体VarHandle介绍可以参考上一篇《Java
Concurrent Atomic（JDK 10）》。

</div>

<div>

接下来是一个自旋为超时阈值。

</div>

<div>

[/\*\*] 

</div>

<div>

[\* The number of nanoseconds for which it is faster to
spin] 

</div>

<div>

[\* rather than to use timed park. A rough estimate
suffices] 

</div>

<div>

[\* to improve responsiveness with very short
timeouts.] 

</div>

<div>

[\*实际[能更快地旋转而不是使用固定的纳秒数。粗略的估计足以在很短的时间内提高响应能力。] 

</div>

<div>

[\*/] 

</div>

<div>

[static final long] 
[SPIN_FOR_TIMEOUT_THRESHOLD] 
=
[1000L] 

</div>

<div>

函数内容都是双指针链表的经典操作，但是代码写的很优雅，参考下代码规范也是很不错的。

</div>

<div>

以下为基础的支撑函数：

</div>

<div>

enq：insert函数

</div>

<div>

addwaiter：创建要添加的node

</div>

<div>

unparkSuccessor：如果存在元素，弹出元素。

</div>

<div>

到重要部分了：很明显的看到里面使用的经典的CAS操作，具体的细节可以看下面代码。

</div>

<div>

作用：释放共享模式的操作------信号继承并确保传播。(注意:对于独占模式，释放只是在需要信号时调用head的unpark继承者)。属于通过CAS对于信号量进行正确性控制。

</div>

<div>

[private void] 
[doReleaseShared] () {

</div>

<div>

[   ] 
[for] 
([;;] ) {

</div>

<div>

        Node h =
[head] 

</div>

<div>

[        if]  (h !=
[null]  && h !=
[tail] ) {

</div>

<div>

            [int]  ws =
h.[waitStatus] 

</div>

<div>

[            if]  (ws ==
Node.[SIGNAL] ) {

</div>

<div>

                [if] 
(\![h.compareAndSetWaitStatus(Node](http://h.compareAndSetWaitStatus(Node).[SIGNAL] 
[0] ))

</div>

<div>

                   
[continue;           ]  [// loop to
recheck cases] 

</div>

<div>

[               ] 
unparkSuccessor(h)[;] 

</div>

<div>

[           ]  }

</div>

<div>

            [else if]  (ws ==
[0]  &&

</div>

<div>

                    
\![h.compareAndSetWaitStatus](http://h.compareAndSetWaitStatus)([0] 
Node.[PROPAGATE] ))

</div>

<div>

               
[continue;               ]  [// loop
on failed CAS] 

</div>

<div>

[       ]  }

</div>

<div>

        [if]  (h ==
[head] )                   [// loop
if head changed] 

</div>

<div>

[           ] 
[break;] 

</div>

<div>

[   ]  }

</div>

<div>

}

</div>

<div>

\

</div>

<div>

下面是public函数的内部支持函数，细节不过多描述，简述功能，具体实现太长了，大家可以参照以下看对应的源码。

</div>

<div>

setHeadAndPropagate：设置队列头部，并检查后续是否在共享模式下等待，如果传播了\>
0或传播状态，则进行传播。

</div>

<div>

cancelAcquire：取消正在尝试的取操作。

</div>

<div>

shouldParkAfterFailedAcquire：检查和更新未能获取的节点的状态。如果线程应该阻塞，返回true。这是所有获取循环的主要信号控制。要求pred
= node.prev。

</div>

<div>

selfInterrupt：中断当前线程

</div>

<div>

parkAndCheckInterrupt：检查是否处于被中断状态

</div>

<div>

acquireQueued：为队列中已经存在的线程获取独占的不可中断模式。用于条件等待方法和获取方法。

</div>

<div>

doAcquireInterruptibly：获取独占可中断模式。

</div>

<div>

展示一点细节性的东西，对咱们写代码很有帮助的。

</div>

<div>

[if]  (p ==
[head]  && tryAcquire(arg)) {

</div>

<div>

    setHead(node)[;] 

</div>

<div>

[   ] 
p.[next]  =
[null;]  [// help
GC] 

</div>

<div>

[   ]  [return
true;] 

</div>

<div>

}

</div>

<div>

然后是各种模式下的取操作，不一一简述了。

</div>

<div>

fullGetFirstQueuedThread：当fastpath失败时调用的getFirstQueuedThread的版本。

</div>

<div>

\

</div>

<div>

对外开放的使用函数：

</div>

<div>

hasQueuedThreads：展示持有的线程

</div>

<div>

hasContended：查询是否有任何线程争取这个同步器;也就是说，如果获取方法曾经被阻塞。

</div>

<div>

getFirstQueuedThread：返回队首元素，若果没有则null。

</div>

<div>

isQueued：检查是否在排队

</div>

<div>

apparentlyFirstQueuedIsExclusive：返回{@code
true}，如果第一个明显的队列线程(如果存在)正在排他模式中等待。如果该方法返回{@code
true}，并且当前线程正在尝试以共享模式获取(即，该方法从{@link)调用然后保证当前线程不是第一个排队的线程。仅用于ReentrantReadWriteLock中的启发式。

</div>

<div>

hasQueuedPredecessors：查询是否有任何线程等待获取的时间超过当前线程。

</div>

<div>

getQueueLength：获取长度

</div>

<div>

getQueuedThreads：获取正在等待的线程

</div>

<div>

getExclusiveQueuedThreads：返回一个包含可能等待以独占模式获取的线程的集合。它具有与{@link
#getQueuedThreads}相同的属性，但是它只返回由于独占获取而等待的线程。

</div>

<div>

getExclusiveQueuedThreads：功能同上，但是这个获取的是shared模式下的

</div>

<div>

isOnSyncQueue：如果一个节点(总是最初放在条件队列上的节点)现在正在等待同步队列上的重新获取，则返回true。

</div>

<div>

findNodeFromTail：如果节点通过向后搜索，则返回true。只有在isOnSyncQueue需要时才调用。

</div>

<div>

transferForSignal：将一个节点从条件队列传输到同步队列。如果成功，返回true。

</div>

<div>

owns：查询给定的ConditionObject是否使用这个同步器作为其锁。

</div>

<div>

hasWaiters：查询是否有任何线程正在等待与此同步器相关的给定条件。注意，由于超时和中断可能在任何时候发生，{@code
true}返回不能保证将来的{@code
signal}将唤醒任何线程。该方法主要用于监控系统状态。

</div>

<div>

getWaitQueueLength：返回与此同步器相关联的给定条件上等待的线程数的估计值。注意，由于超时和中断可能在任何时候发生，因此估计仅作为实际服务员数量的上限。这种方法设计用于监视系统状态，而不是用于同步控制。

</div>

<div>

以下几个都是不能保证完全正确的估略值。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

然后最后是一个内部类，针对信号量及节点的条件操作。可以学习这种方式，将特定的规则收敛到对应的结构。

</div>

<div>

[public class]  ConditionObject
[implements] 
Condition[,]  java.io.Serializable {

</div>

<div>

    [private static final long] 
[serialVersionUID] 
=
[1173984872572414699L] 

</div>

<div>

[   ]  [/\*\* First node of condition
queue. \*/] 

</div>

<div>

[   ]  [private
transient]  Node
[firstWaiter] 

</div>

<div>

[   ]  [/\*\* Last node of condition
queue. \*/] 

</div>

<div>

[   ]  [private
transient]  Node
[lastWaiter] 

</div>

<div>

\

</div>

<div>

差不多就这样，过程可能有些枯燥，以上介绍了大约90%以上的函数，及关键操作，正如一开始说的，要想深入理解，还需要对着文章去看源码。

</div>

<div>

AQS除了基本的信号操作及队列操作，依赖的实现就是volatile及对应的CAS操作，而CAS是有VarHandle所支持，VarHandle又依赖于native函数，JNI本地函数调用。

</div>

<div>

说完AQS再加上之前的Atomic，第二层结构已经清晰了。

</div>

<div>

下面开始说第三层，也是代码量及工具量非常庞大的一层，后续可能更加枯燥，除去基础使用的API，更愿意简述里面的实现原理。

</div>

<div>

\

</div>
