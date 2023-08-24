---
author: zouzq7@163.com
created: "2018-12-11 12:17:36 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis string之SDS源码分析2
updated: "2018-12-12 03:50:05 +0000"
---

<div>

上一篇我们看了Redis里面关于新旧版本对于sds的不同的结构体实现，接下来看看sds.c中关于redis动态字符串的具体操作。

</div>

<div>

从new开始：

</div>

<div>

sdsnew函数是创建一个sds字符串的开始函数。

</div>

<div>

![](Redis%20string%E4%B9%8BSDS%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%902.resources/9CD2ADD6-D6DF-4F9E-8846-11C0E8DFCE79.png) 
 \

</div>

<div>

实际的初始化逻辑在sdsnewlen中，不仅是sdsnew函数，sdsempty、sdsup其实内部调的都是sdsnewlen这个函数。

</div>

<div>

先看一下函数注释的意思，就是说使用给定的字符串长度作为初始化值来创建一个sds动态字符串，如果是Null的话，那就给0字节，最后说了下sds是二进制安全的，我们可以放心的使用\\0。

</div>

<div>

然后是代码逻辑：

</div>

<div>

1、首先定义了一个void \*sh
，这里为了进一步提升性能不同长度的字符串只能使用不同的结构体，所以说无法确切定义
例如：struct sdshdr
\*sh，这里宏里是写死的。然后声明一个别名（这里的sds其实就是sdshdr中的buf的指针）

</div>

<div>

2、根据不同的长度决定使用不同的结构体，然后下面那一段是一个经验写法为了放入超过32长度的字符串

</div>

<div>

3、定义一个具体标示具体结构体的指针

</div>

<div>

4、剩下的都是根据不同的长度分配对应的结构体，并且设置属性了

</div>

<div>

![](Redis%20string%E4%B9%8BSDS%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%902.resources/24A8EC42-8896-4358-AE6F-8C2B4176C95D.png) 
 \

</div>

<div>

这个函数没有截完整，稍微有点长，后面的都是类似的case 语句啦

</div>

<div>

\

</div>

<div>

除了创建新的字符串，然后就是追加字符串，追加字符串用的是sdscatlen 函数
src/sds.c L：379

</div>

<div>

这个函数是用于截取指定字符串的指定长度追加到原有字符串中的，sds
s：原有串、const void
\*t：要追加到原有字符串后的值、len：要截取的待拷贝的值的长度

</div>

<div>

先计算当前字符串已经使用的长度，根据新长度来衡量是进行扩容还是保持不变，直接进行内存拷贝而不是字符串拷贝，来保证二进制兼容，最后设置新长度就好了。

</div>

<div>

![](Redis%20string%E4%B9%8BSDS%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%902.resources/C88957C2-198B-4908-B497-B8C00C4E7711.png) 
 

</div>

<div>

很显然sdscatlen函数中没有做什么很核心的事儿，真正比较关心其中使用的sdsMakeRoomFor
扩容函数。下面来看一下这个函数：src/sds.c L：197

</div>

<div>

当出现现有可用空间无法满足新串长度时进行扩容

</div>

<div>

入参：sds s：带扩容的sds字符串指针、size_t addlen：新串的长度

</div>

<div>

出参：新的sds指针，如果没发生扩容和入参是一致的

</div>

<div>

1、首先计算原sds还剩多少可分配空间，如果够的话，直接返回（一直到第8行）

</div>

<div>

2、如果新长度小于最大预分配长度则扩容为2倍，如果新长度大于最大预分配长度则仅追加SDS_MAX_PREALLOC长度

</div>

<div>

3、如果是SDS_TYPE_5，则直接分配SDS_TYPE_8类型

</div>

<div>

4、如果类型没有发生变化则重新开辟一块内存将原先整个SDS拷贝一份过去即可

</div>

<div>

![](Redis%20string%E4%B9%8BSDS%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%902.resources/228BECEF-13F3-4817-978F-3632D69809CE.png) 
 

</div>

<div>

其他的另外几个函数都是依赖于这几个核心函数的，现在应该对SDS有了更深一步的理解啦。

</div>

<div>

\

</div>
