---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-20 13:16:21 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 语言基础\--语法基础
updated: "2019-06-17 15:54:37 +0000"
---

:::  
#### 运算符  

同其他语言一样go也有
算术运算符、关系运算符、逻辑运算符、位运算符、赋值运算符
这几类，作用也是一致的，这里就不过多赘述了。\
算数运算符：`+、-、*、/、%、++、--` \
关系运算符：`==、!=、>、<、>=、<=` \
逻辑运算符：`&&、||、!` \
位运算符：`&、|、^、>>、<<` \
赋值运算符：`=及复合赋值运算+=、-=、*=、/=、%=、<<=、>>=、&=、^=、|=` \
关于运算符的优先级，二元运算符运算方向均是从左至右，先计算括号内算式，其他的基本按照"单目乘除位关系，逻辑三目后赋值"。

#### 条件运算  

这一块儿比较简单，直接看代码吧\
if:

```  
var a = true
if a {
    fmt.Println("is true")
}
```

if-else:

```  
var a = true
if a {
    fmt.Println("is true")
} else {
    fmt.Println("is false")
}
```

switch:

```  
var target = "A"
switch target {
    case "A" : fmt.Println("A")
    case "B" : fmt.Println("B")
    default : fmt.Println("default")
}
/** 这里需要注意一点，go 中是自动break的 **/
/** case 也可以这么用 **/
switch {
    case target == "A" : fmt.Println("A")
    case target == "B" : fmt.Println("B")
    default : fmt.Println("default")
}
```

select:\
select 是一种类似于switch的结构。\
常用于监听IO操作，当IO操作发生时触发相应的动作.\
在看select前先看一下channel，go建议使用通信的方式代替共享内存，通信的就是指的channel。\
大体如下图所示\
![](Go%20%E8%AF%AD%E8%A8%80%E5%9F%BA%E7%A1%80--%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80.resources/29AC1B9D-CC28-4895-88BE-B39D093CA59C.jpg) 
 \
后续会对通道进行单独的介绍，这里近看一下该要即可。

```  
/** 语法：每一个case 都必须是一个通信（发送 or 接受），所有channel都会被求值，所有被发送的表达式都会被求值，select会随机选择一个可执行的case执行，其他的不会执行。如果所有case都在执行 则选择default执行，如果没有default，select被阻塞**/
select {
    case communication clause  :
       statement(s);      
    case communication clause  :
       statement(s); 
    /* 你可以定义任意数量的 case */
    default : /* 可选 */
       statement(s);
}
/** 看一下 demo**/
package main

import "fmt"

func main() {
    ch1 := make(chan int, 1)
    ch2 := make(chan int ,2)
    ch1 <- 1
    var e1, e2 int
    select {
        case e1 = <-ch1:
          fmt.Printf("e1[%v]", e1)
        case e2 = <-ch2:
          fmt.Printf("e2[%v]", e2)
      default :
      fmt.Printf("default:e1[%v] e2[%v]", e1, e2)
    }
}
```

output:\
![](Go%20%E8%AF%AD%E8%A8%80%E5%9F%BA%E7%A1%80--%E8%AF%AD%E6%B3%95%E5%9F%BA%E7%A1%80.resources/3FD8FDE0-5875-48CA-A307-0074483BA013.png) 
 \
大家可以看一下注释掉其中的default或者换个赋值情况看一下效果。

#### 循环结构  

Go
中循环结构就只有一个for循环，语法和其他语言一致，仅仅是for-each的语法结构不太一样而已。

```  
/** 语法 **/
for init; condition; post { }
/** demo **/
for i:=1; i<10; i++ {
    fmt.Println(i)
}
/** go 可以对于slice、map、数组、字符串等进行遍历，数组这些key值就是下标**/
for key, value := range oldMap {
    newMap[key] = value
}
/** go 的for可以类似于while用的 **/
for true {
    fmt.Println("for for for")
}
```

这一篇就先介绍到这里。
:::

 
