---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-17 11:22:13 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战\--协程浅析 二
updated: "2019-06-17 15:49:22 +0000"
---

:::  
#### 前言  

继续上一篇的内容，我们介绍了go协程的实现中的几个核心的对象，也说了他们之间是如何合作工作的。\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98--%E5%8D%8F%E7%A8%8B%E6%B5%85%E6%9E%90%20%E4%BA%8C.resources/28C3029B-4933-4AE7-9D3E-C514D5E6C33B.png) 
 \
下面来看一些go协程的使用：

```  
 go func() {
    fmt.Println("hello goroutine")
    }()
```

这样就完成了一个最简单的demo，相较而言go协程的使用同Java中线程的使用要简单太多了，至少少写了十几行有没有，也更加清晰了。

#### 并发编程模型  

就并发来说，通常分为`Actor模型` 这么几种\
***多线程共享内存:***\
并发是大多数语言中的实现，比如说Java、C++、python等。这种方式基本上是最原始的并发编程的形式，我们需要通过锁来协同各个线程，保证并发的安全性。在这个基础上语言提供了&我们也实现了不少的并发工具和线程安全的数据结构，比如说ConcurrentHashMap、CountDownLatch、ReentrantLock等，这种编程模式对于程序员要求较高，需要非常清楚所使用API的各个细节及线程&内存实现，平心而论学习成本&门槛较高。\
***Actor模型：***\
由于多线程共享内存的编程模式，编程复杂度较高，并且容易出问题，调试起来更加的费劲，所以产生了像是Actor、CSP这些并发模型。\
actor模型是处理并行计算的概念模型，它定义了系统部件行为和交互的一些规则，Actor模型内部的状态由它自己维护即它内部数据只能由它自己修改(通过消息传递来进行状态修改)，所以使用Actors模型进行并发编程可以很好地避免这些问题，Actor由状态(state)、行为(Behavior)和邮箱(mailBox)三部分组成。实现比较经典的有Erlang，Java中也有对应的实现。\
actor有这么几个好处：\
1、事件模型驱动\--Actor之间的通信是异步的\
2、强隔离性\--Actor中的方法不能由外部直接调用，所有的一切都通过消息传递进行的，从而避免了Actor之间的数据共享。\
3、位置透明\--无论Actor地址是在本地还是在远程机上对于代码来说都是一样的。\
4、轻量性\--Actor是非常轻量的计算单元，单个Actor仅占400多字节，只需少量内存就能达到高并发。\
缺点：\
***CSP 模型：***\
CSP的核心理念是通过消息传递的尝试交互而不是共享内存，关注点更是仅关心消息的传递方式，而不是关心发送方和接收方。Actor也是通过消息来进行通信的，但是csp和actor的差异在于CSP进程通常是同步的(即任务被推送进Channel就立即执行，如果任务执行的线程正忙，则发送者就暂时无法推送新任务)，Actor进程通常是异步的(消息传递给Actor后并不一定马上执行)。Actor
更加关注的是一个数据和行为封闭。\
go支持共享内存这种比较原始的方式，也支持CSP模型，go的官方建议是强力建议使用消息传递也就是CSP的模式完成并发的构建，原话是这么说的：\
`Do not communicate by sharing memory; instead, share memory by communicating.` \
"不要以共享内存的方式来通信，相反，要通过通信来共享内存。"\
go具有天然的并发性，为了保证在这个优势的基础上构建更加安全稳定的应用，go提出了这些建议。\
我们在使用go写成的时候也应该尽可能的遵循这些建议，关于管道的使用可以看channel那篇文章，后面也会有相应的实战。\
具体来说是这样的,下面来看一个很经典的生产者消费者的例子。

```  
package main

import "fmt"

func main() {
    var channel = make(chan int, 3) // 用于消息通信的管道
    var c2 = make(chan int) // 防止main提前退出的通道
    // 同时开启两个go协程创建生产者消费者，能大致模拟同步进行
    go func() { // 开一个协程用来启动生产者
        for i:=0; i<100 ; i++ {
            go produce(channel, i)
        }
    }()
    go func() { // 开一个协程用来启动消费者
        for i:=0; i<100 ; i++ {
            go consumer(channel)
        }
    }()
    
    <-c2 // 防止主线程直接结束
}
func produce(channel chan int, data int) {
    channel <- data
}
func consumer(channel chan int) {
    fmt.Println(<- channel)
}
```

解释一下上述的代码，producer通过channel向生产者发送产品，而consumer从channel来取产品，producer&consumer并没通过一个共享内存直接交互，而是通过这种发消息的方式来进行协同的，包括阻塞等待等。\
当然了，我们也可以用共享内存这种方式来完成并发编程，go也提供了对应的API：

```  
    sync.Mutex.Lock()
    if i >2 {
       i = i + 1
     } else {
       i = i + 2
     }
    sync.Mutex.Unlock()
```

像上述代码，就通过sync.Mutex的锁操作，完成了共享变量i的一个存在竞态条件的操作。\
剩下的go协程相关的就从后面并发编程的几个经典场景来理解吧，本篇暂时就先讲这么多。
:::

 
