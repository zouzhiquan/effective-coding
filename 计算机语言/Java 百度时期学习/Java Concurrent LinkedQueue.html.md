---
author: zouzq7@163.com
created: "2018-08-27 12:09:17 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent LinkedQueue
updated: "2018-11-12 07:59:40 +0000"
---

<div>

首先实现线程安全的queue的方式很很多种，比如说方法全部加锁，再或者使用基于CAS的LinkedQueue。然后ConcurrentLinkedQueue是一个无界线程安全队列（当然也遵循先进先出）

</div>

<div>

使用方式上跟普通的queue
基本没有任何区别，只不过它是线程安全的，但注意以下几个函数：

</div>

<div>

1、Peek函数只取第一个元素，并不出队，poll函数才真的出队（poll函数如果函数为空则null）

</div>

<div>

[public
] [E
] [()          
{
] [return
] [;
] 

</div>

<div>

\

</div>

<div>

![](Java%20Concurrent%20LinkedQueue.resources/35E8CB82-B1F2-4795-8F4B-6C4848526D88.png) 
 \

</div>

<div>

2、size函数

</div>

<div>

跟list中维持一个长度元素不同的是，这里的queue需要遍历整个链表来完成对应的长度统计，时间花销可想而知，慎用。

</div>

<div>

![](Java%20Concurrent%20LinkedQueue.resources/962A699A-AD00-4D09-82FC-38221ED1C505.png) 
 \

</div>

<div>

\

</div>

<div>

在使用这类同步容器时需要注意的是，它能够保证单个操作的原子性但是像那种先判断后操作的典型的竞态条件还是存在问题的。

</div>

<div>

\

</div>

<div>

\

</div>
