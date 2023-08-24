---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-20 11:51:19 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 语言基础\--变量&数据类型
updated: "2019-06-17 15:50:58 +0000"
---

:::  
#### go 变量  

Golang 变量声明和初始化比起其他语言要简单一些，并且丰富不少。\
语法：
`var identifier type` \
var
代表声明一个变量，identifier为变量名（go中变量名由字母、数字、下划线构成，首个字符不能为数字），type为变量类型。其中go变量的命名规则是建议驼峰式的。\
常见的方式：

```  
/** 单变量 **/
var tmp int // 声明一个int 类型变量tmp，go的变量类型放在变量之后
var tmp int = 6 // 声明+初始化
var tmp = 6 // 声明+初始化，并省略类型
tmp := 6 // 直接初始化一个短变量，局部变量强烈使用这种方式

/**多变量**/
tmp1, tmp2, tmp3 := 1, 2, 3
var tmp1, tmp2, tmp3 = 1, 2, 3
```

#### go常量  

const：\
常量是一个简单的标识符，在程序运行时不能修改其变量。常量的数据类型可以是boolean型、数字型、字符串型。\
语法：`const identifier [type] = value` \
当存在type时为显式定义，省略type时为隐式调用。\
iota：\
iota是一种特殊常量，一种认为可以被编译器修改的常量。\
itoa在const关键字出现时被置为0，const中每新增一个常量声明，则iota计数+1.

```  
const (
    var1 = iota // 1
    var2 = iota // 2
    var2 = iota // 3
)
/** 可以简化为下面的写法 **/
const (
    var1 = iota // 1
    var2 // 2
    var2 // 3
)
```

#### go 数据类型  

Golang同Java/C/C++
一样也是一种强类型语言，Golang内置的类型比较丰富下面仔细来看看。\
golang大致分为boolean类型、数字类型、字符串类型、派生类型

##### 布尔类型  

boolean：只可以为`true or false` 

```  
var boolTmp bool = true
```

##### 数字类型  

go语言支持整型和浮点型，同样支持复数，其中位的运算采用补码。\
int：\
uint8 ：无符号8位整型 (0 到 255)\
uint16：无符号16位整型 (0 到 65535)\
uint32：无符号 32 位整型 (0 到 4294967295)\
uint64：无符号 64 位整型 (0 到 18446744073709551615)\
int8：有符号 8 位整型 (-128 到 127)\
int16：有符号 16 位整型 (-32768 到 32767)\
int32：有符号 32 位整型 (-2147483648 到 2147483647)\
int64：有符号 64 位整型 (-9223372036854775808 到 9223372036854775807)

还有几个比较特殊的：byte类似于uint8、rune类似于int32、uint32位或者64位、int
（与uint 一样大小）、uintptr（无符号整型，通常用于存放一个指针）

float：\
float32：IEEE-754 32位浮点型数\
float64：IEEE-754 64位浮点型数\
complex64：32 位实数和虚数\
complex128：64 位实数和虚数

##### 字符串类型  

go中字符串的字节采用UTF-8编码标识Unicode文本，关键字：string\
string底层其实就是一个\[\]byte，并且可以直接跟\[\]byte类型互相转换（string本质就是一个字符流）\
字符串中的字符时不能修改的，string是一个只读的类型。

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
字符串相关的工具包是strings、strconv\
![](Go%20%E8%AF%AD%E8%A8%80%E5%9F%BA%E7%A1%80--%E5%8F%98%E9%87%8F&%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B.resources/61BE34C4-0BBF-48A8-9376-2788ED0ECD2D.png) 
 \
![](Go%20%E8%AF%AD%E8%A8%80%E5%9F%BA%E7%A1%80--%E5%8F%98%E9%87%8F&%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B.resources/A7C1AAC8-F44C-4B41-A486-C9227E330317.png) 
 \
string相关API后续会单独介绍。

##### 派生类型  

派生类型主要包括：\
指针类型（Pointer）\
数组类型\
Map类型\
结构化类型（type）\
结构化类型（struct）\
Channel类型\
函数类型\
切片类型（slice）\
接口类型（interface）\
这一篇暂时就介绍这么多

##### 指针类型  

go指针是一种非常重要的变量，同C中的指针一样，指针保存的是指向变量的地址，语法也跟C的类似。

```  
var pointerOne *int
var tmpInt int
var pointerTwo = &tmpInt
```

这样就定义了一个int类型的指针pointerOne,
也定了一个指向tmpInt变量的指针pointerTwo。`&` 
是一个取地址符，指针通过`*` 标示。\
go中可能会出现空指针（指向nil），但是不会出现野指针（当一个变量被释放时，指针指向的应该是一个nil），相对安全。\
这一篇暂时就这么多。
:::

 
