---
author: zouzq7@163.com
created: "2018-12-06 09:43:06 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Redis的事件模型（ae epoll实现方式）
updated: "2018-12-07 03:48:04 +0000"
---

<div>

上一篇我们说到了文件描述符及常见的polling机制，下面来看看ae中对于epoll的封装实现。

</div>

<div>

![](Redis%E7%9A%84%E4%BA%8B%E4%BB%B6%E6%A8%A1%E5%9E%8B%EF%BC%88ae%20epoll%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F%EF%BC%89.resources/40004D9B-10B6-44FE-AAB3-7AF28A907F46.png) 
 \

</div>

<div>

具体完整的交互过程是这样的：

</div>

<div>

（看一下初始化过程及对应的函数及事件的作用）

</div>

<div>

1、首先调用aeCreateEventLoop创建了一个事件循环。

</div>

<div>

2、通过anetTcpServer创建了一个TCP服务器，然后绑定端口并且监听对应的网络请求。

</div>

<div>

3、调用anetNonBlock将套接字的fd设置为非阻塞IO模式

</div>

<div>

4、然后将套接字的fd的"客户端连接"时间设置为acceptProc。（然后这个回调函数被aeCreateEventLoop中创建的事件循环使用）

</div>

<div>

5、当TCP连接建立之后，将触发acceptProc中的动作。

</div>

<div>

6、通过anetTcpAccept接受连接请求，然后更新套接字中的fd的"有数据可读取"事件的回调函数注册为readProc

</div>

<div>

7、当readProc被调用时，就可以直接取到可读取的数据，并且更新套接字中的fd的"有数据可写入"事件的回调函数writeProc

</div>

<div>

8、当EventLoop接收到"有数据可供写入"事件时，就会触发writeProc将数据发送给套接字对应的客户端。

</div>

<div>

大家可以下载下来看一下，Redis 源码的量其实还好。

</div>

<div>

wget <http://download.redis.io/releases/redis-4.0.1.tar.gz>

</div>

<div>

tar
-zxvf [redis-4.0.1.tar.gz](http://download.redis.io/releases/redis-4.0.1.tar.gz)

</div>

<div>

![](Redis%E7%9A%84%E4%BA%8B%E4%BB%B6%E6%A8%A1%E5%9E%8B%EF%BC%88ae%20epoll%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F%EF%BC%89.resources/1A56636B-E258-498E-8730-467F7A50CAC4.png) 
 

</div>

<div>

然后进到src
文件夹下，看一下server.c，下面是初始化过程的代码片段，L：1812

</div>

<div>

server.c
主函数中initServer调用了aeCreateEventLoop、anetTcpServer、anetNonBlock来完成初始化

</div>

<div>

![](Redis%E7%9A%84%E4%BA%8B%E4%BB%B6%E6%A8%A1%E5%9E%8B%EF%BC%88ae%20epoll%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F%EF%BC%89.resources/EEB7AB36-D60E-4FBA-AD73-89C95BB151EC.png) 
 \

</div>

<div>

\

</div>

<div>

处理"客户端连接"事件的函数也是在initServer中进行的 L:1897

</div>

<div>

![](Redis%E7%9A%84%E4%BA%8B%E4%BB%B6%E6%A8%A1%E5%9E%8B%EF%BC%88ae%20epoll%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F%EF%BC%89.resources/5A576E9B-B4A3-496D-BEF7-8D6185073BAE.png) 
 

</div>

<div>

\

</div>

<div>

当初始化完成之后，aeMain方法被调用

</div>

<div>

![](Redis%E7%9A%84%E4%BA%8B%E4%BB%B6%E6%A8%A1%E5%9E%8B%EF%BC%88ae%20epoll%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F%EF%BC%89.resources/3D947ABA-706D-4D61-BC58-CCF09C6B4A65.png) 
 

</div>

<div>

然后在aeMain方法中sProcessEvents方法被陆续的调用来处理各个时间。

</div>

<div>

很显然，在整个交互的过程中没有线程或者进程间的交互及其他线程的创建，这个模型其实就是一个轻量级的上下文切换的IO模型，并且上下文切换的代价还很小。但是Redis
因为是使用单线程进行交互的，一条命令没有处理完，就不能执行另一条命令，所以很容易出现延迟问题，但由于Redis
操作比较高效并且是内存操作，所以说这个问题其实还好啦，并且这种串行操作也完全的避免了并发问题。

</div>

<div>

基本上这就是Redis的事件模型～

</div>

<div>

\

</div>
