---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-08-30 07:33:11 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go defer
updated: "2019-08-30 09:14:28 +0000"
---

:::  
### 前言  

之前的异常和panic的文章和demo中都出现了defer关键字，只是简单的说了下defer
中的代码块儿会在函数结束时及panic发生时被调用，但是内部原理是怎样的并没有深入，本篇就来深入看一下defer运作原理。跟Java
中的finally比较像。\
defer这种操作，在很多语言中都有，这类操作通常是用于一些收尾工作，比如说事务提交、日志啊

### 使用  

先看一个demo：

```  
func test() {
    defer func() {
        // do something
        fmt.println("c")
    }()
    fmt.println("a")
    fmt.println("b")
}
```

输出：a\\b\\c
:::

 
