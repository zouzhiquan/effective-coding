---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-20 11:32:34 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 并发实战 \-- sync Pool
updated: "2019-06-20 13:04:40 +0000"
---

:::  
#### 前言  

sync包中最后一个要说的API是sync.Pool，本质上来说Pool应该不算是sync并发相关的内容，但是既然放在sync里了，并且在并发场景下共用一些对象其实还挺常见的，就一起说一下吧。\
Pool目的是对象的共用，解决的是减少内存的占用及GC频发带来的性能损耗，对于一些大对象，或者构造代价高的对象还是很有必要通过pool来缓存的。

#### 语法基础  

Pool的API非常简单：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Pool.resources/F1D18A7E-F1DE-4291-9AF2-8F60FF962954.png) 
 \
通常来说一个pool存放一个type或者一个interface的对象。\
Get函数用于取对象（取的顺序随机）\
Push函数用于添加对象\
假设现在池子是空的Push一个对象，然后Get取出来的就是这个对象，如果再执行get取到的就是nil了，所以我们用完之后记得push进去，来保证后续还能继续使用。\
Pool的大小是不做限制的，仅与内存限制有关，如果想做一个有尺寸的池子，在上层封装一下就好了。\
来看个demo：

```  
func main() {
    pool := sync.Pool{}
    pool.Put("1")
    pool.Put("2")
    pool.Put("3")
    pool.Put("4")
    pool.Put("5")
    fmt.Println(pool.Get())
    fmt.Println(pool.Get())
    fmt.Println(pool.Get())
    fmt.Println(pool.Get())
    fmt.Println(pool.Get())
    fmt.Println(pool.Get())
}
```

输出：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Pool.resources/AC21567F-241D-49EE-AD75-A5760207037D.png) 
 \
其实我们可以配合Cond 完成一个经典的使用：

```  
func main() {
    lock := &sync.Mutex{}
    cond := sync.NewCond(lock)
    pool := &sync.Pool{}
    produce(cond, pool)
    for i:=0; i<12; i++ {
        consume(cond, pool, i)
    }
    time.Sleep(102*time.Second)
}
func consume(cond *sync.Cond, pool *sync.Pool, i int) {
    go func() {
        for true {
            cond.L.Lock()
            v := pool.Get()
            for ; v == nil; v= pool.Get() {
                cond.Wait()
            }
            if v != nil {
                fmt.Println("consumer "+strconv.Itoa(i)+" consume: ", v)
            }
            cond.L.Unlock()
            pool.Put(v)
            cond.Broadcast()
            time.Sleep(1*time.Second)
        }
    }()
}
func produce(cond *sync.Cond, pool *sync.Pool) {
    go func() {
        for i:=0; i<6; i++ {
            time.Sleep(1*time.Second)
            fmt.Println("produce: ", i)
            pool.Put(i)
            cond.Broadcast()
        }
    }()
}
```

输出：\
![](Go%20%E5%B9%B6%E5%8F%91%E5%AE%9E%E6%88%98%20--%20sync%20Pool.resources/9FC5B22E-5445-462B-ADAA-59AD9BCCED13.png) 
 \
看完这个demo，应该就对于pool及cond的使用就更加清晰了\
关于Pool的实现大家看看源码就可以了，注释写的非常详细，就不过多赘述了：`src/sync/pool.go` \
关于sync.Pool暂时就先介绍这么多。
:::

 
