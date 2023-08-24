---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-18 09:22:45 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战 \-- sync Mutex
updated: "2019-06-19 02:22:02 +0000"
---

:::  
#### 前言  

在并发编程中我们可以使用channel来协同各个goroutine，但是很多场景我们也是需要使用sync的比如说并发场景下计数器的使用等。\
go也为我们提供了一系列的API来协同我们的go协程：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Mutex.resources/C676A8A3-AB25-4506-8332-E279A56766CA.png) 
 \
整体来说语法非常简单，也很好用。

#### 互斥锁使用  

互斥锁是sync包中的核心，也是最常用的API之一，直接看一个demo：

```  
var lock = sync.Mutex{}
lock.Lock()
// do something
lock.Unlock()
```

mutex就实现了lock、unlock两个函数，跟Java中的ReentrantLock比较类似（Mutexx是不可重入的），锁仅与mutex对象有关，可以一个协程锁一个协程解锁。

#### 互斥锁实现  

##### 互斥锁工作状态  

Mutex有两种工作状态：normal和starvation。在normal模式下，新加入竞争锁队列的协程也会直接参与到锁的竞争中来，处于starvation模式下，所以新加入的协程将直接进入等待队列中挂起，直到其等待队列之前的协程全部执行完毕。这里两种模式，如果熟悉Java的话不难发现，就是个公平锁和非公平锁，但是和Java不同的是Go中的这两种模式是自动切换的：\
1、在normal模式下，协程的竞争等待时间如果大于1ms，就会进入starvation模式。\
2、starvation模式下，该协程是等待队列中的最后一个工作协程，或者它挂起等待时长不到1ms，则切换回normal模式。

##### 互斥锁结构体  

下面来看一下源码：(`src/sync/mutex.go` )

```  
// A Mutex is a mutual exclusion lock.
// The zero value for a Mutex is an unlocked mutex.
//
// A Mutex must not be copied after first use.
type Mutex struct {
    state int32
    sema  uint32
}

// A Locker represents an object that can be locked and unlocked.
type Locker interface {
    Lock()
    Unlock()
}
```

1、state存储Mutex的状态量，state不同位表示的含义不同：\
最低位(mutexLocked)用于表示是否上锁\
低二位(mutexWoken)用来表示当前锁是否唤醒\
低三位(mutexStarving)用来表示当前锁是否处于starvation模式\
剩下位数state\>\>mutexWaitershif(mutexWaitershif为3)用来表示当前被阻塞的协程数量。\
2、sema是一个信号量，是协程阻塞的依据。

##### Lock()  

下面来看一下lock函数，lock因为有normal、starvation两种模式在，锁的操作看起来会比较复杂，下面一点一点来看。源码中的注释也比较清晰，大家也可以直接看源码：\
`src/sync/mutex.go` 

```  
// Lock locks m.
// If the lock is already in use, the calling goroutine
// blocks until the mutex is available.
func (m *Mutex) Lock() {
    // 1、通过cas操作来完成state的状态变换（0->1）
    // 如果成功的话表示首次取锁成功，直接返回，否则继续
    if atomic.CompareAndSwapInt32(&m.state, 0, mutexLocked) {
        if race.Enabled {
            race.Acquire(unsafe.Pointer(m))
        }
        return
    }

    var waitStartTime int64
    starving := false
    awoke := false
    iter := 0
    old := m.state
    for {
        // 2、取锁失败代表已经有协程拿到锁了，如果是normal模式，且符合可以自旋的条件，如果可以自旋，在判断是否还有其他协程在等待，如果有的话，将锁改为唤醒状态（cas），然后将该协程自旋等待。
        if old&(mutexLocked|mutexStarving) == mutexLocked && runtime_canSpin(iter) {
            if !awoke && old&mutexWoken == 0 && old>>mutexWaiterShift != 0 &&
                atomic.CompareAndSwapInt32(&m.state, old, old|mutexWoken) {
                awoke = true
            }
            runtime_doSpin()
            iter++
            old = m.state
            continue
        }
        //自旋条件不符合 or 当前锁被释放 or 当前锁处于starvation模式，继续本次循环
        //如果这时有别的协程占用锁，或者当前处于starvation模式，则给state的线程数加1；
        //若当前处于starvating值为true，且别的协程占用锁，则把当前锁状态置为starvation模式；
        // 若之前自旋时将锁唤醒，于是把低二位置为0；然后通过cas将新的state赋值给state：
        //如果失败，那么继续重复之前的操作
        //如果成功，先判断当前协程阻塞时间是否为0，为0则从现在开始计时，通过runtime_SemacquireMutex()方法阻塞当前协程
        new := old
        if old&mutexStarving == 0 {
            new |= mutexLocked
        }
        if old&(mutexLocked|mutexStarving) != 0 {
            new += 1 << mutexWaiterShift
        }
        if starving && old&mutexLocked != 0 {
            new |= mutexStarving
        }
        if awoke {
            if new&mutexWoken == 0 {
                throw("sync: inconsistent mutex state")
            }
            new &^= mutexWoken
        }
        if atomic.CompareAndSwapInt32(&m.state, old, new) {
            if old&(mutexLocked|mutexStarving) == 0 {
                break // locked the mutex with CAS
            }
            queueLifo := waitStartTime != 0
            if waitStartTime == 0 {
                waitStartTime = runtime_nanotime()
            }
            runtime_SemacquireMutex(&m.sema, queueLifo)
            starving = starving || runtime_nanotime()-waitStartTime > starvationThresholdNs
            old = m.state
            if old&mutexStarving != 0 {

                if old&(mutexLocked|mutexWoken) != 0 || old>>mutexWaiterShift == 0 {
                    throw("sync: inconsistent mutex state")
                }
                delta := int32(mutexLocked - 1<<mutexWaiterShift)
                if !starving || old>>mutexWaiterShift == 1 {
                    delta -= mutexStarving
                }
                atomic.AddInt32(&m.state, delta)
                break
            }
            awoke = true
            iter = 0
        } else {
            old = m.state
        }
    }
    if race.Enabled {
        race.Acquire(unsafe.Pointer(m))
    }
}
```

##### UnLock()  

UnLock函数相对来说要简单不少，直接来看源码

```  
func (m *Mutex) Unlock() {
    if race.Enabled {
        _ = m.state
        race.Release(unsafe.Pointer(m))
    }
    // 首先把state的最低位设为0，表示已经解锁
    new := atomic.AddInt32(&m.state, -mutexLocked)
    if (new+mutexLocked)&mutexLocked == 0 {
        throw("sync: unlock of unlocked mutex")
    }
     //如果是normal模式下
    if new&mutexStarving == 0 {
        old := new
        for {
        // 如果当前没有阻塞协程，或者当前有协程在自旋获得锁，那么可以直接返回
            if old>>mutexWaiterShift == 0 || old&(mutexLocked|mutexWoken|mutexStarving) != 0 {
                return
            }
           // 更改state，阻塞协程数减1，调用runtime_Semrelease方法唤醒协程，仅仅是唤醒，仍然存在锁的竞争
            new = (old - 1<<mutexWaiterShift) | mutexWoken
            if atomic.CompareAndSwapInt32(&m.state, old, new) {
                runtime_Semrelease(&m.sema, false)
                return
            }
            old = m.state
        }
    } else { // starvation模式下，则直接调用runtime_Semrelease唤醒协程
        runtime_Semrelease(&m.sema, true)
    }
}
```

关于Mutex暂时先介绍这么多
:::

 
