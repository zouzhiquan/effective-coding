---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-19 03:22:53 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战 \-- sync Once
updated: "2019-06-19 03:47:32 +0000"
---

:::  
#### 前言  

Once是一个非常实用的API，它保证了一个事情仅做一次，这个在许多场景非常有用，所以Once也是go提供的为数不多的API之一。

#### 语法基础  

Once的语法非常简单，整个API就提供了一个Do函数，Do函数接受的是一个函数对象，通过once.Do可以保证这个事情仅做一次，来看一个demo：

```  
func main() {
    var once = sync.Once{}
    once.Do(say)
    once.Do(say)
    once.Do(say)
    once.Do(say)
}

func say() {
    fmt.Println("say once")
}
```

输出：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Once.resources/23D8E7FD-8942-4F2A-A996-61EA51C1931B.png) 
 

#### 源码实现  

// Once is an object that will perform exactly one action.

```  
type Once struct {
    m    Mutex // 一把锁
    done uint32 // 是否已经做过的标记
}

func (o *Once) Do(f func()) {
// 如果已经做过了直接返回，这里使用atmoic保证并发场景下的安全性
    if atomic.LoadUint32(&o.done) == 1 {
        return
    }
// 这里有一个先判断后操作的竞态条件，所以锁了一下
    o.m.Lock()
    defer o.m.Unlock()
    if o.done == 0 {
        // 将状态标记为已完成，这个过程还是在锁内部
        defer atomic.StoreUint32(&o.done, 1)
        f()
    }
}
```

ps：源码中有一个细节点，如果声明了多个defer，按照定义顺序，倒序执行，这也就是为什么最终把状态置为已完成是发生在锁内部了。\
关于Once的内容挺简单的，暂时先介绍这么多。
:::

 
