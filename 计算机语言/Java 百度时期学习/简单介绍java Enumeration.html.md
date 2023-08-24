---
altitude: 153.5784606933594
author: zouzq7@163.com
created: "2016-10-31 12:32:58 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19542757749825
longitude: 117.111666813392
source: desktop.mac
title: 简单介绍java Enumeration
updated: "2016-10-31 12:33:23 +0000"
---

<div>

Enumeration接口 \
Enumeration接口本身不是一个[数据结构](http://lib.csdn.net/base/datastructure "算法与数据结构知识库"){target="_blank"
style="color: rgb(223, 52, 52); text-decoration: none; font-weight: bold;"}。但是，对其他数据结构非常重要。
Enumeration接口定义了从一个数据结构得到连续数据的手段。例如，Enumeration定义了一个名为nextElement的方法，可以用来从含有多个元素的数据结构中得到的下一个元素。 \
Enumeration接口提供了一套标准的方法，由于Enumeration是一个接口，它的角色局限于为数据结构提供方法协议。下面是一个使用的例子： \
//e is an object that implements the Enumeration interface\
while (e.hasMoreElements()) {\
    Object o= e.nextElement();\
    System.out.println(o);\
｝

实现该接口的对象由一系列的元素组成，可以连续地调用nextElement()方法来得到
Enumeration枚举对象中的元素。Enumertion接口中仅定义了下面两个方法。 \
·boolean hasMoreElemerts() \
测试Enumeration枚举对象中是否还含有元素，如果返回true，则表示还含有至少一个的元素。 \
·Object nextElement() \
如果Bnumeration枚举对象还含有元素，该方法得到对象中的下一个元素。

【例】\
/\*\
\*
@(#)DemoEnumeration.[Java](http://lib.csdn.net/base/javaee "Java EE知识库"){target="_blank"
style="color: rgb(223, 52, 52); text-decoration: none; font-weight: bold;"} \
\* 演示Enumeration接口的使用\
\* / \
import java.util.\*;\
class DemoEnumeration{ \
     public static void main(String\[\] args){\
          //实例化MyDataStruct类型的对象\
          MyDataStruct mySataStruct=new myDataStruct();\
          //得到描述myDataStruct类型对象的enumeration对象\
          Enumeration myEnumeration =myDataStruct.getEnum();\
         //使用对象循环显示myDataStruct类型的对象中的每一个元素\
         while (myEnumeration.hasMoreElements())\
               System.out.println(myEnumeration.nextElement());\
    } \
}

//MyEnumeration类实现Enumeration接口\
class MyEnumerator implements Enumeration\
{ \
      int count; // 计数器\
      int length; //存储的数组的长度\
      object\[\] dataArray; // 存储数据数组的引用\
      //构造器\
      MyEnumeration(int count,int length,object\[\] dataArray){ \
            this.count = count;\
            this.length= length;\
            this.dataArray＝dataArray;\
      } \
      public boolean hasMoreElements() { \
            return (count\< length);\
      }\
      public Object nextElement() {\
            return dataArray\[count++\]；\
      }\
} \
//MyDataStruct类用于实例化一个简单的、可以提供enumeration对象\
//给使用程序的数据结果对象\
class MyDataSttuct\
{ \
     String\[\] data;\
     // 构造器\
     MyDataStruct(){\
          data＝new String\[4\] \
          data\[0\] =\"zero\"；\
          data\[1\]=\"one\"；\
          data\[2\] =\"two\";\
          data\[3\]=\"three\";\
    ｝\
    // 返回一个enumeration对象给使用程序\
    Enumeration getEnum() {\
          return new MyEnumeration(0,data.length,data);\
    ｝

</div>

<div>

\

</div>

:::  
程序的运行结果为: \
zero\
one

<div>

two

</div>
:::
