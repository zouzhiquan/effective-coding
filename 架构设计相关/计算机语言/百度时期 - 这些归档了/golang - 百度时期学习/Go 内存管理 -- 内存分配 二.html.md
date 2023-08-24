---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-21 05:51:59 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 内存管理 \-- 内存分配 二
updated: "2019-06-24 09:18:58 +0000"
---

:::  
接上一篇，下面来看看内存分配的`初始化` 等。

#### 初始化  

首先会申请一段连续的内存空间以供使用，大小(64位机器上)512M(spans_mapped)+16G(bitmap_mapped)+512G(arena)。\
hmap初始化的源码在`src/runtime/proc.go` 中，大家可以参照本文看一下。\
***mheap初始化***

```  
func (h *mheap) init(spansStart, spansBytes uintptr) 
```

1、初始化空间\
2、mcentral初始化\
3、spans初始化\
mheap具体的初始化发生在`mallocinit()` 调用)：

```  
func mallocinit()
```

1、检查系统/硬件信息\
2、 计算预留空间大小\
3、尝试预留地址\
4、初始化mheap中的一部分变量\
5、其他部分初始化，67个mcentral在这里初始化\
***mcentral 初始化***\
1、设置自己的级别\
2、将两个mspanList初始化\
***mcache***

```  
func procresize(nprocs int32) *p
```

而mcache的初始化在`func procresize(nprocs int32)` 中调用，顺序在mallocinit()之后，所以说也就是说mcentral先初始化，然后是mheap.\
***allocmcache***\
allocmcache初始化比较简单，直接看下源码吧：

```  
func allocmcache() *mcache {
    lock(&mheap_.lock)
    c := (*mcache)(mheap_.cachealloc.alloc())
    unlock(&mheap_.lock)
    for i := 0; i < _NumSizeClasses; i++ {
        c.alloc[i] = &emptymspan
    }
    c.next_sample = nextSample()
    return c
}
```

这几个函数执行完，管理结构、mheap、67个mcentral及每个goroutine的mcache都初始化完毕。

#### 内存分配  

针对于不同大小的的对象，go的分配策略是不同的：\
(0, 16B) 且不包含指针的对象： Tiny分配\
(0, 16B) 包含指针的对象：正常分配

\[16B, 32KB\] : 正常分配\
(32KB, -) : 大对象分配\
Tiny分配和大对象分配都属于内存管理的优化范畴，这里就仅看正常分配。\
1、获取当前线程的私有缓存mcache\
2、跟据size计算出适合的class的id\
3、从mcache的alloc\[class\]数组中找到可用的span\
4、如果mcache没有可用的span，则从mcentral申请一个新的span加入mcache中。\
5、如果mcentral中也没有可用的span，则从mheap中申请一个新的span加入mcentral。（这里大家可以注意下mcache、mcentral、mheap的层级关系）\
6、从该span中获取到空闲对象地址并返回。\
go的内存分配非常复杂，中间还有很多GC的细节在里面，一言半语的也说不详细，大家可以对着简单的纲要直接看源码，注释也非常详细，一定要对照上一篇的图来理解go内存管理。
:::

 
