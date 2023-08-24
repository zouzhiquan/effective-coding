---
altitude: 52.09636306762695
author: zouzq7@163.com
created: "2017-08-29 10:11:40 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03841081145081
longitude: 116.4110143937149
source: desktop.mac
title: String 相关
updated: "2017-08-29 10:13:08 +0000"
---

1.  [当两个字符串字面值连接时（相加），得到的新字符串依然是字符串字面值，保存在常量池中。编译时其实就是一个串] 

2.  [当字符串字面值与String类型变量连接时，得到的新字符串不再保存在常量池中，而是在堆中新建一个String对象来存放。很明显常量池中要求的存放的是常量，有String类型变量当然不能存在常量池中了。] 

3.  [字符串字面值与String类型常量连接，得到的新字符串依然保存在常量池中。] 

4.  [final String bb = getBB();其实与final String bb = new
    String("b");是一样的。也就是说return
    "b"会在堆中创建一个String对象保存"b"，虽然bb被定义成了final。可见并非定义为final的就保存在常量池中，很明显此处bb常量引用的String对象保存在堆中，因为getBB()得到的String已经保存在堆中了，final的String引用并不会改变String已经保存在堆中这个事实。] 

5.  可能很多人对intern()这个函数不了解。JDK
    API文档中对intern()方法的描述是：

    *    返回字符串对象的规范化表示形式。\
        一个初始为空的字符串池，它由类 String 私有地维护。*

    *    当调用 intern 方法时，如果池已经包含一个等于此 String
    对象的字符串（用 equals(Object)
    方法确定），则返回池中的字符串。否则，将此 String
    对象添加到池中，并返回此 String 对象的引用。*

    *    它遵循以下规则：对于任意两个字符串 s 和 t，当且仅当 s.equals(t)
    为 true 时，s.intern() == t.intern() 才为 true。*

    :::  
    *    所有字面值字符串和字符串赋值常量表达式都使用 intern
    方法进行操作。*\
    \
    :::

<div>

[[\
] 

</div>
