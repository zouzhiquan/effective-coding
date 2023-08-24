---
author: zouzq7@163.com
created: "2018-08-29 11:48:54 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java String
updated: "2018-08-30 03:41:08 +0000"
---

<div>

以下代码的测试环境 为Java 10：

</div>

<div>

String

</div>

<div>

<div>

<div>

[public final class ] String

</div>

<div>

    [implements ] java.io.Serializable[,
] Comparable\<String\>[,
] CharSequence {

</div>

<div>

[   
] 

</div>

<div>

[    ] [private final
byte] \[\]
[value] 

</div>

</div>

<div>

</div>

</div>

<div>

1、首先String
是一个final类（不能被继承，可以理解为最终的，防止继承使用），里面维护了一个字节数组。

</div>

<div>

我们经常使用String 一般都是 String str1 = \"2333";
这种的叫做字面量，还有一种是String str2 = new String("2333");
然后还有配合Builder 及 Buffer使用的。

</div>

<div>

这些方式存在什么差异呢：可以试着去运行这个例子

</div>

<div>

<div>

[class ] Test1 {

</div>

<div>

    [public static void
] (String\[\]
args) {

</div>

<div>

        String str1 =
[\"2333\"] 

</div>

<div>

[        ] String str2 =
[\"2333\"] 

</div>

<div>

[        ] String str3 = [new
] 

</div>

<div>

[        ] String str4 = [new
] 

</div>

<div>

[        ] String str5 = str1 +
str2[;] 

</div>

<div>

[        ] String str6 =
[\"23332333\"] 

</div>

<div>

[        ] String str7 =
[\"23332333\"] 

</div>

<div>

[        ] String str8 = str2 +
str1[;] 

</div>

<div>

[       
] .println(str1
== str2)[; ] 

</div>

<div>

[       
] .println(str1
== str3)[; ] 

</div>

<div>

[       
] .println(str3
== str4)[; ] 

</div>

<div>

[       
] .println(str5
== str6)[; ] 

</div>

<div>

[       
] .println(str6
== str7)[; ] 

</div>

<div>

[       
] .println(str7
== str5)[; ] 

</div>

<div>

[       
] .println(str8
== str5)[; ] 

</div>

<div>

[    ] }

</div>

<div>

}

</div>

</div>

<div>

这里可以显然看出，使用new
的时候是产生了新的String对象，而直接给一个字面量实际上是一个对象。Java
中是这样实现的，如果常量池中存在该字面量，则直接使用。如果没有的话创建一个新的字面量，然后投入常量池。当对于字面量进行操作时，实际上是创建一个新的对象，而不是从常量池里面复用。其实这样设计也是更加符合String字面量的概念，更像是一种基础类型。

</div>

<div>

<div>

[public native ] String
[intern] ()[; //
这是加入常量池的方法] 

</div>

</div>

<div>

然后String 还提供了一些相关的便捷方法，但是为了避免NPE
还是直接使用StringUtils吧

</div>

<div>

\

</div>

<div>

StringBuffer & StringBuilder

</div>

<div>

这两个放到一块说，功能是类似的，buffer提供同步策略，builder不保证。\

<div>

[public final class ] StringBuffer

</div>

<div>

    [extends ] AbstractStringBuilder

</div>

<div>

    [implements ] java.io.Serializable[,
] CharSequence {

</div>

<div>

<div>

[public final class ] StringBuilder

</div>

<div>

    [extends ] AbstractStringBuilder

</div>

<div>

    [implements ] java.io.Serializable[,
] CharSequence {

</div>

</div>

<div>

两个类都继承自AbstractStringBuilder，然后StringBuffer、StringBuilder都默认实现一个长度为16的byte\[\]。

</div>

<div>

StringBuffer中都是这样的清一色的synchronized方法，相对于Builder而言效率要低一些。但在并发环境中除非自己额外同步要不还是使用StringBuffer吧，

</div>

<div>

<div>

[public synchronized ] StringBuffer
[append] (String str) {

</div>

<div>

    [toStringCache ] =
[null;] 

</div>

<div>

[   
super] 

</div>

<div>

[    return this;] 

</div>

<div>

}

</div>

<div>

就性能而言，buffer、builder在少量的操作时是差异不大的，但操作次数上升之后Builder就显得有优势了。除此之外，两者比String字面量操作是快非常多的（原因正是上文说的String
字面量的特性）

</div>

</div>

<div>

<div>

[class ] Test1 {

</div>

<div>

    [public static void
] (String\[\]
args) {

</div>

<div>

        String
str1=[\"2333\"] 

</div>

<div>

[        ] StringBuilder str2 = [new
] 

</div>

<div>

[        ] StringBuffer str3 = [new
] 

</div>

<div>

[        long ] startTime =
System.[currentTimeMillis] 

</div>

<div>

[        for] i
= [0] i \<
[10000] i++){

</div>

<div>

            str1 +=
[\"2333\"] 

</div>

<div>

[        ] }

</div>

<div>

        [long ] endTime =
System.[currentTimeMillis] 

</div>

<div>

[       
] .println([\"字面量操作：\"
] +
(endTime-startTime))[;] 

</div>

<div>

\

</div>

<div>

[        ] startTime =
System.[currentTimeMillis] 

</div>

<div>

[        for] i
= [0] i \<
[10000] i++){

</div>

<div>

           
str2.append([\"2333\"] 

</div>

<div>

[        ] }

</div>

<div>

        endTime =
System.[currentTimeMillis] 

</div>

<div>

[       
] .println([\"builder操作：\"
] +
(endTime-startTime))[;] 

</div>

<div>

\

</div>

<div>

[        ] startTime =
System.[currentTimeMillis] 

</div>

<div>

[        for] i
= [0] i \<
[10000] i++){

</div>

<div>

           
str3.append([\"2333\"] 

</div>

<div>

[        ] }

</div>

<div>

        endTime =
System.[currentTimeMillis] 

</div>

<div>

[       
] .println([\"buffer操作：\"
] +
(endTime-startTime))[;] 

</div>

<div>

\

</div>

<div>

[    ] }

</div>

<div>

}

</div>

</div>

<div>

执行结果：

</div>

<div>

字面量操作：114

</div>

<div>

builder操作：1

</div>

<div>

buffer操作：2

</div>

<div>

Process finished with exit code 0

</div>

</div>

<div>

\

</div>

<div>

StringUtils

</div>

<div>

StringUtils是针对与String对象的提供了一系列建议的判别方法，下面是它的API（总结来自网络）

</div>

<div>

isEmpty(String str) 是否为空，空格字符为false

</div>

<div>

isNotEmpty(String str) 是否为非空，空格字符为true

</div>

<div>

isBlank(String str) 是否为空，空格字符为true

</div>

<div>

isNotBlank(String str) 是否为非空，空格字符为false

</div>

<div>

trim(String str)去除字符串两端的控制符，空字符串、null 返回 null

</div>

<div>

trimToEmpty(String str) 去除字符串两端的控制符，空字符串、null 返回\"\"

</div>

<div>

stripToNull(String str) 去除字符串两端的空白符，空字符串、null 返回null

</div>

<div>

stripToEmpty(String str) 去除字符串两端的空白符，空字符串、null 返回\"\"

</div>

<div>

strip(String str, String stripChars) 去掉str两端的在stripChars中的字符

</div>

<div>

strip(\"000000134_76539000\",\"0\")=\"134_76539\"

</div>

<div>

stripStart (String str,String stripChars) 去除str
前端在stripChars中的字符

</div>

<div>

stripEnd (String str,String stripChars) 去除str 后端在stripChars中的字符

</div>

<div>

equals(String str1,String str2)
比较两个字符串是否相等，如果两个均为空则认为相等

</div>

<div>

indexOf(String str,char searchChar) 返回searchChar
在字符串中第一次出现的位置，如果没找到则返回 -1，如果str 为null 或者
\"\",也返回-1

</div>

<div>

indexOf(String str,char searchChar,int startPos)
返回字符searchChar从startPos开始在字符串str中第一次出现的位置。

</div>

<div>

contains(String str,char searchChar)
str中是否包含字符searchChar，str为null 或者 searchChar为null,返回false
。

</div>

<div>

contains(\"\", \"\")  = true

</div>

<div>

contains(\"dfg\", \"\")  = true

</div>

<div>

containsIgnoreCase(String str,String searchStr)
str中是否包含字符searchChar，不区分大小写

</div>

<div>

int indexOfAny(String str, char\[\] searchChars)
找出字符数组searchChars中的字符第一次出现在字符串str中的位置。
如果字符数组中的字符都不在字符串中，则返回-1
，如果字符串为null或\"\"，则返回-1

</div>

<div>

subString(String str,int start) 从start 开始，包含start
那个字符，得到字符串str
的子串，如果start为负数，则从后面开始数起。如果str 为null 或者 \"\"
则返回其本身

</div>

<div>

subStringBefore(String str,String separator)
得到字符串separator第一次出现前的子串。不包含那个字符，如果str 为null
或者 \"\" 则返回其本身。

</div>

<div>

subStringAfter(String str,String separator)
得到字符串separator第一次出现后的子串，不包含那个字符，如果 str
为null，或者\"\",则返回其本身

</div>

<div>

subString(String str,int start,int end) 同上

</div>

<div>

left(String str,int len) 得到字符串str从左边数len长度的子串，如果str
为null 或者 \"\",则返回其本身，如果len小于0，则返回\"\"

</div>

<div>

right(String str,int len)得到字符串str从右边数len长度的子串

</div>

<div>

mid(String str,int pos,int len)
得到字符串str从pos开始len长度的子串，pos小于0，则设为0。

</div>

<div>

split(String str) 把字符串拆分成一个字符串数组，用空白符
作为分隔符，字符串为null 返回null，字符串为\"\",返回空数组{}

</div>

<div>

split(String str,char c) 按照 char c 拆分字符串

</div>

<div>

join(Object\[\] arrey)把数组中的元素连接成一个字符串返回

</div>

<div>

join(Object\[\] arrey,char c)
把数组中的元素拼接成一个字符串返回，把分隔符 c 也带上

</div>

<div>

deleteWhitespace(String str) 删除字符串中的所有空白符，包括转义字符

</div>

<div>

removeStart(String str,String remove)
如果字符串str是以remove开始，则去掉这个开始，然后返回，否则返回原来的串

</div>

<div>

removeEnd(String str,String remove)
如果字符串str是以字符串remove结尾，则去掉这个结尾，然后返回，否则返回原来的串。

</div>

<div>

remove(String str,char remove)
去掉字符串str中所有包含remove的部分，然后返回

</div>

<div>

replace(String str,String reql,String with)
在字符串text中用with代替repl，替换所有

</div>

<div>

replaceChars(String str,char old,char new) 在字符串中 new 字符代替 old
字符

</div>

<div>

public static String replaceChars(String str, String searchChars, String
replaceChars)

</div>

<div>

StringUtils.replaceChars(\"asssdf\",\"s\",\"yyy\"))    =    \"ayyydf\"

</div>

<div>

StringUtils.replaceChars(\"asdf\",\"sd\",\"y\"))        = \"ayf\"

</div>

<div>

StringUtils.replaceChars(\"assssddddf\",\"sd\",\"y\"))= \"ayyyyf\"

</div>

<div>

overlay(String str,String new,int start,int end) 用字符串new
覆盖字符串str从start 到 end 之间的串

</div>

<div>

chop(String str) 去掉字符串的最后一个字符,比如/r/n

</div>

<div>

repeat(String str,int repart) 重复字符串repeat次

</div>

<div>

rightPad(String str,int size,String padStr)
size长度的字符串，如果不够用padStr补齐

</div>

<div>

leftPad(String str,int size,String padStr)同上

</div>

<div>

center(String str,int
size)产生一个字符串，长度等于size，str位于新串的中心

</div>

<div>

swapCase(String str) 字符串中的大写转小写，小写转换为大写

</div>
