---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-08-30 06:03:38 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go panic & recover
updated: "2019-08-30 07:33:05 +0000"
---

:::  
### 前言  

之前针对于go
的错误和异常做了简单的介绍，对于panic介绍的不算多，本篇从原理和源码的角度来看一下panic
和 recover是怎么运作的。\
panic 是一种不可预料的错误，会导致进程直接退出，跟c++
中的core比较类似，发生panic
会把发生问题时那个点的堆栈信息完整的打印到标准输出中，然后崩溃退出。\
在使用go时，panic是非常危险的，即使你的程序有supervise之类的守护进程，不断的挂掉重启，也会严重的影响程序的可用性，通常来说我们使用recover来进行panic的捕获，来阻止程序崩溃。

### 基础使用  

先来看一下demo：

```  
func test() {
    defer func() {
        // do something
        fmt.println("c")
        if err:=recover();err!=nil{
            fmt.println("d")
            fmt.Println(err) // 这里的err其实就是panic传入的内容
        }
    }()
    fmt.println("a")
    // do something maybe panic
    panic("panic")
    fmt.println("b")
}
```

这里程序的输出顺序是：a\\c\\d\\panic\
panic
发生时，会直接从当前行跳出，如果有defer的recover将会被拦住，执行defer中的内容。

通常来说，panic一般是由一些运行时错误导致的，比如说数组越界、空指针等。针对这类问题：\
1、写代码时要谨慎处理，避免发生panic，\
2、要有recover来阻止panic 崩溃程序。

### 原理  

panic和recover关键字会在编译时被编译器转换为OPANIC、ORECOVER类型的节点，然后进一步转换成gopanic、gorecover两个运行时的函数调用。\
先来看一下panic的数据结构：\
`src/runtime/runtime2.go` 

```  
//go:notinheap
type _panic struct {
    argp      unsafe.Pointer 
    arg       interface{}    
    link      *_panic        
    recovered bool           
    aborted   bool           
}
```

每次发生panic函数的调用时。都会创建上述结构体的一个实例来存储相关的信息和结构。\
其中：\
`argp` 
只想defer调用时参数的指针\
`arg` panic的入参\
`link` 指向更早调用的_panic的实例
（很显然panic出现时是一个异常链）\
`recoveres` 表示当前是否被恢复（recover）\
`aborted` 是否被强行终止

#### panic 终止进程  

没有被recover的panic会导致程序直接退出，主要在`gopanic` 中做了这件事。\
继续看源码：\
`src/runtime/runtime2.go l:445` 

```  
func gopanic(e interface{}) {
    gp := getg()
    if gp.m.curg != gp {
        print("panic: ")
        printany(e)
        print("\n")
        throw("panic on system stack")
    }

    if gp.m.mallocing != 0 {
        print("panic: ")
        printany(e)
        print("\n")
        throw("panic during malloc")
    }
    if gp.m.preemptoff != "" {
        print("panic: ")
        printany(e)
        print("\n")
        print("preempt off reason: ")
        print(gp.m.preemptoff)
        print("\n")
        throw("panic during preemptoff")
    }
    if gp.m.locks != 0 {
        print("panic: ")
        printany(e)
        print("\n")
        throw("panic holding locks")
    }

    var p _panic
    p.arg = e
    p.link = gp._panic
    gp._panic = (*_panic)(noescape(unsafe.Pointer(&p)))

    atomic.Xadd(&runningPanicDefers, 1)

    for {
        d := gp._defer
        if d == nil {
            break
        }

        if d.started {
            if d._panic != nil {
                d._panic.aborted = true
            }
            d._panic = nil
            d.fn = nil
            gp._defer = d.link
            freedefer(d)
            continue
        }

        d.started = true
        
        d._panic = (*_panic)(noescape(unsafe.Pointer(&p)))

        p.argp = unsafe.Pointer(getargp(0))
        reflectcall(nil, unsafe.Pointer(d.fn), deferArgs(d), uint32(d.siz), uint32(d.siz))
        p.argp = nil

        if gp._defer != d {
            throw("bad defer entry in panic")
        }
        d._panic = nil
        d.fn = nil
        gp._defer = d.link

        pc := d.pc
        sp := unsafe.Pointer(d.sp) 
        freedefer(d)
        if p.recovered {
            atomic.Xadd(&runningPanicDefers, -1)

            gp._panic = p.link
            for gp._panic != nil && gp._panic.aborted {
                gp._panic = gp._panic.link
            }
            if gp._panic == nil { 
                gp.sig = 0
            }
            gp.sigcode0 = uintptr(sp)
            gp.sigcode1 = pc
            mcall(recovery)
            throw("recovery failed") 
        }
    }

    preprintpanics(gp._panic)

    fatalpanic(gp._panic) 
    *(*int)(nil) = 0     
}
```

1、首先对内部变量还有抢锁的情况做了check。\
2、获取当前的goroutine\
3、创建一个_panic实例\
4、从当前的goroutine中获取一个_defer结构体\
5、如果_defer存在，调用reflectcall执行_defer中的代码\
6、将下一个的_defer结构设置到 Goroutine 上并回到 4\
7、调用fatalpanic中止整个程序\
其中，在fatalpanic中止整个程序之前就会通过printpanics打印出全部的panic消息以及调用时传入的参数

```  
func preprintpanics(p *_panic) {
    defer func() {
        if recover() != nil {
            throw("panic while printing panic value")
        }
    }()
    for p != nil {
        switch v := p.arg.(type) {
        case error:
            p.arg = v.Error()
        case stringer:
            p.arg = v.String()
        }
        p = p.link
    }
}

func printpanics(p *_panic) {
    if p.link != nil {
        printpanics(p.link)
        print("\t")
    }
    print("panic: ")
    printany(p.arg)
    if p.recovered {
        print(" [recovered]")
    }
    print("\n")
}
```

fatalpanic会调用exit来退出程序，并且返回错误码2.

```  
func fatalpanic(msgs *_panic) {
    pc := getcallerpc()
    sp := getcallersp()
    gp := getg()
    var docrash bool
    systemstack(func() {
        if startpanic_m() && msgs != nil {
            atomic.Xadd(&runningPanicDefers, -1)

            printpanics(msgs)
        }

        docrash = dopanic_m(gp, pc, sp)
    })

    if docrash {
        crash()
    }

    systemstack(func() {
        exit(2)
    })
    *(*int)(nil) = 0 // not reached
}
```

#### recover 恢复程序  

上面介绍了panic崩溃程序的过程，接下来看一下recover阻止崩溃，恢复程序的过程。\
看一下gorecover 函数：

```  
func gorecover(argp uintptr) interface{} {
    gp := getg()
    p := gp._panic
    if p != nil && !p.recovered && argp == uintptr(p.argp) {
        p.recovered = true
        return p.arg
    }
    return nil
}
```

这个函数非常简单，修改panic结构体的recovered字段，当前函数的调用其实都发生在gopanic期间。\
然后后期检测这个字段的时候，就不崩溃了（看一下gopanic函数就比较清晰了）

```  
     if p.recovered {
            atomic.Xadd(&runningPanicDefers, -1)

            gp._panic = p.link
            for gp._panic != nil && gp._panic.aborted {
                gp._panic = gp._panic.link
            }
            if gp._panic == nil { 
                gp.sig = 0
            }
            gp.sigcode0 = uintptr(sp)
            gp.sigcode1 = pc
            mcall(recovery)
            throw("recovery failed") 
        }
```

从_defer结构体中取出了程序计数器pc和栈指针sp并调用recovery方法进行调度，调度之前会准备好sp、pc以及函数的返回值。\
这一块儿就是panic和recover的过程啦。
:::

 
