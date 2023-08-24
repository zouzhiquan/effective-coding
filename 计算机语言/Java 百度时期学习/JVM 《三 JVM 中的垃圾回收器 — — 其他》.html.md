---
altitude: 55.04395294189453
author: zouzq7@163.com
created: "2017-09-05 09:33:08 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.69514765638159
longitude: 119.2477933640145
source: desktop.mac
title: JVM 《三 JVM 中的垃圾回收器 --- --- 其他》
updated: "2017-09-05 09:53:12 +0000"
---

<div>

放一张很古老的图，大家在N多博客都看到的图。

</div>

<div>

![](JVM%20%E3%80%8A%E4%B8%89%20JVM%20%E4%B8%AD%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8%20%E2%80%94%20%E2%80%94%20%E5%85%B6%E4%BB%96%E3%80%8B.resources/576557-20170324103246533-546487373.png) 
 

</div>

<div>

\

</div>

<div>

除去上一篇说的CMS，剩下的都是这几类了，然后根据Old or New 自行分配就好了

</div>

<div>

串行垃圾回收器（Serial Garbage Collector）

</div>

<div>

并行垃圾回收器（Parallel Garbage Collector）

</div>

<div>

\

</div>

<div>

并不是说老的收集器就一无是处，在单线程环境下串行垃圾回收还是作用很强大的，或者说没得选？然后再就是新生代老生代的搭配问题了，这也是古老的问题了。

</div>

<div>

\

</div>

<div>

这两篇结束之后，基本上垃圾回收器这一部分就说完了，最后说明一下如何指定回收器吧

</div>

<div>

-XX:+UseG1GC G1垃圾回收器

</div>

<div>

-XX:ParallelCMSThreads= 并发标记扫描垃圾回收器 =后面为使用的线程数量

</div>

<div>

-XX:+UseSerialGC 串行垃圾回收器

</div>

<div>

-XX:+UseParallelGC 并行垃圾回收器

</div>

<div>

-XX:+UseConcMarkSweepGC 并发标记扫描垃圾回收器

</div>

:::  
\
:::

:::  
\
:::
