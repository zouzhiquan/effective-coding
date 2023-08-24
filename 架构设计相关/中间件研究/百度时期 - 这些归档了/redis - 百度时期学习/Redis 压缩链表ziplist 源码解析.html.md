---
author: zouzq7@163.com
created: "2018-12-19 13:08:22 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis 压缩链表ziplist 源码解析
updated: "2019-01-16 07:15:17 +0000"
---

<div>

之前说quicklist 及 hash 类型的时候都提到了一种底层的实现结构叫做
ziplist。先看一下源码里面官方的介绍：

</div>

<div>

![](Redis%20%E5%8E%8B%E7%BC%A9%E9%93%BE%E8%A1%A8ziplist%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90.resources/72F115A7-DCF7-4B49-B3ED-B3734B51A3CE.png) 
 \

</div>

<div>

这段话大体意思是说，ziplist是一种特殊编码的双链表，主要是为了节省内存而存在的，整个都是由字符串和整数值组成的，其中整数值被编码为实际的整数，而不是一系列字符。可以在O(1)
时间内进行push和pop操作，然后具体一些操作的复杂程度和具体使用的内存数量有关，这也就是为什么数量膨胀到一定程度之后很多结构就不采用ziplist。下面具体的来看一下ziplist的实现：

</div>

<div>

首先ziplist是一种连续&无序的线形数据结构，结构是这样的：

</div>

<div>

![](Redis%20%E5%8E%8B%E7%BC%A9%E9%93%BE%E8%A1%A8ziplist%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90.resources/5C443FE1-2BAA-4CB2-8A18-27E13CF51904.png) 
 \

</div>

<div>

![](Redis%20%E5%8E%8B%E7%BC%A9%E9%93%BE%E8%A1%A8ziplist%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90.resources/E3CCB814-DD24-48E1-9D5F-40663CB8E5EC.png) 
 \

</div>

<div>

与其他的数据结构不同并没有定义专门的数据结构来保存ziplist的信息，而是采用了一组宏来定位每个成员的地址，具体源码的话可以看一src/ziplist.c

</div>

<div>

![](Redis%20%E5%8E%8B%E7%BC%A9%E9%93%BE%E8%A1%A8ziplist%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90.resources/BAC37F9F-D4C1-47D0-A7DC-16BFE537A134.png) 
 \

</div>

<div>

ZIPLIST_BYTES
将zl定位到签字个字节的bytes成员，记录整个ziplist的内存字节数，ZIPLIST_TAIL_OFFSET
将zl定位到4字节到8字节的tail_offset成员，记录了下压缩列表起始地址的偏移字节量，将zl定位到8字节到10字节的length成员，也就是记录了ziplist的节点数量，ZIPLIST_HEADER_SIZE
指的是ziplist头节点的大小（ZIPLIST_END_SIZE 一样），ZIPLIST_ENTRY_HEAD
、ZIPLIST_ENTRY_TAIL、ZIPLIST_ENTRY_END
分别指的是首节点的地址、尾节点的地址、END节点的地址。

</div>

<div>

然后存在一个叫做zlentry的结构来管理各个节点的所有信息，prevrawlen是指前驱节点的长度，prevrawlensize
编码前驱节点prevrawlen所需要的字节大小，lensize
指的是当前节点值长度len所需要的字节数，len当前节点长度，headersize
当前节点header大小（也就是lensize+prevrawlensize），encoding
为当前节点的编码格式，\*p
只想当前节点的指针。然而并没有使用这个结构体来存储数据节点中的结构，因为如果存储小整型或者短字符串就造成了不必要的内存浪费。

</div>

<div>

![](Redis%20%E5%8E%8B%E7%BC%A9%E9%93%BE%E8%A1%A8ziplist%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90.resources/4AA197A8-7599-496D-98E4-57EBD6549A9D.png) 
 \

</div>

<div>

但是为了更加省内存，ziplist对于上述的这种结构都是嫌弃的，直接简单粗暴的采用了下面这种结构：

</div>

<div>

实际上是这么来存的 \<prev_entry_len\>(记录前驱节点的长度),
\<encoding\>(当前节点的value成员的数据类型和长度),
\<value\>（保存字节数组和整数）

</div>
