---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-20 02:51:41 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战 \-- sync Cond
updated: "2019-06-20 06:50:21 +0000"
---

:::  
#### 前言  

go中的sync.Cond也就是condition，是一个条件同步变量，与Java中Object的wait、notify、notifyAll方法或者Condition类的作用比较类似，如果有这方面的基础学习起来会非常简单。其实Java中的JUC包实现的可以是最丰富和易用的了，熟知JUC的话，学习其他语言的并发特性及工具的话会非常简单。

#### 语法基础  

sync.Cond同其他并发条件变量一样，提供了阻塞和唤醒函数：\
Wait() 阻塞操作\
Signal() 唤醒一个协程\
Broadcast() 唤醒所有协程\
不同的Cond需要我们制定一把锁，通常是Mutex、RWMytex，当然也可以是你自己实现的锁。\
下面来看一下sync.Cond的使用：

```  
func main() {
    lock := &sync.Mutex{}
    cond := sync.NewCond(lock)
    for i:=0; i<10; i++ {
        runGorotine(cond, i)
    }
    time.Sleep(1*time.Millisecond)
    fmt.Println("----------------------------: signal 唤醒单个")
    cond.Signal()
    time.Sleep(1*time.Millisecond)
    fmt.Println("----------------------------: broadcast 唤醒全部")
    cond.Broadcast()
    time.Sleep(2*time.Second)
}

func runGorotine(cond *sync.Cond, i int) {
    go func(cond *sync.Cond, i int) {
        cond.L.Lock()
        for condition() {
            fmt.Println("-goroutine-" + strconv.Itoa(i) + " 命中wait")
            cond.Wait()
        }
        fmt.Println("-goroutine-" + strconv.Itoa(i) + " 命中条件")
        cond.L.Unlock()
    }(cond, i)
}

func condition() bool {
    rand.Intn(50)
    if rand.Intn(50) > 20 {
        fmt.Print(true)
        return true
    }
    fmt.Print(false)
    return false
}
```

输出：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Cond.resources/DD9064D0-2858-474E-BB6E-3E7831DE58CC.png) 
 \
ps：go
协程之后启动后并不是立即执行的,需要有一定的分配过程及等待的时间，所以说sleep一小段时间，否则唤醒通知在wait之前发生就没有意义了。\
上述就是Cond的最简单的使用，生产环境比这个demo要复杂一些，但是大致也就这样了。

#### 实现原理  

Cond的实现非常简单，锁操作依赖的是我们创建的lock。然后依赖于runtime阻塞和唤醒go协程的函数。

```  
type Cond struct {
// 这个已经不是第一次见了，第一次使用后就不能copy了
    noCopy noCopy
    L Locker // 创建cond是传入的锁
    notify  notifyList // 调用runtime阻塞和唤醒的函数，内部维护了一个阻塞链表
    checker copyChecker // 保留指向自身的指针以检测对象复制。
}
// 构造函数
func NewCond(l Locker) *Cond {
    return &Cond{L: l}
}
```

wait函数的锁逻辑有点奇怪，其实主要是为了把外面条件判断和添加阻塞队列变为一个原子操作，这种锁的使用方式其实不太建议，比较容易出问题。

```  
func (c *Cond) Wait() {
    c.checker.check()
    t := runtime_notifyListAdd(&c.notify) // 添加阻塞列表
    c.L.Unlock() // 函数调用前已经加锁了，在添加阻塞队列后就可以撤销锁了
    runtime_notifyListWait(&c.notify, t) // 进行阻塞
    c.L.Lock() // 外层还有个解锁操作，加把锁
}
```

下面是唤醒操作：

```  
func (c *Cond) Signal() {
    c.checker.check()
    runtime_notifyListNotifyOne(&c.notify) 
    // 唤醒一个
}

func (c *Cond) Broadcast() {
    c.checker.check()
    runtime_notifyListNotifyAll(&c.notify)
    // 唤醒多个
}
```

关于Cond的使用及源码实现暂时介绍这么多。
:::

 
