---
altitude: 50.64455413818359
author: zouzq7@163.com
created: "2017-09-26 05:40:39 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03830629542771
longitude: 116.4110089824704
source: desktop.mac
title: JVM 《七 JVM 角度看对象》
updated: "2019-02-26 06:55:11 +0000"
---

<div>

作为一个Java 程序员，天天与我们交互的除了逻辑就是对象了，我们可以自己new一个，也可以注入一个，得到对象的方法儿有很多。

</div>

<div>

这个对象有自己的属性、方法。然后 我们就可以利用这些对象的行为来完成我们的逻辑了。这是对象站在我们的角度时的样子。那在JVM或者计算机看起来是什么样子呢。

</div>

<div>

\

</div>

<div>

一、

</div>

<div>

首先，这是个对象。JVM也知道这是个对象，会给它一个标示。

</div>

<div>

也就是所谓的高大上的mark
word&一些指针信息，说白了也就是存储一部分信息的标示头。

</div>

<div>

那这个mark中有什么呢？

</div>

<div>

一部分是 对象运行时的一部分数据
比如：hashCode、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳。

</div>

<div>

然后是 一些类型指针。这个是用来指向它所属的类的。

</div>

<div>

如果你现在正在学习Java 并发相关的内容，mark word
这部分的内容还是值得好好看一看的，出了Mark
Word的结构，还有如何基于这个东西来实现那些个高级锁。我们使用的的锁，为什么能锁呢，对象锁？
锁？这类的问题这一块都值得好好看看。

</div>

<div>

\

</div>

<div>

还有类对象挺特殊，Java 数组。

</div>

<div>

在普通的对象中，我们有一个记录对象大小的字段（显然对象产生时大小已经就是确定的了）。但是数组对象不确定啊，我们需要一个字段来标示该数组的元素个数～

</div>

<div>

\

</div>

<div>

二、

</div>

<div>

要说的对象的下一部分。

</div>

<div>

实例数据，就是这个对象中所包含的数据。没别的 就这样

</div>

<div>

\

</div>

<div>

三、

</div>

<div>

对齐填充

</div>

<div>

嗯，对齐填充

</div>

<div>

\

</div>

<div>

这样一个对象的结构就很清晰了

</div>

<div>

然后前面提到了对象大小的问题，我们都很清楚对象包含的其他对象，其实仅仅是存储了一个引用而已。那这个被包含的对象属于我们这个对象的大小计算吗？

</div>

<div>

这里说明一下，对象的大小是分为两种的： shallow size 、deep size

</div>

<div>

shallow size 是只算本对象内部内容的大小

</div>

<div>

deep size 是自身大小加上递归引用所有对象大小的总和

</div>

<div>

\

</div>

<div>

然后说起引用了，放两张老图，看看经典的样子

</div>

<div>

![](JVM%20%E3%80%8A%E4%B8%83%20JVM%20%E8%A7%92%E5%BA%A6%E7%9C%8B%E5%AF%B9%E8%B1%A1%E3%80%8B.resources/232926_WTnX_103999.png) 
 \

</div>

<div>

\

</div>

<div>

![](JVM%20%E3%80%8A%E4%B8%83%20JVM%20%E8%A7%92%E5%BA%A6%E7%9C%8B%E5%AF%B9%E8%B1%A1%E3%80%8B.resources/232935_FM5T_103999.png) 
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

<div>

这样在JVM中的对象已经描述的差不多了。

</div>

<div>

\

</div>

<div>

其实呢这个是以 hotspot
为例来阐述的，其他的JVM会有一定的偏差不过明白运作原理、举一反三才是最重要的吧。

</div>
