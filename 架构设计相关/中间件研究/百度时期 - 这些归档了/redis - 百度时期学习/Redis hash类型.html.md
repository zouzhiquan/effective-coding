---
author: zouzq7@163.com
created: "2018-12-12 11:28:09 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis hash类型
updated: "2019-05-22 11:47:03 +0000"
---

<div>

Hash
表示的是一种字段与值之间的映射关系，与很多编程语言中的map或者字典类型类似。Redis其实本身就可以本身就可以看作一个大Hash，其字符串类型的键关联到字符串或者链表之类的数据对象。而Redis
中的数据对象也可以再次使用Hash，其字段和值必须是字符串类型，在这里其实可以简单的理解为一个大Map。

</div>

<div>

先看一下使用：HMSET（设置多个属性值，如果存在会产生覆盖）、HSET（设置一个属性值，如果存在会产生覆盖）、HMGET（从一个Hash中获取多个属性值）、HGET（从一个Hash中获取一个属性值）、HEXISTS（判断Hash中某个属性是否存在）、HGETALL（获取一个Hash中的所有属性和值）、HSCAN（增量获取属性和值）从使用上来说，Hash看起来跟list是类似的，都是先初始化一个Hash，然后进行对应的操作，并且在Hash为空是Redis也会帮我们直接删除。

</div>

<div>

一个Hash最多能容纳8388607（2\^23 -
1）个字段，如果这个量打满或者说量相对较大的时候，一个HGETALL命令会直接直接夯住Redis
服务器（Redis
之前提过执行其他命令时是会阻塞其他命令的），所以数据量非常大的时候Redis会一直在执行HGETALL
从而导致其他命令没法顺利执行。如果存在这种场景，尽可能的使用HSCAN来完成操作。

</div>

<div>

下面来看看关于Hash在Redis中的具体实现：

</div>

<div>

Redis Hash
类型底层有两种编码格式：ziplist、hashtable，就默认来说Hash对象保存的所有键值对的字符串都小于64字符，并且Hash对象保存的键值对数量小于512时是使用ziplist的，如果无法同时满足这两个则使用hashtable编码，这里是可以由使用者自定义进行控制的，redis提供了这么几个参数：

</div>

<div>

hash-max-ziplist-value 64 // ziplist中最大能存放的值长度

</div>

<div>

hash-max-ziplist-entries 512 // ziplist中最多能存放的

</div>

<div>

这里采用两个结构还是因为空间和时间之间的衡量，如果量还好的话直接使用ziplist
进行存储，时间上还ok 但空间上非常小，但如果是Hashtable
时间上ok，但空间不太合适，在量小并且长度较小的时候ziplist是比较占优势的，通常来说大多数场景都是符合那两个要求的。具体关于ziplist、zipmap等后续会有单独的文章进行描述。

</div>

<div>

下面来看Hash对象的底层实现: src/t_hash.c

</div>

<div>

按照命令使用顺序，先来看HSET：

</div>

<div>

先来看注释内容：

</div>

<div>

![](Redis%20hash%E7%B1%BB%E5%9E%8B.resources/68A1E1BD-B9D2-4D09-B6AA-80AF7A6940F3.png) 
 \

</div>

<div>

使用上：

</div>

<div>

1、添加新字段，如果旧字段已经存在，则用新值覆盖旧字段。

</div>

<div>

2、在插入时返回0，在更新时返回1。

</div>

<div>

源码解释：

</div>

<div>

默认情况下，将复制键和值SDS字符串，因此调用方保留所传递的字符串的所有权。然而，可以通过传递适当的标志（可能按位或按位）来影响这种行为：

</div>

<div>

HASH_SET_TAKE_FIELD------SDS 参数的所有权传递给函数。

</div>

<div>

HASH_SET_TAKE_VALUE------SDS值所有权传递给函数。

</div>

<div>

HASH_SET_COPY对应于不传递任何标志，默认需要时复制值。

</div>

<div>

\

</div>

<div>

接下来是源码：

</div>

<div>

首先判断编码：

</div>

<div>

如果是ziplist编码，先得到ziplist的head，然后检查fptr是否已经存在，如果存在的话取ziplist的next（也就是对应的value），之后标示更新操作，先删除旧的值，然后插入新的值。如果不存在的话，也就代表着不是一个更新操作，则直接把对应的键值对插入到ziplist的尾部，先插入key，再插入value。

</div>

<div>

如果是hashtable编码的话，在dict中查找对应的field，如果存在的话执行更新操作，如果不存在dictAdd
添加新的键值对就好了

</div>

<div>

关于宏定义的描述就跟上面注释说的一样：

</div>

<div>

flag为HASH_SET_COPY，field与value没有释放掉空间

</div>

<div>

flag为HASH_SET_TAKE_VALUE则value的值会释放掉

</div>

<div>

flag为HASH_SET_TAKE_FIELD则field的值会释放掉

</div>

<div>

![](Redis%20hash%E7%B1%BB%E5%9E%8B.resources/8938BC57-E0BF-48FC-AF89-0964A838E0AB.png) 
 \

</div>

<div>

\

</div>

<div>

上面提到了关于Hash 底层两种ziplist的转换（实际上
是ziplist向hashtable转换，单向不可逆），下面就来看一下插的节点过多或者长度过长之后带来的转换操作

</div>

<div>

因为在函数中用到了t_hash
中所定义的迭代器，所以先来看一下迭代器的实现：src/t_hash L：324

</div>

<div>

首先完成内存空间的分配，然后根据不同的编码设置不同的成员，最后返回一个字典迭代器给di成员就完成了初始化步骤。

</div>

<div>

![](Redis%20hash%E7%B1%BB%E5%9E%8B.resources/C43BA154-9BB2-4C96-B8A3-01A3618F09B5.png) 
 \

</div>

<div>

具体迭代操作繁盛在hashTypeNext函数中，先声明了这一堆指针，如果当前迭代器为空，则初始化只想ziplist的第一个节点，如果不为空则只想下一个key节点，fptr的下个节点就是对应的值节点，然后更新参数，如果是OBJ_ENCODING_HT编码，直接调用哈希迭代器就可以了。

</div>

<div>

![](Redis%20hash%E7%B1%BB%E5%9E%8B.resources/CE60BCB7-6498-42F5-82CC-2728E9F9A462.png) 
 \

</div>

<div>

具体转化函数：src/t_hash.c L：465

</div>

<div>

第一行是类型的转化，根据enc来确定，一般来说是从ziplist转化为dict。

</div>

<div>

如果enc是ziplist则不进行转换，如果是ht的话，先初始化迭代器，创建新的表，不断的插入下一个（前面提到的迭代器操作），最后释放迭代器，然后完成转换。

</div>

<div>

![](Redis%20hash%E7%B1%BB%E5%9E%8B.resources/84D37D17-4DA2-46A0-9632-2ECCA67152CE.png) 
 \

</div>

<div>

然后是对应的删除函数和长度函数：

</div>

<div>

首先是删除函数
同样的判断是哪种编码，然后找到对应的节点删除就好了，如果是ht编码的话，在删除之后通常需要一个resize操作。

</div>

<div>

长度的话，如果是ziplist直接
/2（因为有一半是值节点），如果是ht，看一下字典熟练就好了。

</div>

<div>

![](Redis%20hash%E7%B1%BB%E5%9E%8B.resources/ECDE9566-D785-4E8E-82C3-B8790E540106.png) 
 \

</div>

<div>

整个t_hash 的使用及实现就先说这么多。

</div>
