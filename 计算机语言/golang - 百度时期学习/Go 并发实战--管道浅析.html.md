---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-24 06:14:19 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战\--管道浅析
updated: "2019-06-17 15:49:42 +0000"
---

:::  
在讲 channel 之前，有必要先提一下 CSP 模型，传统的并发模型主要分为
Actor模型和CSP模型，CSP 模型(communicating sequential
processes)由并发执行实体(进程、线程或协程)，和消息通道组成，实体之间通过消息通道发送消息进行通信。\
和 Actor 模型不同，CSP
模型关注的是消息发送的载体，而不是发送消息的执行实体。Go
语言的并发模型就参考了 CSP
理论，其中执行实体对应的是go协程，消息通道指的就是channel。

管道是Go语言在语言级别上提供的goroutine间的`通讯方式` 的通讯方式，是不支持跨进程通信的，如果需要进程间通讯的话，可以使用类似于Socket、管道、信号量等进程间通信方式，关键字是chan。\
channel定义方式：

```  
var ch chan int // 定义了一个int类型的管道ch
var ch = make(chan int) // make了一个int 类型的管道ch
var ch = make(chan int, 10)// make了一个int 类型的size为10的管道ch
```

channel的使用：

```  
var ch = make(chan int)
var intValue = 2
ch <- value // 传入管道
intResult <- ch // 管道输出
close(ch)  // 关闭管道
```

使用上来看就这么简单，那channel
有什么作用，如果有Java或者c的并发编程经验就简单的多了，我们可以使用chan的读写操作来阻塞go协程。看一个demo：

```  
func runFunc(ch chan int) {
    result := 0
    // do something
    ch <- result // 写chan
}

func main() {
    chs := make([]chan int)
    for i:=0; i<len(chs); i++ {
        chs[i] := make(chan int)
        go runFunc(chs[i])
    }
    for i:=0; i<len(chs); i++ {
    result := <- chan
    }
}
```

这样就相当于一次性起了n个协程去做事情，main
会因为读chan阻塞，事情完成后放行，有没有很像Java
中的CyclicBarrier或者C++&Java
中的Join，虽然可以这么用来当作协程间的协作，其实channel核心作用其实还是通信作用。

#### channel 缓冲  

上面提到的channel其实是一种无缓冲的管道。那具体来说什么是缓冲呢？\
使用channel就是经典的生产者消费者模式，缓冲可以理解为channel的一种资源池，缓冲的大小就是资源池的容量，有资源时才能取资源，有空闲时才能放资源，定义方式就是上文提到：`var ch = make(chan int, 10)` \
go的channel实际上就是阻塞队列的结构:\
**当缓冲区为0时（无缓冲）**\
当channel有元素时，只能读了才能继续写，\
当channel无元素时，只有写了才能读。

**当缓冲区不为0时（有缓冲）**\
当channel有元素时，但缓冲区有空余位置，写操作不会被阻塞，缓冲区无空闲位置才会阻塞。（可以理解为同样是阻塞结构但容量是
size（缓冲区） + 1）\
当channel无元素时，只有写了才能读。\
鉴于这种阻塞行为，chan
使用不正确是会容易导致死锁的，比如说一个正在读，但某些原因没有进行写操作。类似的情况有不少，使用的时候一定要注意。

> merge chan
>
> > merge 操作在chan的使用中非常常见，通常把多个chan
> > merge到同一个chan中进行处理。

```  
// 我们可以开一个 for循环来merge，也可以像下面这样使用go协程来merge
func Merge(chns ...<-chan int) <-chan int {
    outChn := make(chan int)
    go func() {
        var wg sync.WaitGroup
        wg.Add(len(chns))
        for _, chn := range chns {
            go func(chn <-chan int) {
                for v := range chn {
                    outChn <- v
                }
                wg.Done()
            }(chn)
        }
        wg.Wait()
        close(outChn)
    }()
    return outChn
}

// 利用反射来merge
func Merge(chns ...<-chan int) <-chan int {
    outChn := make(chan int)
    go func() {
        defer close(outChn) // defer 关键字是指运行结束时指定的动作
        var selectcCases []reflect.SelectCase
        for _, chn := range chns {
            selectcCases = append(selectcCases, reflect.SelectCase{
                Dir:  reflect.SelectRecv,
                Chan: reflect.ValueOf(chn),
            })
        }
        for len(selectcCases) > 0 {
            i, v, ok := reflect.Select(selectcCases)
            if !ok {
                cases = append(selectcCases[:i], selectcCases[i+1:]...)
                continue
            }

            outChn <- v.Interface().(int)
        }
    }()
    return outChn
}
```

#### channel close  

chan
在使用完成时是需要关闭的，但是当重复关闭或者向一个已关闭的channel写入数据时是会导致panic发生的。这就涉及一个优雅关闭的问题。\
我们有如下方式来避免上面两种方式的发生：

> closed() 检查chan是否已经关闭。
>
> > 这种方式是有问题的，不是closed有问题，而是使用closed的方式必然会产生问题，有并发编程经验的同学很容易发现这是种竞态条件（先检查后操作），当检查完chan状态时，发现是未关闭，然后在进行操作时，chan已经变成了关闭状态。

> 不要在接收方关闭chan、不要在多个发送端执行关闭
>
> > 这个看上去实现是比较困难的，因为需要依赖独立sender或者最后一个活跃的sender来关闭chan，独立sender还好，主要是最后一个活跃的sender不是很好控制。但是我们可以通过现有`sync` 

```  
var once sync.Once
once.Do(func(){
        close(channnel)
    })
var mutex sync.Mutex
mutex.Lock()
if !mc.closed {
    close(channnel)
    mc.closed = true
}
mutex.Unlock()
```

如果觉着上面的那种方式不够优雅，就以`不要在接收方关闭chan、不要在多个发送端执行关闭` 
为原则，创建一种chennel的通信机制来控制了，比如说："停止发送消息"，所有结束后再关闭。\
详细的地方，可以参考一下源码：`src/runtime/chan.go` \
channel
主要由一个环形队列主要存储消息元素，两个链表实现的goroutine等待队列（用于存储阻塞在recv、send操作上的goroutine）、一个互斥锁用于各个属性更新的同步。

```  
const (
    maxAlign  = 8
    hchanSize = unsafe.Sizeof(hchan{}) + uintptr(-int(unsafe.Sizeof(hchan{}))&(maxAlign-1))
    debugChan = false
)

type hchan struct {
    qcount   uint           // total data in the queue
    dataqsiz uint           // size of the circular queue
    buf      unsafe.Pointer // points to an array of dataqsiz elements
    elemsize uint16
    closed   uint32
    elemtype *_type // element type
    sendx    uint   // send index
    recvx    uint   // receive index
    recvq    waitq  // list of recv waiters
    sendq    waitq  // list of send waiters
    lock mutex
}

type waitq struct {
    first *sudog
    last  *sudog
}
```

顺着源码往下读，是channel的make函数，函数实现比较简单，注意一点就是当元素不包含指针时会为整个hchan分配一段连续的内存空间。具体每一步是做什么看一下注释就可以了，但是本文中的注释删减了一部分，详细可以参考源码文件。

```  
func makechan(t *chantype, size int) *hchan {
    elem := t.elem

    // compiler checks this but be safe.
    if elem.size >= 1<<16 {
        throw("makechan: invalid channel element type")
    }
    if hchanSize%maxAlign != 0 || elem.align > maxAlign {
        throw("makechan: bad alignment")
    }

    if size < 0 || uintptr(size) > maxSliceCap(elem.size) || uintptr(size)*elem.size > maxAlloc-hchanSize {
        panic(plainError("makechan: size out of range"))
    }
    var c *hchan
    switch {
    case size == 0 || elem.size == 0:
        // Queue or element size is zero.
        c = (*hchan)(mallocgc(hchanSize, nil, true))
        // Race detector uses this location for synchronization.
        c.buf = c.raceaddr()
    case elem.kind&kindNoPointers != 0:
        // Elements do not contain pointers.
        // Allocate hchan and buf in one call.
        c = (*hchan)(mallocgc(hchanSize+uintptr(size)*elem.size, nil, true))
        c.buf = add(unsafe.Pointer(c), hchanSize)
    default:
        // Elements contain pointers.
        c = new(hchan)
        c.buf = mallocgc(uintptr(size)*elem.size, elem, true)
    }
    c.elemsize = uint16(elem.size)
    c.elemtype = elem
    c.dataqsiz = uint(size)

    if debugChan {
        print("makechan: chan=", c, "; elemsize=", elem.size, "; elemalg=", elem.alg, "; dataqsiz=", size, "\n")
    }
    return c
}
```

send
函数，当看send函数的时候就会发现上面提到的一些缓冲相关的及其他的细节都会有所体现，比如说：\
1、当有go协程阻塞在channel
recv操作上时，此时缓存队列如果为空，直接将消息复制给等待recv
的go协程，这样就只产生了一次复制。\
2、当channel的缓存队列满了的时候，将当前go协程阻塞并加入send队列。\
3、当channel缓存队列还有空间的时候，将数据放到队列中，等待接受，然后有recv的go协程，则将消息给从缓存队列取出给该recv的go
协程，总共会产生两次复制。

```  
func chansend(c *hchan, ep unsafe.Pointer, block bool, callerpc uintptr) bool {
    if c == nil {
        if !block {
            return false
        }
        gopark(nil, nil, waitReasonChanSendNilChan, traceEvGoStop, 2)
        throw("unreachable")
    }

    if debugChan {
        print("chansend: chan=", c, "\n")
    }

    if raceenabled {
        racereadpc(c.raceaddr(), callerpc, funcPC(chansend))
    }
    if !block && c.closed == 0 && ((c.dataqsiz == 0 && c.recvq.first == nil) ||
        (c.dataqsiz > 0 && c.qcount == c.dataqsiz)) {
        return false
    }

    var t0 int64
    if blockprofilerate > 0 {
        t0 = cputicks()
    }

    lock(&c.lock)

    if c.closed != 0 {
        unlock(&c.lock)
        panic(plainError("send on closed channel"))
    }

    if sg := c.recvq.dequeue(); sg != nil {
        // Found a waiting receiver. We pass the value we want to send
        // directly to the receiver, bypassing the channel buffer (if any).
        send(c, sg, ep, func() { unlock(&c.lock) }, 3)
        return true
    }

    if c.qcount < c.dataqsiz {
        // Space is available in the channel buffer. Enqueue the element to send.
        qp := chanbuf(c, c.sendx)
        if raceenabled {
            raceacquire(qp)
            racerelease(qp)
        }
        typedmemmove(c.elemtype, qp, ep)
        c.sendx++
        if c.sendx == c.dataqsiz {
            c.sendx = 0
        }
        c.qcount++
        unlock(&c.lock)
        return true
    }

    if !block {
        unlock(&c.lock)
        return false
    }

    // Block on the channel. Some receiver will complete our operation for us.
    gp := getg()
    mysg := acquireSudog()
    mysg.releasetime = 0
    if t0 != 0 {
        mysg.releasetime = -1
    }
    // No stack splits between assigning elem and enqueuing mysg
    // on gp.waiting where copystack can find it.
    mysg.elem = ep
    mysg.waitlink = nil
    mysg.g = gp
    mysg.isSelect = false
    mysg.c = c
    gp.waiting = mysg
    gp.param = nil
    c.sendq.enqueue(mysg)
    goparkunlock(&c.lock, waitReasonChanSend, traceEvGoBlockSend, 3)

    // someone woke us up.
    if mysg != gp.waiting {
        throw("G waiting list is corrupted")
    }
    gp.waiting = nil
    if gp.param == nil {
        if c.closed == 0 {
            throw("chansend: spurious wakeup")
        }
        panic(plainError("send on closed channel"))
    }
    gp.param = nil
    if mysg.releasetime > 0 {
        blockevent(mysg.releasetime-t0, 2)
    }
    mysg.c = nil
    releaseSudog(mysg)
    return true
}
```

剩下的源码部分就暂时先不介绍了，有兴趣的同学可以去看一下。\
关于channel 就暂时介绍这么多。
:::

 
