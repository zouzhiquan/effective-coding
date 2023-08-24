---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-19 03:47:37 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战 \-- sync WaitGroup
updated: "2019-06-20 02:50:12 +0000"
---

:::  
#### 前言  

waitgroup也是一个非常有用的并发工具，有点像是Java中的CyclicBarrier，只不过Go中的WaitGroup等待的是协程而已。\
通常来说，WaitGroup是go并发中最常用的工具了，在起协程并发做一些事儿，我们可以通过WaitGroup了表达这一组协程的任务是否完成，已决定是否继续往下走，或者取任务结果，下面来看一下WaitGroup的使用及实现。

#### 语法基础  

WaitGroup的核心关注点是：Add()、Done()、Wait()三个函数\
Add函数主要为WaitGroup的等待数+1或者+n\
Done函数调用的也是Add函数，主要用于-1操作\
Wait函数是指阻塞当前协程，直到等待数归为0才继续向下执行\
下面来看一个demo：

```  
func main() {
    waitGroup := &sync.WaitGroup{}
    DoSomething(waitGroup)
    waitGroup.Wait() // 这里会阻塞main，直到所有的任务都完成
    fmt.Println("end")
}

func DoSomething(waitGroup *sync.WaitGroup) {
    for i:=0;i <10;i++ {
        waitGroup.Add(1)
        go func(waitGroup *sync.WaitGroup) {
            fmt.Print("1-")
            defer waitGroup.Done()
        }(waitGroup)
    }
}
```

输出：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20WaitGroup.resources/AF174912-C3B4-4E29-B5CD-1E66A6067988.png) 
 

#### 实现原理  

首先来看WaitGroup的结构体：

```  
type WaitGroup struct {
    noCopy noCopy
    state1 [3]uint32
}
```

noCopy共64位，高32位是一个计数器，低32位代表有多少在等待，64位原子操作需要64位对齐，但32位编译器不能确保对齐。因此，我们分配12个字节，然后使用其中对齐的8个字节作为状态，另外4个字节作为SEMA的存储。\
state1 保存的是状态信息\
接下来看一下核心的两个函数Add()、Wait()\
***Add()：***\
1、添加将delta（可能为负）加到waitgroup计数器上。\
2、如果计数器变为零，则所有在等待时阻塞的goroutine都被释放。\
3、如果计数器为负，则直接panic。

需要注意的是：\
1、当计数器为零时发生的具有正增量的调用必须在等待之前发生，但任何时候都可能发生负增量的调用。这意味着要Add的函数调用应该在创建goroutine或等待的其他事件的语句之前执行。\
2、如果waitgroup被重用以等待几个独立的事件集，则必须在返回所有以前的wait调用之后进行新的add调用。

```  
func (wg *WaitGroup) Add(delta int) {
    statep, semap := wg.state()
    if race.Enabled {
        _ = *statep // trigger nil deref early
        if delta < 0 {
            // 与wait变为同步操作
            race.ReleaseMerge(unsafe.Pointer(wg))
        }
        race.Disable()
        defer race.Enable()
    }
    state := atomic.AddUint64(statep, uint64(delta)<<32)
    v := int32(state >> 32)
    w := uint32(state)
    if race.Enabled && delta > 0 && v == int32(delta) {
//第一个增量必须与wait同步。
//需要将其变为为一个读取，因为可以有几个从0开始的并发wg.counter转换。
        race.Read(unsafe.Pointer(semap))
    }
    if v < 0 {
        panic("sync: negative WaitGroup counter")
    }
    if w != 0 && delta > 0 && v == int32(delta) {
        panic("sync: WaitGroup misuse: Add called concurrently with Wait")
    }
    if v > 0 || w == 0 {
        return
    }
//当waiters>0时，此goroutine已将counter设置为0。
//现在状态不能同时突变：
//-添加不能与等待同时发生，
//-如果看到counter==0，则wait不会增加waiters。
//仍然执行简单的检查以检测WaitGroup的误用。
    if *statep != state {
        panic("sync: WaitGroup misuse: Add called concurrently with Wait")
    }
    // Reset waiters count to 0.
    *statep = 0
    for ; w != 0; w-- {
        runtime_Semrelease(semap, false)
    }
}
```

***Wait()：***\
Wait函数主要用于阻塞那些调用Wait的goroutine，直到waitgroup的任务计数器为0，才会放行，下面来看一下源码：

```  
func (wg *WaitGroup) Wait() {
    statep, semap := wg.state()
    if race.Enabled {
        _ = *statep
        race.Disable()
    }
    // 循环检查计数器情况
    for {
        state := atomic.LoadUint64(statep)
        v := int32(state >> 32)
        w := uint32(state)
        if v == 0 {
            // 如果技术为0的话就不用等待了，完成放行
            if race.Enabled {
                race.Enable()
                race.Acquire(unsafe.Pointer(wg))
            }
            return
        }
        // 如果有有新的调用wait函数的，添加新的等待者，等待者计数器+1
        if atomic.CompareAndSwapUint64(statep, state, state+1) {
            if race.Enabled && w == 0 {
            // 等待和新添加的是同步的
            // 只能为第一个竞争者进行写操作，否则的话会产生互相竞争
          race.Write(unsafe.Pointer(semap))
            }
            runtime_Semacquire(semap)
            if *statep != 0 {
                panic("sync: WaitGroup is reused before previous Wait has returned")
            }
            if race.Enabled {
                race.Enable()
                race.Acquire(unsafe.Pointer(wg))
            }
            return
        }
    }
}
```

关于WaitGroup暂时介绍这么多。
:::

 
