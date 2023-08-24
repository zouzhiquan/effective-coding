---
author: zouzq7@163.com
created: "2018-08-02 14:18:44 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent CountDownLatch
updated: "2018-08-09 12:14:10 +0000"
---

<div>

CountDownLatch 用于使一组线程（1 or
n）等待一个外部任务的完成。很多人将它称为闭锁，可以理解为锁的就是那些线程，然后需要一个外部任务的完成来触发比锁打开。主要场景的话，等待资源加载完毕、检查接口是否正常
再执行。或者取到各种数据后再入库。还有经常用于唤醒大量而不是全部的线程去执行任务。

</div>

<div>

CountDownLatch
demo的代码量有点多，这个大家百度一下就ok了，这一篇主要说原理。

</div>

<div>

CountDownLatch
可以看为一个简单的计数器，等待的任务数量为n，如果任务都完成了（countDown）则代表整体ok。

</div>

<div>

看一下源码：

</div>

<div>

源码量非常小，可以理解为仅仅依赖于AQS实现了一个计数器，只不过这个计数器持有一堆等待线程。

</div>

<div>

首先里面包含一个Sync，想必看过前两篇文章的同学对于这个已经很容易理解了，就是一个AQS，然后这个Sync中持有的state就是count，用来显示等待完成的数量。当state为0时放行。通过CAS完成状态的更新，然后很显然CountDownLatch是一次性的，每次初始化，然后减小。

</div>

<div>

<div>

[package
] 

</div>

<div>

[import
] 

</div>

<div>

[public class ] CountDownLatch {

</div>

<div>

    [/\*\*] 

</div>

<div>

[     \* Synchronization control For
CountDownLatch.] 

</div>

<div>

[     \* Uses AQS state to represent
count.] 

</div>

<div>

[     \*/] 

</div>

<div>

[    ] [private static final
class ] Sync [extends
] AbstractQueuedSynchronizer {

</div>

<div>

        [private static final long
] [serialVersionUID
] =
[4982264981922014374L] 

</div>

<div>

\

</div>

<div>

[        ] ([int
] count) {

</div>

<div>

            setState(count)[;] 

</div>

<div>

[        ] }

</div>

<div>

\

</div>

<div>

        [int
] () {

</div>

<div>

            [return
] 

</div>

<div>

[        ] }

</div>

<div>

\

</div>

<div>

        [protected int
] ([int
] acquires) {

</div>

<div>

            [return ] (getState() ==
[0] :
-[1] 

</div>

<div>

[        ] }

</div>

<div>

\

</div>

<div>

        [protected boolean
] ([int
] releases) {

</div>

<div>

            [// Decrement count; signal when transition to
zero] 

</div>

<div>

[            ] [for
] ) {

</div>

<div>

                [int ] c =
getState()[;] 

</div>

<div>

[                if ] (c ==
[0] )

</div>

<div>

                    [return false;] 

</div>

<div>

[                int ] nextc = c -
[1] 

</div>

<div>

[                if ] (compareAndSetState(c[,
] nextc))

</div>

<div>

                    [return ] nextc ==
[0] 

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

    [private final ] Sync
[sync] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[    ] [public
] ([int
] count) {

</div>

<div>

        [if ] (count \<
[0] ) [throw new
] IllegalArgumentException([\"count \<
0\"] 

</div>

<div>

[        this] .[sync
] = [new
] 

</div>

<div>

[    ] }

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[    ] [public void
] () [throws
] InterruptedException {

</div>

<div>

       
[sync] 

</div>

<div>

[    ] }

</div>

<div>

\

</div>

<div>

[    ] [public boolean
] ([long
] TimeUnit
unit)

</div>

<div>

        [throws ] InterruptedException {

</div>

<div>

        [return
] [,
] 

</div>

<div>

[    ] }

</div>

<div>

\

</div>

<div>

[    ] [public void
] () {

</div>

<div>

       
[sync] 

</div>

<div>

[    ] }

</div>

<div>

\

</div>

<div>

[    ] [public long
] () {

</div>

<div>

        [return
] 

</div>

<div>

[    ] }

</div>

<div>

[    ] [public
] () {

</div>

<div>

        [return super] .toString() + [\"\[Count
= \" ] +
[sync] .getCount() +
[\"\]\"] 

</div>

<div>

[    ] }

</div>

<div>

}

</div>

<div>

\

</div>

</div>

<div>

\

</div>
