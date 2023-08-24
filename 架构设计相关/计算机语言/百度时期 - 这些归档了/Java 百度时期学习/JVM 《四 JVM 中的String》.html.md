---
altitude: 52.14627838134766
author: zouzq7@163.com
created: "2017-09-05 09:53:25 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.0384144884613
longitude: 116.410998659092
source: desktop.mac
title: JVM 《四 JVM 中的String》
updated: "2018-10-26 02:39:15 +0000"
---

<div>

String 这样的量，在我们的认知中是比较特别的。

</div>

<div>

\

</div>

<div>

其中String
是个对象，然后String也可以是个普通的字面量。在每代JDK中对String
的处理也是不同的，本篇的篇幅不大，仅仅觉着String有意思，所以拿出来说。

</div>

<div>

\

</div>

<div>

先说几个常见的问题

</div>

<div>

比如String tempStr="123"+"456"；

</div>

<div>

这句话在编译的过程中就已经是一个字符串了，俗称的编译优化？？？

</div>

<div>

\

</div>

<div>

String字面量跟String 对象的存放位置是不同的，一个是在堆上，一个在常量池。

</div>

<div>

所以有时候会产生这样的情况：String abc="123"；String acb="123"；String
bca="123"；......取到的是一个对象，但是当我们去new
一个对象时必定是产生一个对象的而不是先检查常量池。

</div>

<div>

\

</div>

<div>

除此之外还有一个方法叫做intern
这个是一个native方法，作用很简单，检查常量池中是否有该字符串量，若没有向常量池中复制一份。

</div>

<div>

JDK API文档中对intern()方法的描述是：

</div>

<div>

返回字符串对象的规范化表示形式。

</div>

<div>

一个初始为空的字符串池，它由类 String 私有地维护。

</div>

<div>

当调用 intern 方法时，如果池已经包含一个等于此 String 对象的字符串（用
equals(Object) 方法确定），则返回池中的字符串。否则，将此 String
对象添加到池中，并返回此 String 对象的引用。

</div>

<div>

它遵循以下规则：对于任意两个字符串 s 和 t，当且仅当 s.equals(t) 为 true
时，s.intern() == t.intern() 才为 true。

</div>

<div>

所有字面值字符串和字符串赋值常量表达式都使用 intern 方法进行操作。

</div>

<div>

\

</div>

<div>

然后来回说这个常量池，感觉这么说太抽象了，其实说白了就是一个方便查找的存放常量的池子，结构类似于HashMap。

</div>

<div>

这一个很简洁，就这么多～

</div>

<div>

其实跟JVM 并没什么关系，就是想说说

</div>

<div>

\

</div>

<div>

// todo 合并Java 版中的String

</div>
