---
author: zouzq7@163.com
created: "2018-08-03 14:30:32 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent CAS使用&原理
updated: "2018-11-01 08:51:04 +0000"
---

<div>

CAS
可以简单描述比较并交换，Java中轻量级锁的理论支持。CAS很早就出现了，并且以此为理论基础实现了很多有趣的工具，Java依赖的就是操作系统中的cmpxchg指令。

</div>

<div>

ps：这里的CAS是compare and swap，还有个常见的CAS叫做Central
Authentication Service，这个单点登录的认证方式大家有兴趣也可以去看看。

</div>

<div>

CAS操作原理：

</div>

<div>

CAS有3个操作数，内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做，因为基于底层的cmpchg原语，所以该操作是原子的。后面要介绍的Atomic值就是依赖于这个实现的，保证了更新的原子性。

</div>

<div>

\

</div>

<div>

CAS中存在的问题：

</div>

<div>

1、CAS存在一个经典的问题叫做ABA，因为CAS需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。ABA问题的解决思路就是使用版本号。在变量前面追加上版本号，每次变量更新的时候把版本号加一，那么A-B-A
就会变成1A-2B-3A。Atomic中有AtomicStampedReference来专门解决ABA问题。

</div>

<div>

2、很容易看出CAS是针对单个变量的操作。

</div>

<div>

\

</div>

<div>

concurrent包中对于CAS的使用方式：（主要是用于状态（锁状态），队列操作等）

</div>

<div>

结合之前介绍的volatile，配合CAS
实现了很多非阻塞但线程安全非阻塞数据结构，这些结构实现了一些Java多线程并发编程的工具，比如说Atomic系列、ConcurrentHashMap等

</div>

<div>

看一眼concurrent实现的结构：

</div>

<div>

![](Java%20Concurrent%20CAS%E4%BD%BF%E7%94%A8&%E5%8E%9F%E7%90%86.resources/A139B3D7-B9A2-4886-8634-741C95667922.png) 
 \

</div>

<div>

顺道提一句，基于volatile和CAS线程通信存在以下几种方式：

</div>

<div>

A线程写volatile变量，随后B线程读这个volatile变量。

</div>

<div>

A线程写volatile变量，随后B线程用CAS更新这个volatile变量。

</div>

<div>

A线程用CAS更新一个volatile变量，随后B线程用CAS更新这个volatile变量。

</div>

<div>

A线程用CAS更新一个volatile变量，随后B线程读这个volatile变量。

</div>

<div>

\

</div>

<div>

CAS实现原理：

</div>

<div>

具体的函数为[unsafe.compareAndSwapInt](http://unsafe.compareAndSwapInt)，这里使用的是JNI（Java
Native
Interface），具体使用的代码为：unsafe.cpp、atomic.cpp，可以去看看openjdk的实现。

</div>

<div>

这里是其中一点核心的函数

</div>

<div>

// Adding a lock prefix to an instruction on MP machine

</div>

<div>

// VC++ doesn\'t like the lock prefix to be on a single line

</div>

<div>

// so we can\'t insert a label after the lock prefix.

</div>

<div>

// By emitting a lock prefix, we can define a label after it.

</div>

<div>

#define LOCK_IF_MP(mp) \_\_asm cmp mp, 0  \\

</div>

<div>

                       \_\_asm je L0      \\

</div>

<div>

                       \_\_asm \_emit 0xF0 \\

</div>

<div>

                       \_\_asm L0:

</div>

<div>

</div>

<div>

inline jint     Atomic::cmpxchg    (jint     exchange_value, volatile
jint\*     dest, jint     compare_value) {

</div>

<div>

  // alternative for InterlockedCompareExchange

</div>

<div>

  int mp = os::is_MP();

</div>

<div>

  \_\_asm {

</div>

<div>

    mov edx, dest

</div>

<div>

    mov ecx, exchange_value

</div>

<div>

    mov eax, compare_value

</div>

<div>

    LOCK_IF_MP(mp)

</div>

<div>

    cmpxchg dword ptr \[edx\], ecx

</div>

<div>

  }

</div>

<div>

}

</div>

<div>

这里存在一个问题，CAS原理所依赖的是缓存的一致性（CPU层面保证一致性通常有两种方式：1、总线锁
2、缓存锁），其中在多处理器上运行时，会为cmpxchg指令加上lock前缀（lock
cmpxchg），如果在单处理器上运行，就省略lock前缀。这个lock前缀是什么含义呢，可以看一下Intel的手册说明：

</div>

<div>

1、确保对内存的读-改-写操作原子执行。在Pentium及Pentium之前的处理器中，带有lock前缀的指令在执行期间会锁住总线，使得其他处理器暂时无法通过总线访问内存。很显然，这会带来昂贵的开销。从Pentium
4，Intel
Xeon及P6处理器开始，intel在原有总线锁的基础上做了一个很有意义的优化：如果要访问的内存区域（area
of
memory）在lock前缀指令执行期间已经在处理器内部的缓存中被锁定（即包含该内存区域的缓存行当前处于独占或以修改状态），并且该内存区域被完全包含在单个缓存行（cache
line）中，那么处理器将直接执行该指令。由于在指令执行期间该缓存行会一直被锁定，其它处理器无法读/写该指令要访问的内存区域，因此能保证指令执行的原子性。这个操作过程叫做缓存锁定（cache
locking），缓存锁定将大大降低lock前缀指令的执行开销，但是当多处理器之间的竞争程度很高或者指令访问的内存地址未对齐时，仍然会锁住总线。

</div>

<div>

2、禁止该指令与之前和之后的读和写指令重排序。

</div>

<div>

3、把写缓冲区中的所有数据刷新到内存中。

</div>

<div>

\

</div>

<div>

并且。CAS会存在延迟本地调用，因为在SMP（对称多处理器）架构中，所有的CPU会共享一条系统总线（BUS），靠此总线连接主存。每个核都有自己的一级缓存，各核相对于BUS对称分布。

</div>

<div>

![](Java%20Concurrent%20CAS%E4%BD%BF%E7%94%A8&%E5%8E%9F%E7%90%86.resources/DFE7CF07-3902-4D7B-BC99-A3A3B41B7C75.png) 
 \

</div>

<div>

Core1和Core2可能会同时把主存中某个位置的值Load到自己的L1
Cache中，当Core1在自己的L1
Cache中修改这个位置的值时，会通过总线，使Core2中L1
Cache对应的值"失效"，而Core2一旦发现自己L1
Cache中的值失效（称为Cache命中缺失）则会通过总线从内存中加载该地址最新的值，大家通过总线的来回通信称为"Cache一致性流量"，因为总线被设计为固定的"通信能力"，如果Cache一致性流量过大，总线将成为瓶颈。而当Core1和Core2中的值再次一致时，称为"Cache一致性"，从这个层面来说，锁设计的终极目标便是减少Cache一致性流量。

</div>

<div>

而CAS恰好会导致Cache一致性流量，如果有很多线程都共享同一个对象，当某个Core
CAS成功时必然会引起总线风暴，这就是所谓的本地延迟。这算是CAS
一个缺点吧。

</div>

<div>

Java 中偏向锁就是为了消除CAS，降低Cache一致性流量，偏向锁另做介绍。

</div>

<div>

另外上面提到的缓存一致性是存在对应的协议支持的，现在通用的协议是MESI：（以下引自维基百科）

</div>

<div>

MESI协议是基于Invalidate的高速缓存一致性协议，是支持回写缓存的最常见协议之一。它也被称为伊利诺斯议定书。回写缓存可以节省大量的缓存写操作的带宽。回写缓存中总是存在一个脏状态，表明缓存中的数据与主内存中的数据不同。如果块驻留在另一个缓存中，则MESI协议要求缓存对丢失的缓存进行缓存传输。该协议减少了与MSI协议相关的主内存事务的数量，标志着性能的显著提高。mesi，表示cpu中一个缓存行的四个状态（Modified
(被修改，等待写回内存，已不可用【脏数据】),
Exclusive（正常，与内存一致）,
Shared（正常，与其他cpu共享的，修改后置为invalid）,Invalid（无效，必须从内存重新读））

</div>

<div>

说远了，这一块也是刚看到，一知半解，不敢过多说，不过看到一点，大家都说的CAS操作十分巧妙，但其实除某些场景除功能上的不足以外，还会存在一些底层的小问题。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>
