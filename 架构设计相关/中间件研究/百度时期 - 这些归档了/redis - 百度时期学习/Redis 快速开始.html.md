---
author: zouzq7@163.com
created: "2018-08-15 03:31:59 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis 快速开始
updated: "2019-01-16 07:19:22 +0000"
---

<div>

1、master-save的数据备份模式

</div>

<div>

2、单个操作具有原子性（单线程）、多个操作可以使用（MULTI、EXEC）完成原子操作的包装

</div>

<div>

安装：

</div>

<div>

[\$ wget
http] 

</div>

<div>

[\$ tar xzf
redis] 

</div>

<div>

[\$ cd
redis] 

</div>

<div>

[\$ make] 

</div>

<div>

\

</div>

<div>

启动：若指定redis.conf 则代表使用该配置文件启动

</div>

<div>

[\$ cd src] 

</div>

<div>

[\$
] [server
] 

</div>

<div>

\

</div>

<div>

一般都在usr local下

</div>

<div>

\

</div>

<div>

redis-server ：启动

</div>

<div>

redis-cli：启动客户端

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

<div>

[发布订阅：] 

</div>

:::  
[读取：] 
:::

<div>

[redis
] [
SUBSCRIBE
redisChat] 

</div>

<div>

\

</div>

:::  
[发布] 
:::

<div>

[redis
] [
PUBLISH redisChat
] [\"Redis
is a great caching
technique\"] 

</div>

<div>

[(] 

</div>

<div>

[redis
] [
PUBLISH redisChat
] [\"Learn
redis by
] 

</div>

<div>

[(] 

</div>

<div>

\

</div>

<div>

事务：redis里的事务其实就是对于一组命令的批处理，如果中间一条命令失败并不会导致前面的回滚、也不会导致后面的不执行。

</div>

<div>

[redis
] [
MULTI] 

</div>

<div>

[OK] 

</div>

<div>

[redis
] [
SET
book] [name
] [\"Mastering C++ in 21
days\"] 

</div>

<div>

[QUEUED] 

</div>

<div>

[redis
] [
GET
book] 

</div>

<div>

[QUEUED] 

</div>

<div>

[redis
] [
SADD tag
] [\"Mastering
Series\"] 

</div>

<div>

[QUEUED] 

</div>

<div>

[redis
] [
SMEMBERS tag] 

</div>

<div>

[QUEUED] 

</div>

<div>

[redis
] [
EXEC] 

</div>

<div>

[1] [
OK] 

</div>

<div>

[2] [\"Mastering
C++ in 21 days\"] 

</div>

<div>

[3] 

</div>

<div>

[4] [\"Mastering
Series\"] 

</div>

<div>

[  
] 

</div>

<div>

[  
] 

</div>

<div>

\

</div>

<div>

脚本：lua脚本

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

<div>

[启动：redis] 

</div>

:::  
[远程启动：] [cli
] [h
host
] [p
port
] [a
password] 
:::

<div>

DEL key

</div>

<div>

该命令用于在 key 存在时删除 key。

</div>

<div>

\

</div>

<div>

DUMP key

</div>

<div>

序列化给定 key ，并返回被序列化的值。

</div>

<div>

\

</div>

<div>

EXISTS key

</div>

<div>

检查给定 key 是否存在。

</div>

<div>

\

</div>

<div>

EXPIRE key seconds

</div>

<div>

为给定 key 设置过期时间。

</div>

<div>

\

</div>

<div>

EXPIREAT key timestamp

</div>

<div>

EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于
EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。

</div>

<div>

\

</div>

<div>

PEXPIRE key milliseconds

</div>

<div>

设置 key 的过期时间以毫秒计。

</div>

<div>

\

</div>

<div>

PEXPIREAT key milliseconds-timestamp

</div>

<div>

设置 key 过期时间的时间戳(unix timestamp) 以毫秒计

</div>

<div>

\

</div>

<div>

KEYS pattern

</div>

<div>

查找所有符合给定模式( pattern)的 key 。

</div>

<div>

\

</div>

<div>

MOVE key db

</div>

<div>

将当前数据库的 key 移动到给定的数据库 db 当中。

</div>

<div>

\

</div>

<div>

PERSIST key

</div>

<div>

移除 key 的过期时间，key 将持久保持。

</div>

<div>

\

</div>

<div>

PTTL key

</div>

<div>

以毫秒为单位返回 key 的剩余的过期时间。

</div>

<div>

\

</div>

<div>

TTL key

</div>

<div>

以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。

</div>

<div>

\

</div>

<div>

RANDOMKEY

</div>

<div>

从当前数据库中随机返回一个 key 。

</div>

<div>

\

</div>

<div>

RENAME key newkey

</div>

<div>

修改 key 的名称

</div>

<div>

\

</div>

<div>

RENAMENX key newkey

</div>

<div>

仅当 newkey 不存在时，将 key 改名为 newkey 。

</div>

<div>

\

</div>

<div>

TYPE key

</div>

<div>

返回 key 所储存的值的类型。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[获取配置信息：CONFIG GET
CONFIG_SETTING_NAME] 

</div>

:::  
[case：] 
:::

:::  
[config get
loglevel] 
:::

:::  
[config get
\*] 
:::

:::  
<div>

\

</div>
:::

:::  
[可以通过命令配置相关，也可以直接修改conf文件修改配置] 
:::

:::  
<div>

\

</div>
:::

:::  
[redis.conf 配置项说明如下：
] 
:::

:::  
[1.
Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程
] 
:::

:::  
[    ] [daemonize
no] 
:::

:::  
[2.
当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
] 
:::

:::  
[    ] [pidfile
/var/run/redis.pid] 
:::

:::  
[3.
指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia
Merz的名字
] 
:::

:::  
[    ] [port
6379] 
:::

:::  
[4. 绑定的主机地址
] 
:::

:::  
[    ] [bind
127.0.0.1] 
:::

:::  
[5.当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能
] 
:::

:::  
[    ] [timeout
300] 
:::

:::  
[6.
指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
] 
:::

:::  
[    ] [loglevel
verbose] 
:::

:::  
[7.
日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null
] 
:::

:::  
[    ] [logfile
stdout] 
:::

:::  
[8. 设置数据库的数量，默认数据库为0，可以使用SELECT
\<dbid\>命令在连接上指定数据库id
] 
:::

:::  
[    ] [databases
16] 
:::

:::  
[9.
指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合
] 
:::

:::  
[    ] [save
\<seconds\>
\<changes\>] 
:::

:::  
[    Redis默认配置文件中提供了三个条件：
] 
:::

:::  
[    ] [save
900
1] 
:::

:::  
[    ] [save
300
10] 
:::

:::  
[    ] [save
60
10000] 
:::

:::  
[   
分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。
] 
:::

:::  
[ 
] 
:::

:::  
[10.
指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大
] 
:::

:::  
[    ] [rdbcompression
yes] 
:::

:::  
[11. 指定本地数据库文件名，默认值为dump.rdb
] 
:::

:::  
[    ] [dbfilename
dump.rdb] 
:::

:::  
[12. 指定本地数据库存放目录
] 
:::

:::  
[    ] [dir
./] 
:::

:::  
[13.
设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
] 
:::

:::  
[    ] [slaveof
\<masterip\>
\<masterport\>] 
:::

:::  
[14. 当master服务设置了密码保护时，slav服务连接master的密码
] 
:::

:::  
[    ] [masterauth
\<master-password\>] 
:::

:::  
[15.
设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH
\<password\>命令提供密码，默认关闭
] 
:::

:::  
[    ] [requirepass
foobared] 
:::

:::  
[16.
设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置
maxclients
0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max
number of clients reached错误信息
] 
:::

:::  
[    ] [maxclients
128] 
:::

:::  
[17.
指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理
后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区
] 
:::

:::  
[    ] [maxmemory
\<bytes\>] 
:::

:::  
[18.
指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为
redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no
] 
:::

:::  
[    ] [appendonly
no] 
:::

:::  
[19. 指定更新日志文件名，默认为appendonly.aof
] 
:::

:::  
[     ] [appendfilename
appendonly.aof] 
:::

<div>

[20.
指定更新日志条件，共有3个可选值： ] 

</div>

<div>

[    ] 

</div>

<div>

[    ] 

</div>

:::  
[    ] [：表示每秒同步一次（折衷，默认值）
] 
:::

:::  
[    ] [appendfsync
everysec] 
:::

:::  
[ 
] 
:::

:::  
[21.
指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）
] 
:::

:::  
[     ] [vm-enabled
no] 
:::

:::  
[22. 虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享
] 
:::

:::  
[     ] [vm-swap-file
/tmp/redis.swap] 
:::

:::  
[23.
将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据
就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0
] 
:::

:::  
[     ] [vm-max-memory
0] 
:::

:::  
[24. Redis
swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的
数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不
确定，就使用默认值
] 
:::

:::  
[     ] [vm-page-size
32] 
:::

:::  
[25.
设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。
] 
:::

:::  
[     ] [vm-pages
134217728] 
:::

:::  
[26.
设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4
] 
:::

:::  
[     ] [vm-max-threads
4] 
:::

:::  
[27. 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启
] 
:::

:::  
[    ] [glueoutputbuf
yes] 
:::

:::  
[28.
指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法
] 
:::

:::  
[    ] [hash-max-zipmap-entries
64] 
:::

:::  
[    ] [hash-max-zipmap-value
512] 
:::

:::  
[29.
指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）
] 
:::

:::  
[    ] [activerehashing
yes] 
:::

:::  
[30.
指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
] 
:::

<div>

<div>

[    ] [include
/path/to/local.conf] 

</div>

</div>

<div>

[\
] 

</div>

<div>

\

</div>
