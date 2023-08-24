---
author: zouzq7@163.com
created: "2019-05-17 06:19:23 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: shell \-- AWK&文本处理 浅析
updated: "2019-07-18 08:57:09 +0000"
---

<div>

awk 入门：

</div>

<div>

awk
是格式化文本处理最常用的工具，日常捞数据、切数据最常用的，当然了不用awk
也有其他的工具能解决问题，但是经过检验
awk可以说是最好用的。说实话对于新手来说
awk上手可能会慢一些，但是用习惯了即将溜的飞起。

</div>

<div>

下面从我工作时常用的一些方式来阐述这个命令（网上也有很多的资料可以对比参考）：

</div>

<div>

假设1.demo 文件内容如下：

</div>

<div>

1 2 3 4 abc

</div>

<div>

1 2 3 4 abcdce

</div>

<div>

\

</div>

<div>

awk \'{print \$0}\' 1.demo 这就是最简单的一个命令，\'{print \$0}\'
指的是动作，1.demo是文件名，这也是awk 的通用格式 awk 动作
文件。动作中常有\$0\\\$1\\\$2 等变量，\$0指的是当前行，\$1这些指的是第1
..n的字段（awk 会默认按照空格或者\\t对于行进行分割，我们也可以使用-F
指定分割符）

</div>

<div>

awk \'{print \$0}\' 1.demo  output：1 2 3 4 abc

</div>

<div>

awk \'{print \$5}\' 1.demo  output：abc

</div>

<div>

\

</div>

<div>

awk 基础：

</div>

<div>

1、原样输出 是通过动作中的双引号控制的，demo：awk \'{print
\$5\"2333\"}\' 1.demo  output：abc2333

</div>

<div>

2、常用参数：

</div>

<div>

-F 指定文件分割符

</div>

<div>

\

</div>

<div>

3、常用变量：

</div>

<div>

FILENAME：当前文件名 demo: awk \'{print toupper(\$NF)\$FILENAME}\'
1.demo output:ABC1

</div>

<div>

FS：字段分隔符，默认是空格和制表符。

</div>

<div>

RS：行分隔符，用于分割每一行，默认是换行符。

</div>

<div>

OFS：输出字段的分隔符，用于打印时分隔字段，默认为空格。

</div>

<div>

ORS：输出记录的分隔符，用于打印时分隔记录，默认为换行符。

</div>

<div>

OFMT：数字输出的格式，默认为％.6g。

</div>

<div>

NR：当前处理的第几行

</div>

<div>

NF：当前行都多少个字段

</div>

<div>

BEGIN: 设置初始参数，初始化变量

</div>

<div>

END: 读完文件后做最终的处理 demo：awk \'BEGIN {print \$0,
\$1;}\' 1.demo output：1 2 3 4 abc 1

</div>

<div>

4、常用函数：

</div>

<div>

toupper() ：将字符转化为大写 demo: awk \' \'
1.demo  output：ABCtest

</div>

<div>

tolower()：字符转为小写

</div>

<div>

length()：返回字符串长度 demo: awk \'{print \"str: \" \$(NF-1)  \"
length: \" length(\$(NF-1))}\' 1.demo  output：str: 4 length: 1

</div>

<div>

substr()：返回子字符串

</div>

<div>

sin()：正弦

</div>

<div>

cos()：余弦

</div>

<div>

sqrt()：平方根

</div>

<div>

rand()：随机数

</div>

<div>

5、条件判断：

</div>

<div>

\$ awk \'条件 动作\' 文件名

</div>

<div>

demo : awk \'NR % 2 == 1 {print \$0}\' 1.demo output: 1 2 3 4
abc(仅展示了第一行)

</div>

<div>

demo : awk \'/.\*dce/ {print \$0}\' 1.demo output: 1 2 3 4
abcdce（其中/.\*dce/ // 内部是一个正则表达式）

</div>

<div>

除了这类简单的条件判断，当判断条件逐渐复杂之后，我们可以使用if 语句

</div>

<div>

\~            匹配，与==相比不是精确比较

</div>

<div>

!\~           不匹配，不精确比较

</div>

<div>

==         等于，必须全部相等，精确比较

</div>

<div>

!=           不等于，精确比较

</div>

<div>

&&　     逻辑与

</div>

<div>

\|\|             逻辑或

</div>

<div>

+            匹配时表示1个或1个以上

</div>

<div>

常用的一些信息这里就不过多赘述了，可以man awk

</div>

<div>

\

</div>

<div>

除去awk 常用的文本处理相关的命令还有不少，这里也大体说一下：

</div>

<div>

常用的查看文本的命令
通常就是cat（行数较小）、less（可以滚动查看，类似的还有more，区别就是一次滚一行还是一页）、grep
（产看匹配到的文本，当然了grep 可不仅仅只是查看个文本）

</div>

<div>

sort：

</div>

<div>

[sort将文件的每一行作为一个单位，相互比较，比较原则是从首字符向后，依次按ASCII码值进行比较，最后将他们按升序输出。] 

</div>

:::  
[常用参数：] 
:::

:::  
[[-u : unique
唯一，排序并且排除重复项] 
:::

:::  
[-r : reverse
反向排序] 
:::

:::  
[[-o : output
将排序结果输出到源文件] 
:::

:::  
[[[-n ： number
安装数字进行排序，默认数字会被当作字符串进行比较] 
:::

:::  
[[[[-t ： tables
指定分隔符，默认tab为分隔符] 
:::

:::  
[-k：
指定安装哪一个分割区域进行排序] 
:::

<div>

comm：

</div>

<div>

comm file1 file2

</div>

<div>

comm
命令通常用于两个排好序的文件之间进行比较（必须是已排好序），然后提供了一些参数来调整输出，我们就可以很轻松的拿到交集、差集这些结果了。

</div>

<div>

comm 的输出通常有三列：1、file1 特有的。2、file2特有的 3、都有的

</div>

<div>

所以我们可以这么用：

</div>

<div>

[交集：打印出两个文件所共有的行。] 

</div>

:::  
[求差：打印出指定文件所包含的且不相同的行。] 
:::

:::  
[差集：打印出包含在一个文件中，但不包含在其他指定文件中的行。] 
:::

<div>

参数：

</div>

<div>

[-1：不显示在第一个文件出现的内容；] 

</div>

<div>

[-2：不显示在第二个文件中出现的内容；] 

</div>

<div>

[-3：不显示同时在两个文件中都出现的内容。] 

</div>

<div>

字符串截取：

</div>

<div>

\${var#\*/}

</div>

<div>

从左边数删除第一个'/'及'/'左边的字符

</div>

<div>

\${var##\*/}

</div>

<div>

从左边数删除最后一个'/'及'/'左边的字符

</div>

<div>

\${var%/\*}

</div>

<div>

从右边数删除第一个'/'及'/'右边的字符

</div>

<div>

\${var%%/\*}

</div>

<div>

从右边数删除最后一个'/'及右边的字符

</div>

<div>

\${var:1:9}

</div>

<div>

从左边数删除第一个至第九个字符

</div>

<div>

\${var:0-1:9}

</div>

<div>

从右边数删除第一个至第九个字符

</div>
