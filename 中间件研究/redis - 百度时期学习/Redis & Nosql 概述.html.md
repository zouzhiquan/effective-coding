---
author: zouzq7@163.com
created: "2018-12-05 12:47:09 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis & Nosql 概述
updated: "2019-01-16 07:18:55 +0000"
---

<div>

这个系列要讲的是几乎所有互联网公司都在使用的Redis，Redis
适用于挺多的业务场景，比如会话存储、数据分析、消息队列、缓存等。应用面非常广泛，主要是得益于Redis
的高效的读写效率及丰富的数据结构。

</div>

<div>

在讲Redis之前，我们先来看一下常见的NoSQL DB，所谓的NoSQL
也就是相对于关系型数据库而言，泛指所有的非关系型数据库。我们常说的SQL
也就是结构化查询语言，有时候经常被认为关系型数据库的一种特征，其实NoSQL有时候也被理解为不使用SQL，但这种描述方式不是很准确。这里我还挺支持之前看过的《NoSQL精粹》里面的观点的。

</div>

<div>

常见的NoSQL 有这么几类：key-value
结构、图数据库、文档型数据库、列族数据库。

</div>

<div>

常见的代表有这么几个：

</div>

<div>

key-value结构：Memcached、Redis

</div>

<div>

文档：MangoDB、CouchDB

</div>

<div>

列：Hbase、Cassandra

</div>

<div>

里面熟悉的就只有Redis和MangoDB，HBase仅仅是在用而已，其他的那几个都是写文章还有看NoSQL精粹的时候了解到的一点，仅知道概念，不过多做描述。

</div>

<div>

\

</div>

<div>

所有的事物框架的产生和繁衍都是有一定的原因和环境的推动因素的，NoSQL也是这样。在web
2.0的兴起的同时，网站开始变得越来越复杂，数据量级不断提升、对于数据的存取方式&速度产生了更高的要求，存在各方面来自业务场景的挑战，而传统的关系型数据库开始无法儿满足要求，所以各大公司开始针对这些场景研究一种类似于专用的数据库，这些数据库大部分不使用SQL进行交互（也有一部分为了兼容历史业务也是支持的），然后拥有自己特有的存储结构及交互API，慢慢的也就发展成了上面提到的那几类NoSQL数据库，慢慢的变得规范起来。

</div>

<div>

但是所有的NoSQL都如同上面描述的一样，最初的诞生都是为了解决某一类特殊场景的业务需求才产生的，所以说大部分NoSQL都可以简单粗暴的理解为专用的，我们在选择具体的应用时就要注意选择最合适的。

</div>

<div>

\

</div>

<div>

然后来看一下Redis：

</div>

<div>

Redis 是一个开源的、使用ANSI
C编写的、支持网络、基于内存的、可持久化的Key-Value
型的数据库，并且提供多种语言的API。创建者是一个出生于西西里岛的意大利人（antirez），他早年是做系统管理员工作的，2004到2006年做嵌入式开发，之后开始接触web开发，07年的时候创建了一个叫做LLOOGG.com，当时这个网站存在负载瓶颈，为了解决这个问题，在09年的时候开发Redis
数据库（Redis
其实还挺年轻的），然后在2010年3月15日之后，Redis的开发工作由VMware主持，之后5月份的时候，有Pivotal赞助。

</div>

<div>

但现在为止Redis
已经历经了好几个版本（简单描述几个重大版本，Redis借鉴了Linux操作系统对于版本号的命名规则，版本号第二位为奇数的是非稳定版本（通常是下个稳定版的开发版本），是偶数的为稳定版本）：

</div>

<div>

1、Redis 2.6

</div>

<div>

2012年正式发布，历经了17个版本，相对于之前的，多了对于Lua的支持、去掉虚拟内存相关功能、支持毫秒级别键过期时间、从节点支持只读功能、增加了bitcount&bitop命令等，从2.6开始Redis
做的更加专注了

</div>

<div>

\

</div>

<div>

2、Redis 2.8

</div>

<div>

2013年正式发布，历经了24个版本。主要是针对分布式应用的一些提升，比如说添加部分主从复制来缓解频繁全量复制生成RDB对系统产生的压力、尝试支持IPV6、设置明显的进程名、发布订阅增加了pubsub。

</div>

<div>

\

</div>

<div>

3、Redis 3.0（里程碑）

</div>

<div>

这个直接看一下官方的说明：

</div>

<div>

1）Redis Cluster：Redis的官方分布式实现。

</div>

<div>

2）全新的embedded
string对象编码结果，优化小对象内存访问，在特定的工作负载下载速度大幅提升。

</div>

<div>

3）Iru算法大幅提升。

</div>

<div>

4）migrate连接缓存，大幅提升键迁移的速度。

</div>

<div>

5）migrate命令两个新的参数copy和replace。

</div>

<div>

6）新的client pause命令，在指定时间内停止处理客户端请求。

</div>

<div>

7）bitcount命令性能提升。

</div>

<div>

8）cinfig set设置maxmemory时候可以设置不同的单位（之前只能是字节）。

</div>

<div>

9）Redis日志小做调整：日志中会反应当前实例的角色（master或者slave）。

</div>

<div>

10）incr命令性能提升。

</div>

<div>

\

</div>

<div>

4、Redis 3.2
（这里需要注意一下，市面上常见的Redis技术书籍《Redis设计与实现》所阐述的就是3.2之前版本的特性，技术方面的知识还是存在很大的时效性的，这部分知识已经不是那么试用了，大家看那本书的是需要注意一些当前生产环境中的版本及书里版本的差异）

</div>

<div>

Redis3.2在2016年5月6日正式发布，相比于Redis3.0主要特征如下：

</div>

<div>

1）添加GEO相关功能。

</div>

<div>

2）SDS在速度和节省空间上都做了优化。

</div>

<div>

3）支持用upstart或者systemd管理Redis进程。

</div>

<div>

4）新的List编码类型：quicklist。

</div>

<div>

5）从节点读取过期数据保证一致性。

</div>

<div>

6）添加了hstrlen命令。

</div>

<div>

7）增强了debug命令，支持了更多的参数。

</div>

<div>

8）Lua脚本功能增强。

</div>

<div>

9）添加了Lua Debugger。

</div>

<div>

10）config set 支持更多的配置参数。

</div>

<div>

11）优化了Redis崩溃后的相关报告。

</div>

<div>

12）新的RDB格式，但是仍然兼容旧的RDB。

</div>

<div>

13）加速RDB的加载速度。

</div>

<div>

14）spop命令支持个数参数。

</div>

<div>

15）cluster nodes命令得到加速。

</div>

<div>

16）Jemalloc更新到4.0.3版本。

</div>

<div>

\

</div>

<div>

5.Redis4.0

</div>

<div>

Redis3.2之后的版本是4.0，而不是3.4、3.6、3.8，作者blog中给的解释是这次的重大更新足以撑起新的一个大版本，产生了质的差异，所以说直接使用4.0进行命名

</div>

<div>

一般这种重大版本号的升级也意味着软件或者工具本身发生了重大改革。下面是Redis4.0的新特性：

</div>

<div>

1）提供了模块系统，方便第三方开发者拓展Redis的功能。

</div>

<div>

2）PSYNC2.0：优化了之前版本中，主从节点切换必然引起全量复制的问题。

</div>

<div>

3）提供了新的缓存剔除算法：LFU（Last Frequently
Used），并对已有算法进行了优化。

</div>

<div>

4）提供了非阻塞del和flushall/flushdb功能，有效解决删除了bigkey可能造成的Redis阻塞。

</div>

<div>

5）提供了memory命令，实现对内存更为全面的监控统计。

</div>

<div>

6）提供了交互数据库功能，实现Redis内部数据库的数据置换。

</div>

<div>

7）提供了RDB-AOF混合持久化格式，充分利用了AOF和RDB各自优势。

</div>

<div>

8）Redis Cluster 兼容NAT和Docker。

</div>

<div>

\

</div>

<div>

Redis写的确实挺牛逼的，先说说它的几个优势和特点：

</div>

<div>

1、高效读写

</div>

<div>

Redis是基于内存的，并且因为Redis的事件模型、存储结构等方面的优化，相对于传统数据库而言，读取速度异常快。

</div>

<div>

2、丰富的数据结构

</div>

<div>

这个主要是针对相同类型的内存数据库memcahced来说的，Redis里面主要有这样几种结构：字符串（string）、列表（list）、哈希（hash）、集合（set）、有序集合（sorted
set）、HyperLogLog、GEO。因为这些丰富的数据结构在使用Redis
时变得异常轻松，我们很容易就根据业务场景来进行使用，而不用在DB上层再封装n多功能。

</div>

<div>

3、功能专一且强大

</div>

<div>

redis
对于键过期、定期清扫、并且支持多种持久化方式等，并且API易用性非常强。

</div>

<div>

后面从各个大家可能感兴趣的方面来阐述Redis的原理及使用。

</div>
