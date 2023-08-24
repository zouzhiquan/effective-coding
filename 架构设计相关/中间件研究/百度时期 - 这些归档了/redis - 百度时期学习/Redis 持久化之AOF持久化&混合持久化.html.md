---
author: zouzq7@163.com
created: "2019-02-13 06:52:28 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis 持久化之AOF持久化&混合持久化
updated: "2019-03-05 14:27:52 +0000"
---

<div>

上一篇提到了Redis的RDB持久化方式，同时也提到了一点关于AOF的内容。

</div>

<div>

RDB（snapshotting）
是一种内存快照的方式进行持久化，AOF（append-only-file）是通过追加写入命令的方式进行持久化，混合持久化是指RDB和AOF协同完成持久化工作来发挥各自有点的持久化方式。

</div>

<div>

AOF中的核心配置有这么三项：appendonly（是否打开AOF功能）、appendfilename（AOF文件的名称）、appendfsync（具体的同步的频率，always：每个Redis命令都要同步写入磁盘、everysec
每秒批量同步至磁盘、no：操作系统来决定什么时机落盘）

</div>

<div>

![](Redis%20%E6%8C%81%E4%B9%85%E5%8C%96%E4%B9%8BAOF%E6%8C%81%E4%B9%85%E5%8C%96&%E6%B7%B7%E5%90%88%E6%8C%81%E4%B9%85%E5%8C%96.resources/DB7FE05A-7E03-42EF-A077-0E1EA34BA909.png) 
 \

</div>

<div>

然后Redis 中是按照RESP协议格式来保存命令内容的，有兴趣可以看一下：

</div>

<div>

<https://redis.io/topics/protocol>

</div>

<div>

很显然，如果命令操作量非常大的时候，与RDB不同，AOF因为是追加命令，所以很大概率上AOF持久化文件会越来越大。这里AOF提供了一种优化策略策略。用户可以选择BGREWRITEAOF命令，来后台执行重写AOF文件命令，这个过程中会消除冗余的命令，来尽可能的减小AOF大小。（实际上还是会很大，效果其实一般啦，并且如果AOF文件已经非常大了，重写是一种比较影响Redis性能的使用方式，不推荐使用，具体场景具体分析吧）

</div>

<div>

和动态重写相关的配置有这么几项：auto-aof-rewrite-percentage用来指定AOF文件需要比旧AOF文件增大多少时才进行AOF重写。auto-aof-rewrite-min-size
用来指定AOF到达多大体积时才进行AOF重写，Redis
就是用这两个配置来控制AOF重写的（同时达到时才进行重写），整个重写的过程和BGSAVE比较相似，都是通过fork子进程来对AOF文件进行重写的。

</div>

<div>

![](Redis%20%E6%8C%81%E4%B9%85%E5%8C%96%E4%B9%8BAOF%E6%8C%81%E4%B9%85%E5%8C%96&%E6%B7%B7%E5%90%88%E6%8C%81%E4%B9%85%E5%8C%96.resources/5850E5F6-3918-4D33-8F05-DFFF9519C566.png) 
 \

</div>

<div>

从实现角度来看，AOF持久化大致分为命令追加、文件写入、文件同步三个步骤

</div>

<div>

当一条命令写入时，会以上面提到的协议格式话被执行的命令然后追加到服务器状态的aof_buf缓冲区的末尾，服务器执行完写命令之后，调用propagate进行命令追加。然后Redis的服务器进程实际上是一个事件循环，文件时间负责处理客户端的命令请求，时间时间负责执行一些定时执行的函数。在处理文件时间执行写命令，使命令被追加到aof_buf中，然后在处理时间时间执行的serverCron函数会调用flushAppendOnlyFile函数进行文件的写入、同步。

</div>

<div>

上面提到了在重写是通过fork子进程来执行的，这样主要是为了防止长时间阻塞主进程，在子进程重写的期间，主进程可以继续处理命令，不使用线程的原因是，开一个新的线程是无法携带主进程数据副本的，也就没办法避免与主进程竞争db-\>dict。至于主进程数据与子进程的数据副本不一致的问题而言，redis设置了AOF重写缓冲区，在子进程重写期间，主进程执行的每一个写命令都会写到重写缓冲区，然后子进程完成重写之后，再将重写缓冲区的数据写入到重写的AOF文件中，这样就能保证数据状态的一致性啦。

</div>

<div>

\

</div>

<div>

混合持久化：

</div>

<div>

混合持久化是Redis
4.X之后的一个新特性，说是新特性其实更像是一种RDB&AOF的结合，持久化文件变成了RDB +
AOF，首先由RDB定期完成内存快照的备份，然后再由AOF完成两次RDB之间的数据备份。这样就充分了利用了RDB
加载快，备份文件小等特点，也利用了AOF能尽可能不丢数据这个特性（进一步保证了数据一致性），当然了基本上丢失了AOF的可读性。加载过程就是按部分进行加载的，先按照RDB进行加载，然后把AOF命令追加写入就好了。在大多数场景下RDB +
AOF的混合持久化模式其实还是很合适的。

</div>

<div>

\

</div>
