---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-18 10:25:58 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战 \-- sync RWMutex
updated: "2019-06-19 03:01:59 +0000"
---

:::  
#### 前言  

sync中包含Mutex、RWMutex两个排他锁，上一篇介绍了最基础的Mutex锁，本篇来说一下基于Mutex实现的RWMutex，RWMutex是一把读写锁，功能上跟Java中的读写锁比较相近，适用于多读少写的场景，而Mutex适用于读写次数不确定的场景。下面来看一下RWMutex的使用及实现。

#### 语法基础  

RWMutex 函数相对于Mutex来说稍微多一些：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20RWMutex.resources/C0E1A021-FA51-4858-A21A-BDF9C708CEFE.png) 
 \
Lock() : 写锁加锁\
UnLock():写锁释放锁\
RLock():读锁加锁\
RUnLock():读锁释放锁

```  
func Read(lock sync.RWMutex) {
    go func() {
        lock.RLock()
        // read
        lock.RUnlock()
    }()
}

func Write(lock sync.RWMutex) {
    go func() {
        lock.Lock()
        // write
        lock.Unlock()
    }()
}
```

读锁可以被获取多次，而写锁仅能被获取一次，读锁很像一个引用计数器，写锁其实就是个Mutex全局锁。\
这里有个问题：go中的这些锁都是不可重入的，这一点跟Java差异非常大，因为go作者认为需要可重入本身就是代码写的有问题。\
除了重入这事儿，但凡涉及读写锁肯定就有锁升级锁降级这些问题。go中的锁不能重入，也不允许获取读锁后再拿写锁（Java会导致死锁，而go是直接不允许），拿到写锁再拿读锁也是不可以的。\
可以写几个简单的demo测试一下：

```  
// fatal error: all goroutines are asleep - deadlock!
func TestLockUp(lock sync.RWMutex) {
    lock.RLock()
    fmt.Println("read lock sucess")
    lock.Lock()
    fmt.Println("write lock success")
    lock.Unlock()
    lock.RUnlock()
}

// fatal error: all goroutines are asleep - deadlock!
func TestLockDown(lock sync.RWMutex) {
    lock.Lock()
    fmt.Println("write lock success")
    lock.RUnlock()
    fmt.Println("read lock sucess")
    lock.RUnlock()
    lock.Unlock()
}

// 相当于加了两遍读锁
func TestReRLock(lock sync.RWMutex) {
    lock.RLock()
    fmt.Println("read lock 1 success")
    lock.RLock()
    fmt.Println("read lock 2 sucess")
    lock.RUnlock()
    lock.RUnlock()
}
// fatal error: all goroutines are asleep - deadlock!
func TestReLock(lock sync.RWMutex) {
    lock.Lock()
    fmt.Println("write lock 1 success")
    lock.Lock()
    fmt.Println("write lock 2 sucess")
    lock.RUnlock()
    lock.RUnlock()
}
```

为了避免一直有读锁占锁，写锁加不上导致的饥饿问题，go设计为写锁优先级较高，有写锁等待时，优先加写锁，具体实现是这么做的：\
如果在添加写锁之前已经有其他的读锁和写锁，则lock就会阻塞直到该锁可用，为确保该锁最终可用，已阻塞的
Lock
调用会从获得的锁中排除新的读取器（已有的读锁加了也就加了，有写锁请求后会优先加写锁，读锁先等等）

关于RWMutex的实现，比较简单并且源码中的注释也非常详尽，核心的加锁逻辑主要是Mutex中实现的，这里就不过多赘述了，大家看源码就好了`src/sync/rwmutex` ，关于RWMutex暂时先介绍这么多。
:::

 
