---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-30 04:07:15 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 语言基础\--错误&异常浅析
updated: "2019-06-17 15:55:30 +0000"
---

:::  
如果go是你的第一门语言，go的异常和错误体系可能比较容易接受，但如果你有一定的Java或者c++基础，go的异常和错误体系可能会比较不适应。\
go的错误及异常体系也同样的追求简洁优雅，它摒弃了Java或者c++
中的try-catch-finally模式，通过返回值的形式来表示错误，因为go认为try-catch会干扰程序的正常的控制流程，所以通过返回值的性质，认为错误其实是程序运行过程中的重要组成部分。\
除此之外go把错误也异常分开了，真正的异常是指程序已经无法向下执行，需要由服务来进行特殊处理，在go中的表现形式是defer、panic、recover。

#### 错误  

##### 使用  

go为我们提供了一个错误接口、接口中包含一个描述错误信息的
`Error()` 方法：

```  
type error interface {
    Error() string
}
```

比如说类型转换就是一个经典的场景：

```  
var a = "1"
aInt, err := strconv.Atoi(a)
```

这部分错误通常是可以接受的，所以可以为这是函数正常返回之一，我们也应该对于这种情况进行自身的处理，比如说当转换异常时说明参数不合法我们应该拒绝这个操作或者给它一个默认值，并且我们可以通过`err.Error()` 来拿到错误信息，给出程序的一些警报。而不是说通过像Java一样的try-catch、throw来制造一个异常流，这样异常流+流程控制两个维度叠加在一起相对来说是较难读懂的。\
除此之外，我们可以通过之前介绍的接口的方式来定义自己的错误类型，来完善正常的业务逻辑，像下面这样，我们就可以使用自身的错误类型了，这里有点像是Java中摆脱了try-catch的Exeception。

```  
type Operation struct {...}
func (op Operation) Error() string {
 // Do something
}

// 定义完自己异常类型后我们可以把这个err 返回值，当作正常结果进行逻辑处理了（当然了，原生的error也可以这样用，但是功能或者错误信息有一定的局限）
if err != nil {
    switch err {
    case Err1:
        // Do something
        return
    case Err2:
        // Do something
        return
    default:
        // Do something
        return
    }
}
```

##### 实现  

go标准库关于error的实现也蛮简单的，源码位于src/errors包下\
![](Go%20%E8%AF%AD%E8%A8%80%E5%9F%BA%E7%A1%80--%E9%94%99%E8%AF%AF&%E5%BC%82%E5%B8%B8%E6%B5%85%E6%9E%90.resources/2C03C049-205B-4E2C-8107-FDAA02068599.png) 
 \
标准库中对于Error接口做了一个基础的实现叫做errorString

```  
// New returns an error that formats as the given text.
func New(text string) error {
    return &errorString{text}
}
// errorString is a trivial implementation of error.
type errorString struct {
    s string
}
func (e *errorString) Error() string {
    return e.s
}
```

其实就是实现了一下Error接口，包含一个错误信息的string，因为该string是不可导出的，我们只能对于标准库中的error进行Error信息的查看。\
标准库中的错误说实话没什么非常大的用处，顶多是通过errors.New()
来定义自定义的标准错误类型的错误。更多的时候我们需要像上面那样在Error接口的基础上定义自己的异常，来实现更加丰富的功能。

#### 异常  

真正异常情况下，go中依赖的是defer、panic、recover操作来处理异常。\
`defer` 之前提到过，类似于C++
中的析构函数，不过同析构函数不一样的是defer主要用于在函数结束时执行一段代码。\
`panic` 用来表示非常严重不可恢复的错误，像是Java中的Error，在go里面这是一个内置函数，在panic发生时程序通常会宕掉，并且打出调用栈来帮忙分析处理。\
`recover` 通常是用来处理panic这种重大异常的，来让程序不退出，仅影响这一次操作。\
比如说：

```  
func test() {
    defer func() {
        // do something
        if err:=recover();err!=nil{
            fmt.Println(err) // 这里的err其实就是panic传入的内容
        }
    }()
    // do something maybe panic
}
```

这里的处理方式很像是Java中try-chtch异常处理方式，但是go中对于异常的定义不像是Java中那样，我们应该改变Java中的一些思路，尽可能使用错误，不能误用异常，只有致命的panic异常时才这样处理。\
关于go相关的错误和异常体系暂时就先讲这么多。
:::

 
