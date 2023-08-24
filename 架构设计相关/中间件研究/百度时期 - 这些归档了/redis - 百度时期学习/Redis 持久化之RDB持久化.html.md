---
author: zouzq7@163.com
created: "2019-01-16 07:20:44 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis 持久化之RDB持久化
updated: "2019-01-16 08:49:30 +0000"
---

<div>

Redis 相对于其他NoSQL
内存数据库而言，除了更富的数据结构和速度快之外，Redis
的丰富的持久化方案也就一个很显著的优势，Redis
支持RDB、AOF、混合持久化三种模式。RDB（snapshotting）
是一种内存快照的方式进行持久化，AOF（append-only-file）是通过追加写入命令的方式进行持久化，混合持久化是指RDB和AOF协同完成持久化工作来发挥各自有点的持久化方式。

</div>

<div>

本篇讲的是RDB持久化：

</div>

<div>

上面提到了RDB文件实际上是一种内存快照，直接截取某个时间点的Redis中的全部数据，很显然特点是恢复速度会很快（将快照加载到内存中），然后RDB文件是一个很紧凑的二进制文件适合保存。但是也存在一定的缺点，在创建RDB快照之后来的命令及产生的数据变动相当于是无法进行持久化记录的（无法实时保存，另外bgsave代价还挺高的，save又会阻塞redis），所以说备份到断电这段时间是存在一定的数据不一致情况的，并且RDB持久化出来的RDB快照很有可能存在版本兼容问题（不能适用于其他版本的Redis
集群）。

</div>

<div>

RDB 持久化有自动触发、手动触发两种方式。

</div>

<div>

自动触发

</div>

<div>

具体可以看一下redis.conf
中的配置项及对应注释来了解这一部分内容，翻一下注释就很明了了：

</div>

<div>

当达到如下条件的时候就出发自动持久化，这种持久化在后台进行的bgsave

</div>

<div>

先看一下save选项：

</div>

<div>

save 900 1：表示900 秒内如果至少有 1 个 key 的值变化，则保存

</div>

<div>

save 300 10：表示300 秒内如果至少有 10 个 key 的值变化，则保存

</div>

<div>

save 60 10000：表示60 秒内如果至少有 10000 个 key 的值变化，则保存

</div>

<div>

![](Redis%20%E6%8C%81%E4%B9%85%E5%8C%96%E4%B9%8BRDB%E6%8C%81%E4%B9%85%E5%8C%96.resources/24CD5823-708A-4BEF-8B66-4321034B3985.png) 
 

</div>

<div>

再看下一段，RDB持久化时，redis
默认不接受写请求，但不阻断读请求（是一种读写分离的方式）。

</div>

<div>

stop-writes-on-bgsave-error
：默认值为yes。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。这会让用户意识到数据没有正确持久化到磁盘上，否则没有人会注意到灾难（disaster）发生了。如果Redis重启了，那么又可以重新开始接收数据了

</div>

<div>

rdbcompression
；默认值是yes。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能，但是存储在磁盘上的快照会比较大。

</div>

<div>

rdbchecksum
：默认值是yes。在存储快照后，我们还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能。

</div>

<div>

dbfilename ：设置快照的文件名，默认是 dump.rdb

</div>

<div>

dir：设置快照文件的存放路径，这个配置项一定是个目录，而不能是文件名。默认是和当前配置文件保存在同一目录。

</div>

<div>

![](Redis%20%E6%8C%81%E4%B9%85%E5%8C%96%E4%B9%8BRDB%E6%8C%81%E4%B9%85%E5%8C%96.resources/A11C1DBE-546F-4EBE-8AE0-70C0D8E6B1D4.png) 
 

</div>

<div>

总体来说，就是达到save的触发条件时，默认bgsave
将内存快照保存到当前redis.conf 同级目录下。

</div>

<div>

手动触发

</div>

<div>

save

</div>

<div>

这个命令会阻塞Redis
服务器，一直到save命令执行完才会接受其他的新的处理命令。

</div>

<div>

bgsave

</div>

<div>

bgsave时会创建一个fork一个子进程，RDB持久化工作由子进程进行。仅fork期间会阻塞接收新的处理命令。

</div>

<div>

\

</div>

<div>

恢复：

</div>

<div>

数据恢复的过程，整个Redis
都是被阻塞在那里的，一直到持久化完成才正常工作。具体恢复步骤就是把文件移到刚才dir指定的文件下，然后启动redis
就可以啦。

</div>

<div>

\

</div>
