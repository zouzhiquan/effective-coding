---
author: zouzq7@163.com
created: "2018-12-11 10:07:07 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis string之SDS 源码分析 1
updated: "2018-12-11 12:17:34 +0000"
---

<div>

string是Redis中最经常使用的一种结构，与c语言中的原生字符串不太一样，Redis
使用的是一种叫做SDS的结构，simple Dynamic string
粗暴点就是简单动态字符串，跟字面意思一样，sds能够动态的自己增加空间，扩容是无需使用者来操作的（相对于c
中的字符串）。

</div>

<div>

Redis
中sds有两种版本，一种是3.2之前的，这个也是那本《Redis设计与实现》所说的方式，但是就现状来说实效性已经没有那么强了，大家注意区分。下面简单的来看一下3.2
版本之前的：

</div>

<div>

这里以3.0.0版本为例（旧版本）：src/sds.h L：41

</div>

<div>

![](Redis%20string%E4%B9%8BSDS%20%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90%201.resources/075B6FDE-E72B-4A9C-8FA2-D88352D21E0A.png) 
 

</div>

<div>

然后再来看4.0.0 之后的版本（称为新版本）：

</div>

<div>

首先新版的根据不同的长度的字符串定义了不同的结构，分别为：sdshdr5、sdshdr8、sdshdr16、sdshdr32、sdshdr64。

</div>

<div>

看一下里面的几个字段：src/sds.h L：42

</div>

<div>

flags 用3bit的长度来标注类型，然后5bit尚未使用

</div>

<div>

len 指的是字符串的长度

</div>

<div>

alloc 指的是已分配的总长度

</div>

<div>

buf\[ \]指的是就是一个普通的字符串数组，用来实际存储的

</div>

<div>

![](Redis%20string%E4%B9%8BSDS%20%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90%201.resources/1988CA5F-EB3A-4F65-9DFB-0C516E5D1A2E.png) 
 

</div>

<div>

对比而言，新版本对于旧版本对于不同长度的字符串做了优化，选取不同的数据类型uint8_t、uint16_t、uint32_t
等来表示长度，上面\_\_attribute\_\_((\_\_packed\_\_))就是用来告诉编译器取消字节对齐的，所以说结构体的大小其实就是按照结构体成员实际大小相加得到的。

</div>

<div>

\

</div>

<div>

那Redis 中设计的这个sds 有什么优势和缺点呢？

</div>

<div>

它定义的是一个typedef char \*sds，sdsnew的时候实际上返回的是一个char
\*类型的指针，指向字符串的开始位置，所以说

</div>

<div>

优点：

</div>

<div>

1、我们可以把sds传递给任何使用char \*
作为参数的函数（包括一些库函数，而不用通过结构体获取地址再传递（相对于我们自定义的string））。

</div>

<div>

2、可以直接访问单个字符

</div>

<div>

3、分配的空间自制连续，对于高速缓存的命中率更加友好。一次连续分配（Header +
String + Null），对于一个sds它的各个部分总是内存连续的。

</div>

<div>

\

</div>

<div>

缺点：

</div>

<div>

1、sds
经常即是参数也是返回值，所以内部发生了什么是否重新分了空间等我们都是不知道的。

</div>

<div>

2、sds
通常来说会在程序的各个位置被共享，所以我们需要在修改字符串的时候，修改所有的应用的地方。时刻注意
一处改动导致其他地方失效。

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
