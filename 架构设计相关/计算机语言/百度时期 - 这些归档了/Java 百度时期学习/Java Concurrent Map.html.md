---
author: zouzq7@163.com
created: "2018-08-22 02:12:01 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent Map
updated: "2018-10-26 06:21:18 +0000"
---

<div>

HashMap是我们生产过程中使用较多的一个数据结构，平时非并发场景使用的HashMap，并发场景下使用的HashTable、ConcurrentHashMap。

</div>

<div>

表面的API看上去都基本是相同的，但不同的Map实现却差异较大，比如说1.6、1.17、1.8及以上版本中的HashMap、ConcurrentHashMap、远古的HashTable。在进行下面介绍之前默认大家已经理解红黑树、Hash计算等

</div>

<div>

按照顺序介绍：

</div>

<div>

\

</div>

<div>

**Java 1.6**

</div>

<div>

**HashMap**

</div>

<div>

默认负载因子为0.75，默认容量为16。也就是到达16\*0.75
时会出发resize操作。基于数组和链表实现，这算是HashMap的一种教科书里的实现结构了（通常大学课本中特别常见），当key为null
时会添加元素至0的位置。

</div>

<div>

缺点：new
HashMap时就开辟内存空间，在正式使用之前这段时间，造成了一定的内存浪费。下面是HashMap的一种实现结构

</div>

<div>

![](Java%20Concurrent%20Map.resources/7ECD7035-28FC-46CB-91D6-C61E0B2ED094.png) 
 \

</div>

<div>

**HashTable**

</div>

<div>

hashTbles的实现基本可以以HashMap结构为基础，要说差异的话就是每个方法都变成了sychronized，这种直接在每个方法上直接sychronized，怕不是当时当时临时上的策略了,[Collections] 

</div>

<div>

\

</div>

<div>

[**ConcurrentHashMap**] 

</div>

<div>

[1.6中的HashMap采用的是分段加锁的方式，可以简单理解为使用使用分段锁直接锁住某些段，然后减小争用的可能性（比HashTable稍微好一些），产生争用时取锁（通过reentrantlock保证并发的安全，直接继承的），来保证安全性。下面是大致的一种结构，锁的是Segment。有一个concurrency
level
默认是16，也就是segment的数量，这个不可以扩容。[负载因子同样是0.75，然后容量指定时是平均分配给每个桶的。] 

</div>

<div>

![](Java%20Concurrent%20Map.resources/E0696F48-F257-4B09-86CE-EADADA3AF747.png) 
 \

</div>

<div>

\

</div>

<div>

**Java 1.7**

</div>

<div>

HashTable的实现去查了下源码，一直到Java 10
都基本保持原始的样子。应该是停止更新了，所以以1.7 为准。

</div>

<div>

HashMap
相对于1.6中的，解决了上文提到的资源浪费问题，实现了简单的懒加载。

</div>

<div>

ConcurrentHashMap 相对于1.6 中基本没有发生变化。

</div>

<div>

\

</div>

<div>

**Java 8**

</div>

<div>

真正的变化其实发生在1.8中

</div>

<div>

先看HashMap：

</div>

<div>

优化点：解决碰撞过多的问题，理想情况下6和7中的实现碰撞是较少的，在底层结构看起来也就是链表的长度较短。但现实使用中并没法保证是在理想情况下或正常情况下工作的，所以经常出现链表长度很长，导致性能逐渐下降，并且有的还没开始使用，从一定角度上来说属于资源的分配不均，存在一定的浪费。针对这个问题，1.8中设定了一个阀值（8），当链表长度大于8时使用红黑树进行替换。也就是使用红黑树的方式减小碰撞所带来的代价（O(n)
到 O(logn)）

</div>

<div>

![](Java%20Concurrent%20Map.resources/CC8A9656-B912-4BF2-BE1E-88BFA213AE21.png) 
 \

</div>

<div>

然后Hash算法更改为：XORs，扩容算法更改为：ewThr\<\<1,然后懒加载的特性仍然保留，第一次put的时候才真正的new。

</div>

<div>

\

</div>

<div>

ConcurrentHashMap：

</div>

<div>

key&value都不允许为空，从1.8开始出现前置检查，并且舍弃concurrencylevel
改用动态分片。并且，这次可不是挤牙膏式更新，舍弃了之前Segment分段锁式设计，底层采用数组+链表+红黑树结构实现，采用CAS+sychronized实现并发安全。（结构基本同上面java
8中的HashMap）。

</div>

<div>

\

</div>

<div>

下面是putVal中的CAS+Sychronized的使用，putval也是整个ConcurrentHashMap中比较核心的，推荐详细去看一下，因为篇幅，只说里面的一两点。

</div>

<div>

![](Java%20Concurrent%20Map.resources/1FDD7D41-860F-465E-A390-0498DB38AB6D.png) 
 \

</div>

<div>

![](Java%20Concurrent%20Map.resources/5E35BAF7-37A3-4A44-A7D4-F686020FC8C2.png) 
 \

</div>

<div>

\

</div>

<div>

具体的hash算法：

</div>

<div>

![](Java%20Concurrent%20Map.resources/99207BEA-DF9D-4F41-8B04-D03A4A771BA0.png) 
 \

</div>

<div>

再然后取元素时，利用了类似于volatile特性的UnSafe.getObjectVolatile()，保证取到的为最新值。

</div>

<div>

\

</div>

<div>

1.8中的扩容方式：

</div>

<div>

当table的数量达到阀值sizeCtl时，会构建一个nextTable（大小为之前的两倍），然后把table的数据复制到nextTable中（不断的遍历原有的完成复制）。其中sizeCtl利用CAS（UnSafe.compareAndSetInt()
完成操作）可以简单看一下。1.8之前的跟之后的ConcurrentHashMap
这个过程是有所差异的，因为结构不同的关系，1.7及之前的扩容时不需要对整个map做rehash只需要对于segment做rehash就OK了

</div>

<div>

line：2432

</div>

<div>

[private final void
] [\[\]
] [,
] [\[\]
] [)
 

</div>

<div>

\

</div>

<div>

说一个经典的问题：

</div>

<div>

为什么要使用ConcurrentHashMap，单纯使用HashMap存在什么问题。

</div>

<div>

看上去ConcurrentHashMap相对于HashMap
也就是在一个数据操作的环节增加了锁操作变为CAS操作。这么做的意义是什么呢？

</div>

<div>

因为HashMap在并发执行put操作时会引起死循环，多线程可能会导致HashMap的Entry链表形成环形数据结构，查找时会陷入死循环。（两个线程同时扩容相撞了，导致环形链表的产生，所以[悲剧就出现了------Infinite
Loop] ）

</div>

<div>

\

</div>

<div>

关于rehash：

</div>

<div>

Java 6 存在rehash

</div>

<div>

Java 7 存在rehash

</div>

<div>

Java 8
可能会发生rehash，在扩充HashMap的时候，不需要像JDK1.7的实现那样重新计算hash，只需要看原来的hash值新增的那个bit是1还是0，是0的话索引没变，是1的话索引变成"原索引+oldCap"

</div>
