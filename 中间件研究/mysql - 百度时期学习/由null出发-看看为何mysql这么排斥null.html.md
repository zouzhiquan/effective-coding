---
altitude: 7.300780296325684
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2020-05-12 16:01:03 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 30.23690742244003
longitude: 120.0012658893272
source: desktop.mac
title: 由null出发-看看为何mysql这么排斥null
updated: "2020-05-12 17:27:03 +0000"
---

:::  
### 前言  

在表结构设计时我们经常会面对是否要加空值约束、默认值处理等问题，当向前人经验中检索时，经常会看到不建议为null，强刷面经时not
null也经常被归结到优化建议当中去，今天就来看一下为何？\
通常来说，null表示虚无&不确定，在不同的实现中对null的定义相似。最早在Codd提出关系模型的最早的paper里，就引入了NULL。\
先看不同语言对于null的处理：

##### c++的NULL  

c++中NULL是一个宏，是一个空指针常量，如果将NULL扩展为常数，那么这个数是0，类型为int，也就是说常数0既是整型常量，也是空指针常量（cpp
11中引入了nullptr作为空指针常量，解决二义性）。

##### Java的null  

Java中的null是一个关键字，通常指的是非基础类型变量的引用指向一个"空"，可以理解为引用对象为不确定，Java是一种基于copy传递的基础类型+引用类型数据构成的对象体系，Java内存管理中使用句柄来操控对象，而null表示句柄所在的内存地址上没有存储任何真实对象的地址。

##### go的nil  

go中没有null这个关键字（任何类型都会零值处理），但是有一个类似的nil（注意不是关键字），跟null的定位类似但不是完全相同，是一个预先定义的标识符，通常用于指针类型、map、slice、function、channel、interface等类型的零值定义，是一个没有默认类型的空值，标示的并非不确定性，而是可以明确代表各种不同内存布局的类型的零值。\
比如说：

```  
fmt.Println( (interface{})(nil) == (*int)(nil) ) 
// print:false

//下面这样声明零值后是 零值间是无法比较的
var _ = ([]int)(nil) == ([]int)(nil)
var _ = (map[string]int)(nil) == (map[string]int)(nil)
var _ = (func())(nil) == (func())(nil)
```

然后来看存储中对null的定义：

##### redis中的空值  

redis中操作时都是针对明确的值的，不会出现像是不确定这样的定义，通常来说，所以在操作redis时是没有对null的数据定义的，但是会有nil这样的零值存在，跟go处理方式比较像，代表n个结构的零值，一些语言对于nil
return这种情况有时候会变成一种null来处理，因为对于client来说，这就是个不确定的返回或者空值。

```  
127.0.0.1:6379> get B
(nil)
```

说到redis了，也顺道看下lua对于空返回的处理，容易写出bug来，简单记录了下，与本章主题无关。官方文档中的：\
**A non-nil Redis \"bulk reply\" results in a Lua string as the return
value.\
A nil bulk reply results in a ngx.null return value**

### mysql的空值定义  

看到各种语言、工具中对null、nil等关键词的使用，不难发现
null代表的是一种内存存储的不确定性，nil代表的通常是一种约定的零值。\
mysql中也是如此，null代表的是一种不确定性，所以通常用is null 或者not
null来判定一个实例数据是否为不确定的，而不是直接==来进行值比较。\
官方手册中中的定义：\
***The NULL value means "no data." NULL can be written in any
lettercase***\
也就产生了下面的关系操作符：\
IS NULL\\IS NOT NULL:能正常比较\
\<=\>:结果都是false\
mysql 这样对null的定义导致了一些问题：\
1、误导性&sql操作时的疏忽

```  
select A from t_xx where B not null or B = 2\G #正常操作
select A from t_xx where B = null or B = 2\G # B = null条件异常
select * from tableA where xxx not in(1, 2, null)\G #返回永远为空
select count(*) from tableA\G #null不参与统计
```

2、另外，null值在timestamp类型下容易出问题，特别是没有启用参数explicit_defaults_for_timestamp

### mysql null的空间表现  

null在mysql中是一种特殊的"占位符"，用来表示不确定性，但是实际上它也是需要占用一部分内存空间的，比不是所想的会省内存。\
null的长度是null,Compact Row Format前提下，每个行记录都会有一个Bit
vector来记录行中出现NULL的字段，长度为 N / 8 向上取整，其中
N为值NULL的字段数。

```  
select length(''), length(null), length(0), length('0');
 +------------+--------------+-----------+-------------+
 | length('') | length(null) | length(0) | length('0') |
 +------------+--------------+-----------+-------------+
 |          0 |         NULL |         1 |           1 |
 +------------+--------------+-----------+-------------+
```

对于变长字段是可以起到一定的省空间的作用，对于int／char这些是毫无作用的，并且需要一个额外字节作为判断是否为null的标志位（这个是问题，但忽略不计）\
某些情况可以省，但是相对于它带来的其他影响，这点优势啥都不是，继续往下看

### mysql null对索引的影响  

***Mysql难以优化引用可空列查询，它会使索引、索引统计和值更加复杂。可空列需要更多的存储空间，还需要mysql内部进行特殊处理。可空列被索引后，每条记录都需要一个额外的字节，还能导致MYisam
中固定大小的索引变成可变大小的索引。 \--《高性能mysql》***\
1、虽然mysql会对null字段也进行索引，但是只有is
null的方式会使用上索引,所以一旦使用不好，索引就无效了。\
2、如果null列做了唯一索引，那就尴尬了：唯一索引字段允许插入多条null的记录\
3、null一定程度上会是key_len变长（key_len
的计算规则和三个因素有关：数据类型、字符编码、是否为null,上面也提到啦）\
4、mysql内部会对null 做很多特殊逻辑的处理影响性能。

### 总结  

1、使用上容易出错\
2、很容易导致索引不可用或者效率下降\
3、很多情况下会增加存储\
4、提升表的维护成本\
所有使用NULL值的情况，都可以通过一个有意义的值的表示，这样有利于代码的可读性和可维护性。
:::

 
