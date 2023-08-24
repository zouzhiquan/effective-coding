---
author: zouzq7@163.com
created: "2018-08-08 08:26:32 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent ReentrantLock（Java 10）
updated: "2018-11-12 08:05:49 +0000"
---

<div>

Reentrant是一种可重入锁，是一种递归无阻塞的同步机制。实现了和synchronized类似的同步策略。与synchronized配合使用的wait、notify、notifyall等函数，由Codition负责提供，这个后续另外会说。

</div>

<div>

一、使用

</div>

<div>

1、在使用上与synchronized差异的是
实现lock接口的reentrant需要手动的去lock和release，因为synchronized是JVM也就是Java语法层面实现的，而Lock是JDK里面实现的。相对于sychronized来说，我们使用时要比synchronized更加严谨，因为忘记释放锁非常容易导致死锁。建议选择在finally中进行锁的释放

</div>

<div>

2、lock实现的锁粒度可以控制更加小

</div>

<div>

3、Lock 实现能更支持更多高级的特性，比如说锁超时等。

</div>

<div>

4、因为是JDK实现，所以具有了更多特性的高级锁比如说：read lock、write
lock，并且支持我们自定义特殊的锁，这个虽然通常用不太到，但必要时是非常有用的。

</div>

<div>

demo：首先是使用synchronize，感受一下

</div>

<div>

[class ] Test1 {

</div>

<div>

    [private static volatile int
] [condition
] =
[0] 

</div>

<div>

[    private static final ] Object
[lock ] = [new
] 

</div>

<div>

\

</div>

<div>

[    public static void
] (String\[\]
args) [throws ] InterruptedException {

</div>

<div>

        Thread A = [new ] Thread([new
] Runnable() {

</div>

<div>

            [\@Override] 

</div>

<div>

[            ] [public void
] ()
{

</div>

<div>

                [synchronized
] )
{

</div>

<div>

                    [while
] (!([condition
] ==
[1] )) {

</div>

<div>

                        [try ] {

</div>

<div>

                           
[lock] 

</div>

<div>

[                        ] } [catch
] (InterruptedException e) {

</div>

<div>

                           
Thread.[currentThread] 

</div>

<div>

[                        ] }

</div>

<div>

                    }

</div>

<div>

                   
System.[out] .println([\"a
executed by
notify\"] 

</div>

<div>

[                ] }

</div>

<div>

            }

</div>

<div>

        })[;] 

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

[        ] [condition
] =
[1] 

</div>

<div>

[        synchronized
] )
{

</div>

<div>

           
[lock] 

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

然后是使用Lock

</div>

<div>

[import
] 

</div>

<div>

[import
] 

</div>

<div>

[import
] 

</div>

<div>

\

</div>

<div>

[class ] Test2 {

</div>

<div>

        [private static volatile int
] [condition
] =
[0] 

</div>

<div>

[        private static ] Lock [lock
] = [new
] 

</div>

<div>

[        private static ] Condition
[lockCondition
] =
[lock] 

</div>

<div>

\

</div>

<div>

[        public static void
] (String\[\]
args) [throws ] InterruptedException {

</div>

<div>

            Thread A = [new
] Thread([new
] Runnable() {

</div>

<div>

                [\@Override] 

</div>

<div>

[                ] [public void
] ()
{

</div>

<div>

                   
[lock] 

</div>

<div>

[                    try ] {

</div>

<div>

                        [while
] (!([condition
] ==
[1] )) {

</div>

<div>

                           
[lockCondition] 

</div>

<div>

[                        ] }

</div>

<div>

                    } [catch
] (InterruptedException e) {

</div>

<div>

                       
Thread.[currentThread] 

</div>

<div>

[                    ] } [finally
] {

</div>

<div>

                       
[lock] 

</div>

<div>

[                    ] }

</div>

<div>

                   
System.[out] .println([\"a
executed by
condition\"] 

</div>

<div>

[                ] }

</div>

<div>

            })[;] 

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

[            ] [condition
] =
[1] 

</div>

<div>

[           
] 

</div>

<div>

[            try ] {

</div>

<div>

               
[lockCondition] 

</div>

<div>

[            ] } [finally
] {

</div>

<div>

               
[lock] 

</div>

<div>

[            ] }

</div>

<div>

        }

</div>

<div>

}

</div>

<div>

\

</div>

<div>

二、原理

</div>

<div>

开始看源码：

</div>

<div>

首先可以看到ReentrantLOck实现了Lock和Serializable
接口，序列化不多说，Lock定义了是现在JDK中锁的规范，然后类中持有一个Sync对象，其中Sync是ReentrantLock的一个静态内部类，这是整个ReentrantLock的基础，Sync继承自AQS，换句话说AQS也就是ReentrantLock的实现基础。

</div>

<div>

[public class ] ReentrantLock
[implements ] Lock[,
] java.io.Serializable {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
[7373984872572414699L] 

</div>

<div>

[    ] [/\*\* Synchronizer providing
all implementation mechanics
\*/] 

</div>

<div>

[    ] [private
final ] Sync
[sync] 

</div>

<div>

\

</div>

<div>

[   
] 

</div>

<div>

[     \* Base of synchronization control for this lock.
Subclassed] 

</div>

<div>

[     \* into fair and nonfair versions below. Uses AQS state
to] 

</div>

<div>

[     \* represent the number of holds on the
lock.] 

</div>

<div>

[     \*/] 

</div>

<div>

[    ] [abstract
static class ] Sync [extends
] AbstractQueuedSynchronizer {

</div>

<div>

        [private static final long
] [serialVersionUID
] =
-[5179523762034025860L] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

然后往后看会得到另外几个静态内部类：FairSync、NonfairSync，他们均继承自Sync

</div>

<div>

这两个区别最大的操作是：

</div>

<div>

如果当前线程不是锁的占有者,则NonfairSync并不判断是否有等待队列,直接使用CAS去进行锁的占用;

</div>

<div>

如果当前线程不是锁的占有者,则FairSync则会判断当前是否有等待队列,如果有则将自己加到等待队列尾;

</div>

<div>

这其实就是公平锁&非公平锁的实现，默认非公平。

</div>

<div>

下面的代码中，我加了几行注释，大家注重关注下可重入及公平非公平是如何实现的。

</div>

<div>

\

</div>

<div>

[/\*\*] 

</div>

<div>

[ \* Sync object for non-fair
locks] 

</div>

<div>

[ \*/] 

</div>

<div>

[static final class ] NonfairSync
[extends ] Sync {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
[7316153563782823691L] 

</div>

<div>

[    protected final boolean
] ([int
] acquires) {

</div>

<div>

        [return
] 

</div>

<div>

[    ] }

</div>

<div>

}

</div>

<div>

它这里使用的是Sync里面的实现：

</div>

<div>

[abstract static class ] Sync [extends
] AbstractQueuedSynchronizer {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
-[5179523762034025860L] 

</div>

<div>

\

</div>

<div>

[   
] 

</div>

<div>

[     \* Performs non-fair tryLock.  tryAcquire is implemented
in] 

</div>

<div>

[     \* subclasses, but both need nonfair try for trylock
method.] 

</div>

<div>

[     \*/] 

</div>

<div>

[   
] 

</div>

<div>

[    ] [final boolean
] ([int
] acquires) {

</div>

<div>

        [final ] Thread current =
Thread.[currentThread] 

</div>

<div>

[        int ] c =
getState()[;] 

</div>

<div>

[        if ] (c ==
[0] ) {

</div>

<div>

            [if
] [,
] acquires)) {//注意一下这里

</div>

<div>

               
setExclusiveOwnerThread(current)[;] 

</div>

<div>

[                return true;] 

</div>

<div>

[            ] }

</div>

<div>

        }

</div>

<div>

[        else if ] (current ==
getExclusiveOwnerThread()) {//可重入的实现

</div>

<div>

            [int ] nextc = c +
acquires[;] 

</div>

<div>

[            if ] (nextc \<
[0] ) [//
overflow] 

</div>

<div>

[                ] [throw new
] Error([\"Maximum lock count
exceeded\"] 

</div>

<div>

[          
 ] 

</div>

<div>

[            return true;] 

</div>

<div>

[        ] }

</div>

<div>

        [return false;] 

</div>

<div>

}

</div>

<div>

然后是FairSync的实现

</div>

<div>

[/\*\*] 

</div>

<div>

[ \* Sync object for fair
locks] 

</div>

<div>

[ \*/] 

</div>

<div>

[static final class ] FairSync
[extends ] Sync {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
-[3000897897090466540L] 

</div>

<div>

[   
] 

</div>

<div>

[     \* Fair version of tryAcquire.  Don\'t grant access
unless] 

</div>

<div>

[     \* recursive call or no waiters or is
first.] 

</div>

<div>

[     \*/] 

</div>

<div>

[   
] 

</div>

<div>

[    ] [protected final boolean
] ([int
] acquires) {

</div>

<div>

        [final ] Thread current =
Thread.[currentThread] 

</div>

<div>

[        int ] c =
getState()[;] 

</div>

<div>

[        if ] (c ==
[0] ) {

</div>

<div>

            [if
] (!hasQueuedPredecessors() &&
//注意一下这一块

</div>

<div>

               
compareAndSetState([0] [,
] acquires)) {

</div>

<div>

               
setExclusiveOwnerThread(current)[;] 

</div>

<div>

[                return true;] 

</div>

<div>

[            ] }

</div>

<div>

        }

</div>

<div>

        [else if ] (current ==
getExclusiveOwnerThread()) {//可重入部分的实现

</div>

<div>

            [int ] nextc = c +
acquires[;] 

</div>

<div>

[            if ] (nextc \<
[0] )

</div>

<div>

                [throw new
] Error([\"Maximum lock count
exceeded\"] 

</div>

<div>

[           
] 

</div>

<div>

[            return true;] 

</div>

<div>

[        ] }

</div>

<div>

        [return false;] 

</div>

<div>

[    ] }

</div>

<div>

}

</div>

<div>

看到这里，大体已经能猜到具体实现了。ReentrantLock中维护着一个AQS，然后竞争锁的线程是在这里排队的，然后通过对应的CAS操作进行锁的争用。具体的实现参考AQS及CAS。

</div>

<div>

默认非公平：

</div>

<div>

[/\*\*] 

</div>

<div>

[ \* Creates an instance of
 [\@code
] 

</div>

<div>

[ \* This is equivalent to using
 [\@code
] 

</div>

<div>

[ \*/] 

</div>

<div>

[public
] ()
{

</div>

<div>

    [sync ] = [new
] 

</div>

<div>

}

</div>

<div>

\

</div>

<div>

可以很明显的看到底层实现几乎完全依赖于AQS，其实就是AQS包了一层罢了。

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

[public void
] ()
[throws ] InterruptedException {

</div>

<div>

   
[sync] 

</div>

<div>

}

</div>

<div>

[public boolean
] ()
{

</div>

<div>

    [return
] 

</div>

<div>

}

</div>

<div>

[public boolean
] ([long
] timeout[,
] TimeUnit unit)

</div>

<div>

        [throws ] InterruptedException
{

</div>

<div>

    [return
] [,
] 

</div>

<div>

}

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

[public int
] ()
{

</div>

<div>

    [return
] 

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

\

</div>

<div>

\

</div>

<div>

\

</div>
