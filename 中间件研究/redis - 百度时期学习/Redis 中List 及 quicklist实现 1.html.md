---
author: zouzq7@163.com
created: "2018-12-07 03:49:00 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis 中List 及 quicklist实现 1
updated: "2018-12-10 09:22:58 +0000"
---

<div>

quicklist是在Redis 3.2
之后出现的一种Redis底层数据结构用于List结构的具体实现，List在Redis中更像是数据结构中常说的双向链表，可以被用作栈或者队列。

</div>

<div>

常用的List中的命令：LPUSH（左端插入）、RPUSH（左端插入）、LRANGE（获取所有元素）、LINSERT（在指定名称之后插入）、LINDEX（查看对应索引位置的元素）、LTRIM（删除元素）、LSET（设置指定位置的元素）。其中LPUSH、RPUSH、LINSERT在完成插入之后会返回插入后列表的长度。当如果之前不存在对应的List键，则创建一个新的链表进行插入，同样的Redis会替我们完成对应的回收。

</div>

<div>

Redis
List中的索引：从左到右是：0～N-1，从右到左是：-1～-N，很容易看到，0～-1只的就是整个链表。

</div>

<div>

这里还有两个比较有趣的命令：POP（L、R），并且对应的有BPOP（L、R），B开头的是一种阻塞结构：阻塞版本也是从左端或者右端弹出元素，但是列表为空时，阻塞版本会将客户端阻塞（这里有一个等待的超时时间，如果超时时间为0时，永久等待，如果是其他的则等待对应的时间），这个特性经常在任务调度场景中使用。

</div>

<div>

\

</div>

<div>

下面来看一下Redis
中List的具体实现QuickList，这里有两个相关的参数来进行quicklist的控制：

</div>

<div>

list-max-ziplist-size：每个quicklist的节点都是一个ziplist，这个参数指定的是内部节点的最大大小。

</div>

<div>

list-compress-depth：列表的也锁策略，这个参数指定的是quicklist两端不被压缩的节点的个数，因为照常来说，最常被访问的数据就是位于列表两端的数据。

</div>

<div>

 就具体实现来说，QuickList是一种二合一的数据结构，实现的方式和STL
源码中的Deque实现思想是相似的只不过细节结构上存在差异而已，deque（一个双向开口的可进可出的容器）是一个有map中控器和一个数组构成的数据结构，既有头尾便捷插入的特点、也有数组内存连续&支持下标访问的特点。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%201.resources/60AAF92C-3793-41E3-ADE2-C418A0242D2F.png) 
 

</div>

<div>

在Redis中sdlsit是途中map中控器地位的存在，然后ziplist作为具体的存储结构(综合了双向链表和ziplist的优点），我们需要关注ziplist的容量如果ziplist中的元素过少，内存随便会增多，可以按照极端情况的双向链表来考虑，如果ziplist中的元素个数过多，那么给ziplist分配最大块连续内存空间的难度就增大，同样会影响效率。所以说确定list-max-ziplist-size的大小十分关键。

</div>

<div>

和quicklist相关的文件有quicklist.c（具体逻辑实现）、quicklist.h（结构体定义）

</div>

<div>

下面是quicklist的结构，每个占32字节的空间，里面有一个首尾指针、数据项总和、ziplist的个数、ziplist大小限定（由list-max-ziplist-size来指定，默认16）、节点压缩深度（由list-compress-depth指定，默认16）

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%201.resources/310964DF-7953-4A93-B1CA-74E6F97DD60C.png) 
 

</div>

<div>

然后是节点信息每个同样占32个字节，一个prev指针、next指针、\*zl为数据指针（如果没有被压缩指向ziplist结构，如果被压缩了指向quicklistLZF），sz是ziplist的总长度（内存深度），count是指包含的数据项的个数，encoding是具体的编码方式（1为ziplist、2为quicklistLZF，默认是2），container
是一个预留字段，指存放数据的方式（1 NONE 2
ziplist），recompress是解压标记（需要解压时设置为1之后再重新进行压缩，
默认1），attempted_compress
测试相关，extra是一个扩展字段，临时没什么用。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%201.resources/C05FEAEE-FB31-47F4-81C6-F7E522BCC814.png) 
 

</div>

<div>

\

</div>

<div>

上面提到的quicklistLZF结构如下：

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%201.resources/B4971E4A-6F43-478B-9F78-CFED284DE820.png) 
 

</div>

<div>

另外quicklist还提供了迭代器结构及指向ziplist中的节点结构：

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%201.resources/ECB20942-BD3B-414A-8A35-22ABC6C4742D.png) 
 

</div>

<div>

\

</div>

<div>

\

</div>
