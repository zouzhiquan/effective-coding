---
author: zouzq7@163.com
created: "2018-12-10 09:23:03 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis 中List 及 quicklist实现 2
updated: "2018-12-11 04:06:19 +0000"
---

<div>

上一篇中看了List的使用方式、quicklist中的各个结构体，这一篇来看看quicklist里面的几个核心函数，quicklistCreate函数、quicklistCreateNode函数、quicklistPush函数、quicklistPop函数。

</div>

<div>

接下来我们通过源码看一下quicklist中是如何借鉴STL中deque的这种实现思想来完成对于sdlist及ziplist有点的结合的，并且在时间和空间是如何做的均衡，来达到"quick"的效果。

</div>

<div>

我们打开quicklist.c文件，找到第94行。

</div>

<div>

首先完成了对于一个quicklist结构体的指针，然后对quicklist进行内存分配操作，之后再设置首尾指针，再指定quicklist的长度、数据项总和、压缩深度、ziplist的的大小限定（这个值可以有五个取值，-1：每个节点的ziplist字节数不能超过4kb,-2：每个节点的ziplist字节数不能超过8kb,-3：每个节点的ziplist字节数不能超过16kb,-4：每个节点的字节数不能超过32kb,-5：每个节点的字节数不能超过64kb,
默认是不能超过4kb的）

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%202.resources/3C753B23-B88E-45E5-AEE9-D69167EBD552.png) 
 

</div>

<div>

创建完quicklist之后，下一步就是创建quicklist节点了,看一下quicklist的第138行

</div>

<div>

这个过程和创建quicklist基本上是一致的，声明指针、申请内存、初始化ziplist指针、初始化数据项、ziplist的大小、初始化prev、next指针、初始化节点编码方式默认是QUICK_NODE_ENCODING_RAW、初始化数据的存放方式默认是QUICKLIST_NODE_CONTAINER_ZIPLIST，最后初始完压缩标示然后结束。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%202.resources/4B012F6F-9C67-4CD1-A3BF-6BA75F9831D8.png) 
 \

</div>

<div>

看完初始化操作之后，接下来是PUSH操作：

</div>

<div>

不管是LPUSH还是RPUSH都包含两个步骤，如果插入节点的ziplist大小没有超过限制直接用ziplistPush函数压入，如果ziplist的大小超过了限制，则新创建一个quicklist来进行压入。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%202.resources/37096B0A-4BFA-4E01-B6EF-A5992384CFAA.png) 
 

</div>

<div>

然后来看一下这两个函数：

</div>

<div>

likely是linux提供的可选择的编译优化方法，可以讲分支专一的信息提供给编译器，然后减少指令跳转带来的性能下降（likely是编译器级别的优化）。

</div>

<div>

首先判断首/尾是否允许插入（首部节点的大小和fill参数做比较）然后如果允许插入世界调用ziplistpush插入，然后更新首部大小即可以了，如果节点满了，看一些else里面的内容，就需要重新创建一个节点，将新节点压入心创建的ziplist中，并且与新创建的quicklist节点关联起来，同时更新大小，然后创建一个新的ziplist节点，完成压入，更新数据项等。如果反悔的quicklist指针没变，则返回0，否则返回1。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%202.resources/44B2D493-98AB-403C-86CF-244AEC4A2554.png) 
 

</div>

<div>

接下来看一下quicklistPop函数，然后具体的逻辑其实是在quicklistPopCustom中实现的。

</div>

<div>

就是进行Pop操作，执行成功返回1，失败返回0，如果弹出的节点是字符串，那么data、sz存放弹出字符串值，如果弹出节点是整型，slong存放弹出节点的整型值。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%202.resources/30E62372-7CE7-488C-952F-FB63604A1231.png) 
 

</div>

<div>

然后具体看一下quicklistPopCustom函数，执行成功返回1，失败返回0，如果弹出的节点是字符串，那么data、sz存放弹出字符串值，如果弹出节点是整型，slong存放弹出节点的整型值。

</div>

<div>

首先判断弹出位置首部或者尾部，如果没有数据直接return
0，然后获取quicklist的节点（ziplist），再获取ziplist的节点，然后获取节点的值，如果是字符串值，通过_quicklistSaver深拷贝取出返回值，如果是整型的则字符串设置为NULL，弹出节点的整型值，删除该节点。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%202.resources/1EACD021-EAB8-49C9-A465-2CE4191A720D.png) 
 

</div>

<div>

这里_quicklistSaver 深拷贝取值的原因是避免二次释放。

</div>

<div>

![](Redis%20%E4%B8%ADList%20%E5%8F%8A%20quicklist%E5%AE%9E%E7%8E%B0%202.resources/686A0446-1032-4FD9-BE5A-F9E352A26C26.png) 
 

</div>

<div>

\

</div>

<div>

quicklist核心的API就这几个，但Redis实际上是实现了好多的，比如说：\

</div>

<div>

1、比较两个quicklist结构数据的：quicklistCount

</div>

<div>

2、从节点node中取出LZF压缩编码后的数据：quicklistGetLzf

</div>

<div>

3、翻转quicklist：quicklistRotate

</div>

<div>

4、删除ziplist节点entry：quicklistDelEntry

</div>

<div>

5、在node节点前添加一个value：quicklistInsertBefore

</div>

<div>

6、在node节点后添加一个value：quicklistInsertAfter

</div>

<div>

7、将ziplist转换为quicklist：\*quicklistCreateFromZiplist

</div>

<div>

\

</div>
