---
author: zouzq7@163.com
created: "2018-08-08 10:11:47 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent ReentrantReadWriteLock（Java 10）
updated: "2018-08-08 15:06:22 +0000"
---

<div>

首先明确一点，ReentrantReadWriteLock
指的并不是一个锁，而是管理着读锁&写锁这样的一组锁，读锁是可共享的（shared），写锁是独占的。这么设计是有原因的，读操作之前互不影响，而写操作需要确保对于读操作是完整且结果可见的。其中ReentrantReadWriteLock实现上跟ReentrantLock类似，可选择公平&非公平，可重入等，里面持有的readlock和writelock都持有继承自AQS的Sync，依赖于AQS实现对应的功能。

</div>

<div>

[public class ] ReentrantReadWriteLock

</div>

<div>

        [implements ] ReadWriteLock[,
] java.io.Serializable {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
-[6992448646407690164L] 

</div>

<div>

[    ] [/\*\* Inner class providing
readlock \*/] 

</div>

<div>

[    ] [private
final
] ReentrantReadWriteLock.ReadLock
[readerLock] 

</div>

<div>

[    ] [/\*\* Inner class providing
writelock \*/] 

</div>

<div>

[    ] [private
final
] ReentrantReadWriteLock.WriteLock
[writerLock] 

</div>

<div>

[    ] [/\*\* Performs all
synchronization mechanics
\*/] 

</div>

<div>

[    ] [final
] Sync
[sync] 

</div>

<div>

\

</div>

<div>

一、使用

</div>

<div>

使用上，我们可以定义一个ReentrantLock来获取对应的ReadLock及WriteLock完成对应的功能，下面是一个简单的demo，可以执行一下，看一下具体的效果。

</div>

<div>

[public class ] ReadWriteLockDemo {

</div>

<div>

    [static ] SimpleDateFormat [sdf
] = [new
] SimpleDateFormat([\"yyyy-MM-dd
HH:mm:ss.SSS\"] 

</div>

<div>

[    public static void
] (String\[\]
args) {

</div>

<div>

        Data data = [new
] 

</div>

<div>

[        ] Worker t1 = [new
] Worker(data[,
false] 

</div>

<div>

[       
] 

</div>

<div>

[        ] Worker t2 = [new
] Worker(data[,
true] 

</div>

<div>

[       
] 

</div>

<div>

[        ] Worker t3 = [new
] Worker(data[,
true] 

</div>

<div>

[       
] 

</div>

<div>

[       
] 

</div>

<div>

[       
] 

</div>

<div>

[       
] 

</div>

<div>

[    ] }

</div>

<div>

    [static class ] Worker [extends
] Thread {

</div>

<div>

        Data
[data] 

</div>

<div>

[        ] Boolean
[read] 

</div>

<div>

[        public
] (Data
data[, ] Boolean read) {

</div>

<div>

            [this] .[data
] =
data[;] 

</div>

<div>

[            this] .[read
] =
read[;] 

</div>

<div>

[        ] }

</div>

<div>

        [public void
] ()
{

</div>

<div>

            [if
] )

</div>

<div>

                [data] .read()[;
else] 

</div>

<div>

[               
] 

</div>

<div>

[        ] }

</div>

<div>

    }

</div>

<div>

    [static class ] Data {

</div>

<div>

        ReadWriteLock [lock ] = [new
] 

</div>

<div>

[        ] Lock [read
] =
[lock] 

</div>

<div>

[        ] Lock [write
] =
[lock] 

</div>

<div>

[        public void
] ()
{

</div>

<div>

            [try ] {

</div>

<div>

               
Thread.[sleep] 

</div>

<div>

[               
] 

</div>

<div>

[            ] }

</div>

<div>

            [catch ] (Exception e) {

</div>

<div>

            }

</div>

<div>

           
[write] 

</div>

<div>

[           
] () +
[\" write:begin \"] 

</div>

<div>

[                    ] +
[sdf] .format([new
] 

</div>

<div>

[            try ] {

</div>

<div>

               
Thread.[sleep] 

</div>

<div>

[               
] 

</div>

<div>

[            ] }

</div>

<div>

            [catch ] (Exception e) {

</div>

<div>

            }

</div>

<div>

            [finally ] {

</div>

<div>

               
System.[out] () +
[\" write:end \"] 

</div>

<div>

[                        ] +
[sdf] .format([new
] 

</div>

<div>

[               
] 

</div>

<div>

[            ] }

</div>

<div>

        }

</div>

<div>

        [public int
] ()
{

</div>

<div>

           
[read] 

</div>

<div>

[           
] ()+
[\" read :begin \"] 

</div>

<div>

[                    ] +
[sdf] .format([new
] 

</div>

<div>

[            try ] {

</div>

<div>

               
Thread.[sleep] 

</div>

<div>

[               
] 

</div>

<div>

[            ] }

</div>

<div>

            [catch ] (Exception e) {

</div>

<div>

            }

</div>

<div>

            [finally ] {

</div>

<div>

               
System.[out] () +
[\" read :end \"] 

</div>

<div>

[                        ] +
[sdf] .format([new
] 

</div>

<div>

[               
] 

</div>

<div>

[            ] }

</div>

<div>

            [return
] 

</div>

<div>

[        ] }

</div>

<div>

    }

</div>

<div>

}

</div>

<div>

说原理之前先说结论：

</div>

<div>

取锁时：

</div>

<div>

1、如果当前没有写锁或读锁时，第一个获取锁的线程都会成功。

</div>

<div>

2、如果当前已经有了读锁，那么这时获取写锁将失败，获取读锁有可能成功也有可能失败。

</div>

<div>

3、如果当前已经有了写锁，那么这时获取读锁或写锁，如果线程相同（可重入），那么成功

</div>

<div>

释放：

</div>

<div>

1、如果当前是写锁被占有了，只有当写锁的标示数据降为0时才认为释放成功，否则失败。因为只要有写锁，那么除了占有写锁的那个线程，其他线程即不可以获得锁。

</div>

<div>

2、如果当前是读锁被占有了，那么只有在写锁的个数为0时才认为释放成功。

</div>

<div>

\

</div>

<div>

二、原理

</div>

<div>

与Reentrant相同的实现部分不做介绍，主要说不同之处：

</div>

<div>

读写锁里面存在这样几个静态内部类：

</div>

<div>

Sync entends AQS（内含：ThreadLocalHoldCounter extends
ThreadLocal、HoldCounter）、FairLock extends Sync、NonFairLock extends
Sync、ReadLock、WriteLoc、（其中ReadLock、WriteLock
分别持有一个Sync）在Sync中维护了Read和Write的使用之间的关系，并且Read、Write全部依赖于Sync中的逻辑，内部类中并没有具体的逻辑实现，所以说看整个ReentrantReadWriteLock
看Sync的核心实现就OK了。

</div>

<div>

Sync：

</div>

<div>

内部state分为两个无符号的short，读锁锁定的高16位，低16位为写锁锁定，以这个为基础维护读锁及写锁之间的关系：

</div>

<div>

并且内部维持这些静态属性：

</div>

<div>

[static final int ] [SHARED_SHIFT  
] =
[16] 

</div>

<div>

[static final int ] [SHARED_UNIT   
] = ([1
] \<\<
[SHARED_SHIFT] 

</div>

<div>

[static final int ] [MAX_COUNT     
] = ([1
] \<\<
[SHARED_SHIFT] ) -
[1] 

</div>

<div>

[static final int ] [EXCLUSIVE_MASK
] = ([1
] \<\<
[SHARED_SHIFT] ) -
[1] 

</div>

<div>

WriteLock：

</div>

<div>

state变量的低16位，当state低16位为0，表示当前写锁没有被占有，反之表示写锁被某个写线程占有。

</div>

<div>

占用条件&过程

</div>

<div>

1、读锁未被占用(state高16位为0) ，写锁未被占用（state低16位为0）或
占用写锁的线程是当前线程

</div>

<div>

2、writerShouldBlock()方法返回false,即不阻塞写线程

</div>

<div>

3、写锁占有量小于最大值(2\^16 -1)，否则抛出Error(\"Maximum lock count
exceeded\")

</div>

<div>

4、通过CAS竞争将写锁状态+1(将state低16位同步+1)

</div>

<div>

[protected final boolean
] ([int
] acquires) {

</div>

<div>

[    ] Thread current =
Thread.[currentThread] 

</div>

<div>

[    int ] c =
getState()[;] 

</div>

<div>

[    int ] w =
[exclusiveCount] 

</div>

<div>

[    if ] (c !=
[0] ) {

</div>

<div>

        [// (Note: if c != 0 and w == 0 then shared count !=
0)] 

</div>

<div>

[        ] [if
] (w == [0
] \|\| current !=
getExclusiveOwnerThread())

</div>

<div>

            [return false;] 

</div>

<div>

[        if ] (w +
[exclusiveCount] (acquires) \>
[MAX_COUNT] )

</div>

<div>

            [throw new
] Error([\"Maximum lock count
exceeded\"] 

</div>

<div>

[        ] [// Reentrant
acquire] 

</div>

<div>

[        ] setState(c +
acquires)[;] 

</div>

<div>

[        return true;] 

</div>

<div>

[    ] }

</div>

<div>

    [if ] (writerShouldBlock() \|\|

</div>

<div>

        !compareAndSetState(c[, ] c +
acquires))

</div>

<div>

        [return false;] 

</div>

<div>

[   
] 

</div>

<div>

[    return true;] 

</div>

<div>

}

</div>

<div>

\

</div>

<div>

释放过程：

</div>

<div>

写锁的释放过程是state低16位同步递减为0的过程，当state的高16位都为0表示写锁释放完毕，唤醒head节点下一个SIGNAL状态的节点的线程。如果该写锁占有线程未释放写锁前还占用了读锁，那么写锁释放后该线程就完全转换成了读锁的持有线程。

</div>

<div>

调用的AQS 原生方法：

</div>

<div>

[public void
] ()
{

</div>

<div>

   
[sync] 

</div>

<div>

}

</div>

<div>

\

</div>

<div>

\

</div>

<div>

ReadLock：

</div>

<div>

当state的高16位等于0，表示当前读锁未被占有；当state的高16位大于0，表示当前读锁可能被一个或多个线程占有，多于一个占有读锁的线程，允许重入。

</div>

<div>

占用条件&过程：

</div>

<div>

1、当前的写锁未被占有(state低16位为0) 或者当前线程是写锁占有的线程

</div>

<div>

2、readerShouldBlock()方法返回false

</div>

<div>

3、当前读锁占有量小于最大值(2\^16 -1)

</div>

<div>

4、成功通过CAS操作将读锁占有量+1(AQS的state高16位同步加1)

</div>

<div>

[protected final int
] ([int
] unused) {

</div>

<div>

[    ] Thread current =
Thread.[currentThread] 

</div>

<div>

[    int ] c =
getState()[;] 

</div>

<div>

[    if
] (c)
!= [0 ] &&

</div>

<div>

        getExclusiveOwnerThread() != current)

</div>

<div>

        [return
] 

</div>

<div>

[    int ] r =
[sharedCount] 

</div>

<div>

[    if ] (!readerShouldBlock() &&

</div>

<div>

        r \< [MAX_COUNT
] &&

</div>

<div>

        compareAndSetState(c[, ] c +
[SHARED_UNIT] ))
{

</div>

<div>

        [if ] (r ==
[0] ) {

</div>

<div>

            [firstReader ] =
current[;] 

</div>

<div>

[            ] [firstReaderHoldCount
] =
[1] 

</div>

<div>

[        ] } [else if
] ([firstReader
] == current) {

</div>

<div>

           
[firstReaderHoldCount] 

</div>

<div>

[        ] } [else
] {

</div>

<div>

            HoldCounter rh =
[cachedHoldCounter] 

</div>

<div>

[            if ] (rh == [null
] \|\|

</div>

<div>

                rh.[tid ] !=
LockSupport.[getThreadId] (current))

</div>

<div>

                [cachedHoldCounter
] = rh =
[readHolds] 

</div>

<div>

[            else if ] (rh.[count
] ==
[0] )

</div>

<div>

               
[readHolds] 

</div>

<div>

[           
] 

</div>

<div>

[        ] }

</div>

<div>

        [return
] 

</div>

<div>

[    ] }

</div>

<div>

    [return
] 

</div>

<div>

}

</div>

<div>

如果逻辑结果小于0，表示当前无条件获得，则将该线程加入阻塞队列（[do] ）

</div>

<div>

\

</div>

<div>

释放过程：

</div>

<div>

读锁的释放过程即AQS的state高16位同步递减为0的过程，当state的高16位都为0表示读锁释放完毕，如果此时写锁状态为0（即该读锁不是写锁降级来的），唤醒head节点下一个SIGNAL状态的节点的线程。如果读锁的占有数不为0，表示读锁未完全释放。或者写锁的占有数不为0，表示释放的读锁是写锁降级来的。

</div>

<div>

[public void
] ()
{

</div>

<div>

   
[sync] 

</div>

<div>

}

</div>

<div>

\

</div>

<div>

看完ReentrantLock&ReentrantReadWriteLock，是不是发现满满的都是CAS和for
;;，这就是非阻塞的一种实现原理，有利有弊吧。

</div>

<div>

\

</div>
