---
author: zouzq7@163.com
created: "2018-08-28 09:41:16 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent 线程封闭
updated: "2018-10-26 08:46:43 +0000"
---

<div>

并发能够帮助我们完成资源的高效利用，业务逻辑的解耦。但是同时并发也带来了一些问题，比如说数据的并发安全问题。很多情况下，我们是希望能够并行的做一些事情，但是资源最好不共享。

</div>

<div>

就现状而言，差不多有以下几种方式：

</div>

<div>

1、Ad-hoc 线程封闭

</div>

<div>

2、ThreadLocal

</div>

<div>

3、栈封闭

</div>

<div>

\

</div>

<div>

[Ad-hoc 线程封闭：] 

</div>

<div>

Ad-hoc
封闭，靠意念封闭。首先你需要经历过n多线程并发问题，然后对于自己及别人的当前及今后的代码了如指掌，能够完全预料到将来的发展。然后就有可能实现一个完全没有问题的ad-hoc封闭了，说的真，如果有这个能力还是干点别的吧，别封闭了。这个种方式完全依赖于实现者的控制，但是实际开发中基本不可用的一种方式。

</div>

<div>

\

</div>

<div>

[ThreadLocal：] 

</div>

<div>

可以理解为线程内变量的集中管理，线程之间这些变量不共享。

</div>

<div>

ThreadLocal
里维持了一个map，这个map以当前线程的threadlocal为id存放了关于这个线程的变量副本。在这个场景下很容易误解为ThreadLocal是为了解决共享问题而产生的一个安全并发访问的结构，其实不是这样的，它为每个线程保存一份仅自己可见的变量，ThreadLocal解决的核心问题是线程内部大量传递的参数，也就是说在当前线程所使用资源不共享的情况下（避免了共享带来的线程安全问题），减少线程内部参数传递的数量，使用ThreadLocal来维持或者管理线程内的变量。

</div>

<div>

具体来说，ThreadLocal
中存在一个叫做ThreadLocalMap的结构，数据就在这个结构中。然后ThreadLocalMap实际上是一个Entry
数组，每个Entry
维持一个弱引用的key（真正的key是Thread的ThreadLocal对象）和一个强引用的value（具体的变量集合）。

</div>

<div>

看一下源码：

</div>

<div>

[static class ] ThreadLocalMap {

</div>

<div>

\

</div>

<div>

[    ] [static
class ] Entry [extends
] WeakReference\<ThreadLocal\<?\>\> {

</div>

<div>

        [/\*\* The value associated with this ThreadLocal.
\*/] 

</div>

<div>

[        ] Object
[value] 

</div>

<div>

\

</div>

<div>

[       
] (ThreadLocal\<?\>
k[, ] Object v) {

</div>

<div>

            [super] (k)[; // key
值为弱引用] 

</div>

<div>

[            ] [value
] =
v[;] 

</div>

<div>

[        ] }

</div>

<div>

    }

</div>

<div>

这里弱引用的意义是保证不会内存泄漏不会发生。因为如果是强引用会出现这样的情况：当线程已经完成任务没有对应的调用了，但在这个map中仍然持有对于key的强引用，导致GC时无法对于key及value完成回收，导致内存泄漏，而当为弱引用时，在GC时会被对应的回收，那么key为null时，对应的value也是会被回收的。这里相当于完成一次依赖性的转移，GC回收的依据跟map中的key没有关系，完全依赖于外部的ThreadLocal引用。

</div>

<div>

虚线箭头代表弱引用，实线箭头代表强引用。除了弱引用策略外，set函数也会定期的清理无用的Entry。

</div>

<div>

![](Java%20Concurrent%20%E7%BA%BF%E7%A8%8B%E5%B0%81%E9%97%AD.resources/514791DE-4586-476B-AD65-BCE6AF31955E.png) 
 \

</div>

<div>

[public void
] ([T
] value) {

</div>

<div>

    Thread t =
Thread.[currentThread] 

</div>

<div>

[    ] ThreadLocalMap map =
getMap(t)[;] 

</div>

<div>

[    if ] (map !=
[null] )

</div>

<div>

        map.set([this, ] value)[; [//
注意一下这一行] 

</div>

<div>

[    else] 

</div>

<div>

[        ] createMap(t[,
] 

</div>

<div>

}

</div>

<div>

至于为什么使用ThreadLocal
作为key，怕是因为直接用线程id来作为ThreadLocalMap的key，无法区分放入ThreadLocalMap中的多个value。而使用ThreadLocal作为key就可以做到这一点，由于每一个ThreadLocal对象都可以由threadLocalHashCode属性唯一区分或者说每一个ThreadLocal对象都可以由这个对象的名字唯一区分（下面的例子），所以可以用不同的ThreadLocal作为key，区分不同的value，方便存取。

</div>

<div>

然后使用上，每次get前需要先set，如果想在get之前不需要调用set就能正常访问的话，必须重写initialValue()方法。

</div>

<div>

\

</div>

<div>

栈封闭：

</div>

<div>

JVM 中有这么几种内存结构：直接内存（NIO
能够直接操作一部分直接内存）、堆内存、栈内存。其中栈内存是线程私有的，而对内存是线程所共享的。而我们这里利用的就是栈信息是线程私有这一点去实现的。我们都很清楚对象是存放在堆上的，那么如何控制呢？我们知道引用及基础类型是存放于栈帧中的，既然对象不安全，而引用安全，那么我只需要这样就可以了，我们在方法内部创建一个对象，只能局部变量访问，只要不逸出，就能保证这个对象仅有创建它的线程访问。

</div>

<div>

![](Java%20Concurrent%20%E7%BA%BF%E7%A8%8B%E5%B0%81%E9%97%AD.resources/694E8244-79F6-48EB-8D67-7D50E05F97B5.png) 
 \

</div>

<div>

\

</div>

<div>

如何设计线程安全的类，这一点本来想单独写一篇的，但是发现已经存在总结的非常棒的了。

</div>

<div>

并发编程实战中说的挺好的：1、首先找出构成这个类的所有变量（如果是对象，依次向下找）2、找出约束状态变量的不可变条件。3、建立对象状态的并发访问策略。

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
