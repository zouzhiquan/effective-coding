---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-20 06:56:28 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战 \-- sync Map
updated: "2019-06-20 09:18:21 +0000"
---

:::  
#### 前言  

Java中线程安全的map主要有HashTable、ConcurrentHashMap，go中线程安全的Map就是sync.Map。在单协程访问时我们使用map就可以了，但是在多个协程并发访问时要使用协程安全的sync.Map，原生的map会报错。\
常见的并发安全的map实现思路有HashTable那种直接锁死函数，性能比较差，或者说老ConcurrentHashMap那样分段加锁，亦或者像是像是新concurrenthashmap那种cas操作。\
实际上sync.Map这三种思路都没选（因为多核情况下cache
contention，reflect.New、sync.RWMutex都比较慢），sync.Map追求更好的性能和稳定性,实现思路主要面向多读少写的情况，所以写性能其实比较一般。

#### 语法基础  

先来看一下sync.Map的API：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Map.resources/255C40A7-BD9A-48E7-860D-0C4C1873CB4D.png) 
 \
使用上比较简单，我们就直接调用这些API函数就可以了，这里不过多赘述了，我们不用像是cond那样显示的给它一把锁，因为cond是涉及锁外部的一些条件所以才这么做的。

#### 实现原理  

##### 整体结构&结构体构成  

先看一下sync.Map的的整体结构组成：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Map.resources/5C9D8638-4713-43FE-92A9-DA64743B6082.png) 
 \
然后根据上面那张图看一下sync.Map的结构体：

```  
type Map struct {
    mu Mutex
    read atomic.Value // readOnly
    dirty map[interface{}]*entry
    misses int
}
```

***mu*** 是map内部持有的锁，来同步协程之间的操作。\
***read***
包含一部分map的协程安全的信息（无论有没有加锁）。read因为是一个原子变量，本身就是协程安全的。read中存储的entry可以在没有mu的情况下并发地更新，但是需要将更新之前要被删除的entry复制到dirty中，并在可以在持有mu的情况下恢复。\
***dirty***
同样保存了一部分map的信息（操作的时候需要mu协同的部分）为了确保dirty可以快速升级为read
map，它还包括read map中所有未删除的条目。\
被删除的entry不存储在dirty中。clean
map中的被删除的entry必须是可恢复的，在新值覆盖前存放到dirty中。\
***missed*** 是记录没命中read的次数。\
***entry***
保存的是一个指针的值，指向数据，但是有两个特殊值`nil` ,nil表示在read中被删除了，但是dirty中还在，所以能直接更新值，expunged代表数据在ditry中已经被删除了，更新值的时候要先把这个entry复制到dirty。

##### 操作函数  

关于操作函数这里就仅介绍下写入、读取、删除三个核心函数：\
首先是写入：

```  
// Store sets the value for a key.
func (m *Map) Store(key, value interface{}) {
    // 先检查是否已经存在该元素，存在的话，直接通过read中的entry来更新值；
    read, _ := m.read.Load().(readOnly)
    if e, ok := read.m[key]; ok && e.tryStore(&value) {
    // tryStore 通过atomic的cas来解决冲突，如果发现数据被置为expung，tryStore不写入数据，直接返回false
        return
    }
/**在read中不存在，先上锁：
1、double check发现read中存在的话，entry为expunged，尝试把expunged替换成nil，如果entry.p==expunged则复制到dirty中，再写入值；否则不用替换直接写入值。
2、dirty中存在：直接更新
3、dirty中不存在：如果dirty为空，那么需要将read复制到dirty中，最后再把新值写入到dirty中。复制的时候调用的是dirtyLocked()，在复制到dirty的时候，read中为nil的元素，会更新为expunged，并且不复制到dirty中。
**/
    m.mu.Lock()
    read, _ = m.read.Load().(readOnly)
    if e, ok := read.m[key]; ok {
        if e.unexpungeLocked() {
            m.dirty[key] = e
        }
        e.storeLocked(&value)
    } else if e, ok := m.dirty[key]; ok {
        e.storeLocked(&value)
    } else {
        if !read.amended {
            m.dirtyLocked()
            m.read.Store(readOnly{m: read.m, amended: true})
        }
        m.dirty[key] = newEntry(value)
    }
    m.mu.Unlock()
}
```

读取：

```  
func (m *Map) Load(key interface{}) (value interface{}, ok bool) {
// 1、先读read
    read, _ := m.read.Load().(readOnly)
    e, ok := read.m[key]
    if !ok && read.amended {
// 2、如果read中没有，则加锁读dirty
        m.mu.Lock()
        read, _ = m.read.Load().(readOnly)
        e, ok = read.m[key]
        if !ok && read.amended {
            e, ok = m.dirty[key]
// 调用missLocked，递增misses，如果misses>len(dirty)，那么把dirty提升为read，清空原来的dirty
            m.missLocked()
        }
        m.mu.Unlock()
    }
    if !ok {
        return nil, false
    }
    return e.load()
}
```

删除：

```  
// Delete deletes the value for a key.
func (m *Map) Delete(key interface{}) {
// 检查read中是否存在
    read, _ := m.read.Load().(readOnly) 
    e, ok := read.m[key]
    if !ok && read.amended {
        m.mu.Lock() // 上锁
        read, _ = m.read.Load().(readOnly)
        e, ok = read.m[key] // 双检查
        if !ok && read.amended {
            delete(m.dirty, key) // 如果没有直接，删除dirty中的数据
        }
        m.mu.Unlock()
    }
    if ok {
        e.delete() // 如果存在，read中的pointer置为nil，并且删除dirty数据
    }
}
```

关于sync.Map暂时先介绍这些，关于更加详细实现大家可以直接对照源码看一下：`src/sync/map.go` 
:::

 
