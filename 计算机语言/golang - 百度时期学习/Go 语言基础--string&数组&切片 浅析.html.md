---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-21 13:03:47 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 语言基础\--string&数组&切片 浅析
updated: "2019-06-17 15:54:44 +0000"
---

:::  
本篇来看一下go语言基本的一些复合结构，最常使用的复合结构有map、数组、切片这几个，string因为底层实现是一个\[\]byte所以大致可以理解为是一种数组结构，下面会从基础使用及底层实现来看一下这两个结构。

#### string  

使用：\
go中字符串的字节采用UTF-8编码标识Unicode文本，关键字：string

```  
var str string = "23333" // "" 用于定义一个单行字符串
var str string = `2333
                2333` // ``用于定义一个多行的字符串，内部进行原样解析
```

同其他语言一样，go中的string
也支持`\r \n \t \' \" \\ \u***` 
等转译字符。\
并且我们可以使用数组的方式进行访问单个字符`str[0]` \
跟Java类似，我们可以使用`+` 
来比较字符串。\
原理：\
之前提到过string 实际上就是一个\[\]byte数组，但仅仅是粗略的说。实际上
string是一个引用类型，其中包含了两部分：指针（指向底层数组） +
数组长度。当发生复制时复制的也是指针和数组长度，底层的那个数组内容实际上是不发生变化的，这一点跟Java
还是蛮像的。\
这里引用一篇blog，里面阐述了一下go string 的实现：

> https://yq.aliyun.com/articles/656332?utm_content=m_1000019517

```  
func boo(a int, b int)(int, string){
    return a + b, "abcd"
}
// 先找到main.boo
81079 000000000044dfa0 <main.boo>:
// 注意一下这一段
81087   44dfc0:>------48 8d 05 d4 eb 01 00 >--lea    0x1ebd4(%rip),%rax        # 46cb9b <go.string.*+0xbb>
81088   44dfc7:>------48 89 44 24 20       >--mov    %rax,0x20(%rsp)
81089   44dfcc:>------48 c7 44 24 28 04 00 >--movq   $0x4,0x28(%rsp)
81090   44dfd3:>------00 00- 
81091   44dfd5:>------c3                   >--retq---
```

`lea 0x1ebd4(%rip),%rax` 复制给返回值,
`movq $0x4,0x28(%rsp)` 把长度也填进去,其实很轻松看到string就是c里面的char\*
和len的组合。

#### 数组  

go语言中的数组也是一个具有唯一相同类型、已编号、且长度固定的数据序列（一段固定长度的连续内存区域）。\
声明：`var name []type` 

```  
var array [5]int // 声明了一个长度为5的数组array
var array [...]string // 声明了一个暂不确定长度大小的string数组array

var array = [3]int{1, 2, 3} // 定义时指定长度，{}中的元素个数不能大于数组的长度
var array = [...]int{1, 2, 3}// 定义时不指定长度，有{}中的元素个数来确定数组的长度
```

注意点:\
`[n]int` 不是一种类型，这个跟Java
c/c++略有不同，大家可以粗暴的把数组长度也作为数组类型的一部分吧。

#### 切片  

go
中的切片和数组非常相像，就连声明和定义上也是类似的，可以简单粗暴的理解为不写长度就是一个切片，先来看一下差异：

```  
var array [3]int // 这是一个数组
var slice []int // 这是一个切片
var array = [2]int{1, 2} // 这是一个数组
var slice = []int{1,2} // 这是一个切片
```

总体来说，可以把slice理解为一通动态数组，与Java 中的ArrayList
稍微有点像，底层就是一个数组，但是在数组的基础上加了一层抽象，变成非固定长度了。相对于数组，切片还有这种定义方式：

```  
var slice []type = make([]type, len)
var slice []type = make([]type, length, capacity)
```

len指的是slice的长度（初始化长度），cap 为slice容量。\
常用函数：\
`len()` :
求切片长度\
`cap()` :
求切片容量（最长可以到达多少）\
`append()` :
向数组中追加元素，当出现数组长度不够用时，会进行数组的扩容（新开辟一段长度为\*2
的连续内存空间以供使用）\
`copy()` :
切片copy\
demo：

```  
func main() {
    srcSlice := []int{1, 2, 3}
    destSlice := []int{5, 5, 5, 5, 5, 5, 5}
    copy(destSlice, srcSlice)
    // destSlice =  [1 2 3 5 5 5 5]
    fmt.Println("destSlice = ", destSlice) 
}
```

注意点:\
当你定义一个固定长度的slice时，go
会帮你初始化这个固定长度的元素，当执行append时，是追加 len +
1位的，而不是放到第一个，因为已经自动初始化的这几个固定长度的元素了。\
这一篇暂时就这么多。
:::

 
