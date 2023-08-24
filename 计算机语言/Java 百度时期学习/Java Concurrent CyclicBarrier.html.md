---
author: zouzq7@163.com
created: "2018-08-09 12:16:19 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent CyclicBarrier
updated: "2018-10-26 06:41:18 +0000"
---

<div>

CyclicBarrier 是JUC 所提供的比较好用且应用面十分广泛的一个并发工具。

</div>

<div>

CyclicBarrier 字面意思：循环
屏障，也就是一种循环可使用的同步屏障，可以一组线程等待都完成的时候放行。和CountDownLatch对比来看，就是CountDownLatch等待的是外部事件，而CyclicBarrier等待一组线程。虽然看上去功能是相似的，但是实现和使用上都存在一定的差异。

</div>

<div>

先看一下使用的demo

</div>

<div>

构造函数：

</div>

<div>

CyclicBarrier(int parties)

</div>

<div>

参数parties表示需要等待的线程数量。

</div>

<div>

CyclicBarrier(int parties, Runnable barrierAction)

</div>

<div>

barrierAction表示放行时优先触发的事件。

</div>

<div>

[import
] 

</div>

<div>

[public class ] CyclicBarrierTest {

</div>

<div>

    CyclicBarrier [c ] = [new
] [,
new ] Runnable() {

</div>

<div>

        [\@Override] 

</div>

<div>

[        ] [public void
] ()
{

</div>

<div>

           
System.[out] .println([\"hello
EffectiveCoding\"] 

</div>

<div>

[        ] }

</div>

<div>

    })[;] 

</div>

<div>

[    private static
] CyclicBarrierTest [cyclicBarrierTest
] = [new
] 

</div>

<div>

\

</div>

<div>

[    public ] CyclicBarrier
[getC] () {

</div>

<div>

        [return
] 

</div>

<div>

[    ] }

</div>

<div>

\

</div>

<div>

    [public void
] ()
{

</div>

<div>

        [new ] Thread([new
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

                [try ] {

</div>

<div>

                   
System.[out] .println([\"1
到达\"] 

</div>

<div>

[                   
] 

</div>

<div>

[                   
] .println([\"1
已放行\"] 

</div>

<div>

[                ] } [catch
] (Exception e) {

</div>

<div>

                }

</div>

<div>

            }

</div>

<div>

        }).start()[;] 

</div>

<div>

[        try ] {

</div>

<div>

           
System.[out] .println([\"2
到达\"] 

</div>

<div>

[           
] 

</div>

<div>

[        ] } [catch
] (Exception e) {

</div>

<div>

        }

</div>

<div>

       
System.[out] .println([\"2
已放行\"] 

</div>

<div>

[       
] 

</div>

<div>

[    ] }

</div>

<div>

\

</div>

<div>

    [public static void
] (String\[\]
args) {

</div>

<div>

       
[cyclicBarrierTest] 

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

}

</div>

<div>

如果等待数量为3的话，则导致无法放行，因为并没有第三个线程到达。

</div>

<div>

\

</div>

<div>

然后看一下实现：

</div>

<div>

\

</div>

<div>

[private final ] [ReentrantLock
] lock [= new
] 

</div>

<div>

\

</div>

<div>

[private final ] [Condition
] trip [=
] 

</div>

<div>

\

</div>

<div>

[private final int
] 

</div>

<div>

\

</div>

<div>

[private final ] [Runnable
] 

</div>

<div>

\

</div>

<div>

[private ] [Generation
] generation [= new
] 

</div>

<div>

\

</div>

<div>

[private int
] 

</div>

<div>

\

</div>

<div>

首先CyclicBarrier以Reentrant为核心实现，parties为等待数量，Condition来完成一个具体的条件队列（其中使用await方法让线程在统一条件队列等待，使用signalAll方法唤醒通过这一条件的等待线程），这里的Condition的实现类AQS中的内部类ConditionObject。

</div>

<div>

*这里简单的说一下Condition：*

</div>

<div>

*Object中与同步锁synchronized相呼应的唤醒和阻塞的方法：wait、notify、notifyAll
方法，当我们使用JDK中的锁的时候也是依赖于这类操作的，而这些操作依赖于Condition实现，下面是Condition的API（相对于wait等方法更加精确了）：*

</div>

<div>

*await()：使当前线程在接到信号或被中断之前一直处于等待状态。*

</div>

<div>

*boolean await(long time, TimeUnit unit)：带有timout的await*

</div>

<div>

*void
awaitUninterruptibly()：使当前线程在接到信号之前一直处于等待状态。*

</div>

<div>

*boolean awaitUntil(Date
deadline)：使当前线程在接到信号、被中断或到达指定最后期限之前一直处于等待状态。*

</div>

<div>

*void signal()：唤醒一个等待线程。*

</div>

<div>

*void signalAll()：唤醒所有等待线程。*

</div>

<div>

*回到正文*

</div>

<div>

barrierCommand
就是实例中释放时触发动作（可以理解为是一个回调操作），generation维护了一个boolean类型的变量，count可以理解为一个计数器，每当await被调用count就会减一，到0时释放。如何hold所有需要等待的线程，Cyclicbarrier使用的Reentrantlock
来完成显示的锁的实现，

</div>

<div>

下面是dowait里面的一点显示锁的使用细节：

</div>

<div>

[final ] [ReentrantLock
] lock [=
this.] 

</div>

<div>

lock[.] 

</div>

<div>

[try] [
 

</div>

<div>

[    ] [final
] [Generation
] g [=
] 

</div>

<div>

[}] 

</div>

<div>

[\...] 

</div>

<div>

[finally] [
 

</div>

<div>

[   
] 

</div>

<div>

[}] 

</div>

<div>

\

</div>

<div>

[看一下上面已经使用到的几个函数：] 

</div>

<div>

构造函数：

</div>

<div>

这里不放代码都是根据所需要的功能对于前面展示的几个变量进行初始化。

</div>

<div>

[Reset函数就是将cyclicbarrier还原到初始状态] 

</div>

<div>

[public void
] [()
 

</div>

<div>

[    ] [final
] [ReentrantLock
] [lock
] [=
this.] 

</div>

<div>

[   
] 

</div>

<div>

[    try] [
 

</div>

<div>

[       
] [  
// break the current generation] 

</div>

<div>

[       
] [
// start a new generation] 

</div>

<div>

[    } ] [finally
] 

</div>

<div>

[       
] 

</div>

<div>

[    }] 

</div>

<div>

\

</div>

<div>

然后是await函数，可以明显的看到全部依赖于dowait方法

</div>

<div>

[public int
] [long
] [,
] [TimeUnit
] 

</div>

<div>

[    ] [throws
] 

</div>

<div>

[          
] 

</div>

<div>

[          
] [
 

</div>

<div>

[    ] [return
] [true,
] 

</div>

<div>

[}] 

</div>

<div>

[public int
] [()
] [throws
] [,
] [BrokenBarrierException
] 

</div>

<div>

[    ] [try
] 

</div>

<div>

[        ] [return
] [false,
] 

</div>

<div>

[   
}] [catch
] [TimeoutException
] [)
 

</div>

<div>

[        ] [throw new
] [
// cannot happen] 

</div>

<div>

[    }] 

</div>

<div>

[}] 

</div>

<div>

下面开始最核心的dowait函数：

</div>

<div>

先看这一段，首先是完成了当前线程及cyclicbarrier状态的判断，然后对于count进行操作，如果已经计数为零那么出发对应的事件，最后打破栅栏，完成所有线程的唤醒。可以看到任务唤醒的时机是在其他线程之前的，这一点使用的时候是需要注意下的。

</div>

<div>

[final ] [ReentrantLock
] lock [=
this.] 

</div>

<div>

lock[.] 

</div>

<div>

[try] [
 

</div>

<div>

[    ] [final
] [Generation
] g [=
] 

</div>

<div>

\

</div>

<div>

[    if
] 

</div>

<div>

[        ] [throw new
] 

</div>

<div>

\

</div>

<div>

[    if
] interrupted[())
 

</div>

<div>

[       
] 

</div>

<div>

[        throw new
] 

</div>

<div>

[    }] 

</div>

<div>

\

</div>

<div>

[    ] [int
] index [=
\--] 

</div>

<div>

[    if
] index
[==
] [)
 

</div>

<div>

[        ] [boolean
] ranAction [=
false;] 

</div>

<div>

[        try
] 

</div>

<div>

[            ] [final
] [Runnable
] command [=
] 

</div>

<div>

[            if
] command
[!=
null] 

</div>

<div>

[               
] 

</div>

<div>

[            ] ranAction [=
true;] 

</div>

<div>

[           
] 

</div>

<div>

[            return
] 

</div>

<div>

[     ] [  
}] [
 

</div>

<div>

[            ] [if
] 

</div>

<div>

[               
] 

</div>

<div>

[        }] 

</div>

<div>

[    }] 

</div>

<div>

然后是后半段代码：当某个线程执行完前半部分之后，count并没有到达0时，那么开始陷入空转检查，符合条件或者到达timeout

</div>

<div>

当到达对应条件时完成释放。

</div>

<div>

[// loop until tripped, broken, interrupted, or timed
out] 

</div>

<div>

[   
] [
(] [)
 

</div>

<div>

[       
] [
 

</div>

<div>

[            ] [if
] 

</div>

<div>

[               
] 

</div>

<div>

[            else if] [
(] [nanos
] [\>
] 

</div>

<div>

[                ] [nanos
] [=
] 

</div>

<div>

[   ] [     }
] [
(] [InterruptedException
] [)
 

</div>

<div>

[           
] [
(] g [==
] generation [&& !
] broken[)
 

</div>

<div>

[               
] 

</div>

<div>

[                throw
] 

</div>

<div>

[           
}] [
 

</div>

<div>

[                // We\'re about to finish waiting even if we had
not] 

</div>

<div>

[                // been interrupted, so this interrupt is deemed
to] 

</div>

<div>

[                // \"belong\" to subsequent
execution.] 

</div>

<div>

[               
] 

</div>

<div>

[            }] 

</div>

<div>

[        }] 

</div>

<div>

\

</div>

<div>

[        ] [if
] 

</div>

<div>

[            ] [throw new
] 

</div>

<div>

\

</div>

<div>

[        if
] g
[!=
] 

</div>

<div>

[            ] [return
] 

</div>

<div>

\

</div>

<div>

[        if] [
(] [timed
] [&&
] [nanos
] [\<=
] [)
 

</div>

<div>

[           
] 

</div>

<div>

[            throw new
] 

</div>

<div>

[        }] 

</div>

<div>

[    }] 

</div>

<div>

[}
] [
 

</div>

<div>

[   
] 

</div>

<div>

[}] 

</div>

<div>

[核心的差不多就这么多，主要倾向于原理解读，具体的细节性使用问题请参照api。] 

</div>
