---
altitude: 55
author: zouzq7@163.com
created: "2017-07-28 09:53:05 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.69514715381279
longitude: 119.2477956404331
source: desktop.mac
title: JVM《 零  JVM 相关简介&内存模型》
updated: "2019-02-25 03:28:19 +0000"
---

<div>

提起Java，我们首先想到的是Java 语言。其实Java 是包括Java
语言、Java虚拟机规范两部分的。Java 不赘述，要说的是JVM， 即 Java
虚拟机。

</div>

<div>

JVM 是 Java
平台无关性的原因或者说出处。我们写的代码，编译之后的Class文件是放在JVM上运行的，每个平台支持Java之后就意味着把我们编译之后的Class
文件扔过去就好了，这一份结果放到支持Java
的每个平台上都是可以的（当然了，话不敢说这么绝对。一次书写，处处运行。这个广告词也经常会被打脸）这个特性是Java
火起来的一个蛮大的原因。

</div>

<div>

\

</div>

<div>

画个图理解一下：

</div>

<div>

![](JVM%E3%80%8A%20%E9%9B%B6%20%C2%A0JVM%20%E7%9B%B8%E5%85%B3%E7%AE%80%E4%BB%8B&%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E3%80%8B.resources/506B03C8-B8E5-4262-BBCF-F304A44078F3.png) 
 \

</div>

<div>

\

</div>

<div>

相当于JVM就是在我们机器上执行Java代码的一种媒介。大家试着了解这样一个过程，从我们写代码到我们代码执行。

</div>

<div>

![](JVM%E3%80%8A%20%E9%9B%B6%20%C2%A0JVM%20%E7%9B%B8%E5%85%B3%E7%AE%80%E4%BB%8B&%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E3%80%8B.resources/5DD78825-A31A-4949-ADDA-7BC81E7958FB.png) 
 \

</div>

<div>

那JVM是怎么执行我们的代码的，JVM其中的结构时怎么样的，怎么去管理运行时的工程文件。我们如何去使用和优化JVM，我们的代码、我们的对象在JVM上是怎样的。

</div>

<div>

本篇文章及后续的文章要说的就是这些问题，列个目录吧

</div>

<div>

\

</div>

<div>

JVM 《零 JVM相关简介&内存模型》

</div>

<div>

JVM 《一 JVM 中的垃圾回收》

</div>

<div>

JVM 《二 JVM 中的垃圾回收器》

</div>

<div>

JVM 《三 JVM 中的String》

</div>

<div>

JVM 《四 JVM 类加载机制&Tomcat 类加载方式解析》

</div>

<div>

JVM 《五 JVM Class 文件的解析》

</div>

<div>

JVM 《六 JVM 角度看对象》 

</div>

<div>

JVM 《七 JVM JDK反射&CGLib 反射解析》

</div>

<div>

JVM 《八 JVM 参数作用及简单调优》

</div>

<div>

JVM 《九 JVM 的角度去看待并发》

</div>

<div>

\

</div>

<div>

大约三个周之内推完，然后下个系列推Mysql、NoSQL相关、Redis、MongoDB等数据库相关的～

</div>

<div>

\

</div>

<div>

回到正题～

</div>

<div>

\

</div>

<div>

对于JVM我们需要首先了解JVM整体的结构

</div>

<div>

![](JVM%E3%80%8A%20%E9%9B%B6%20%C2%A0JVM%20%E7%9B%B8%E5%85%B3%E7%AE%80%E4%BB%8B&%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E3%80%8B.resources/45EB05F0-61E0-4F3B-8F08-968588AD3BC0.png) 
 \

</div>

<div>

\

</div>

<div>

其中：

</div>

<div>

类加载系统： 负责从文件或者网络中加载Class
信息，加载的信息放在一个叫做方法区的地方。还有String
字面量和数字常量，这些是放在静态常量池中的。

</div>

<div>

\

</div>

<div>

执行引擎：是JVM最核心的组件之一，他负责执行虚拟机中的字节码，通过即时编译到机器码运行。

</div>

<div>

\

</div>

<div>

PC寄存器：会为每一个java
线程创建PC寄存器。在任意时刻，一个java线程总是在执行一个方法，这个正在被执行的方法称为当前方法。如果当前方法不是本地方法，PC寄存器就会指向当前正在被执行的指令。如果当前方法是本地方法，那么PC寄存器的值就是undefined。就是一个简单的线程私有的空间。

</div>

<div>

\

</div>

<div>

垃圾回收系统：负责回收 已经废弃的对象、类信息、常量等。简单的来说就是回收垃圾。

</div>

<div>

\

</div>

<div>

直接内存：Java
中NIO库中的程序是允许使用直接内存的，也就是在JVM之外申请到的内存。

</div>

<div>

\

</div>

<div>

以上是部分JVM的结构介绍，还有几部分没说，因为要详细介绍～

</div>

<div>

\

</div>

<div>

大家肯定听过一个词儿叫做内存结构，JVM内存模型实际上是JVM对自身使用内存（或数据使用区）的一种抽象，更方便管理和理解及使用。

</div>

<div>

惯例，先上图

</div>

<div>

\

</div>

<div>

![](JVM%E3%80%8A%20%E9%9B%B6%20%C2%A0JVM%20%E7%9B%B8%E5%85%B3%E7%AE%80%E4%BB%8B&%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E3%80%8B.resources/BB8A01F7-F361-48F8-AE8E-FFCB9D0CD8D1.png) 
 \

</div>

<div>

\

</div>

<div>

\

</div>

<div>

方法区：是用来存放被虚拟机加载的类信息、常量、静态变量，还有即使编译的代码。是用来放我们的代码的一块内存区域。

</div>

<div>

\

</div>

<div>

Java
堆：用来存放我们使用的对象。这是GC也就是垃圾回收的重点区域。至于对象是什么样子怎么存放的，后续会单独介绍。

</div>

<div>

\

</div>

<div>

虚拟机栈（Java
栈）：里面存放的东西叫做栈帧，是用来保存我们一个又一个Java
方法执行信息的。一个栈帧包括局部变量表（我们方法内部使用的变量及方法参数）操作数栈、动态链接、返回地址。其中提一点多少相关的，存放在Java堆里面的对象是线程共有的，当多个线程同一时间段内操作一个对象时可能会出现并发安全问题。对象是非安全的，但是如果一个对象仅能有一个线程访问，那么它肯定是安全的了，这其实就是一种线程封闭的使用方式。（方法栈线程私有、而其中使用的对象共有）而在方法内产生一个对象，并且不逸出（不安全的发布才叫逸出），仅在内部使用，那么这个对象其实就是安全的使用方式。

</div>

<div>

我还是放个图吧\...

</div>

<div>

![](JVM%E3%80%8A%20%E9%9B%B6%20%C2%A0JVM%20%E7%9B%B8%E5%85%B3%E7%AE%80%E4%BB%8B&%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E3%80%8B.resources/08189794-854F-4B38-BB60-8629A4C0A610.png) 
 \

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>

<div>

本地方法栈：跟虚拟机栈其实类似，也是线程私有的。其中跑的是JVM持有的本地方法（Native
Method）。所谓本地方法，就是Java调用非Java代码的接口。官方定义是这么给的：[ \"A
native method is a
Java ] [method
whose implementation is provided by
non-Java ] 

</div>

<div>

\

</div>

<div>

[程序计数器：程序计数器只占有很小的一部分内存区域，有时候是忽略不计的。它的作用，正如它的名字，我们的Class文件加载之后，方法内部在内存中相当于一条又一条的字节码。我们使用的分支、循环、跳转、异常处理、线程恢复等是通过一个类似于行号的东西来控制下一条我们要驱动执行的指令。而程序计数器里存放和使用就是这个东西。具体的怎么来控制，可以去看一下JVM源码。] 

</div>

<div>

\

</div>

<div>

[以上就是JVM的内存模型。] 

</div>

<div>

\

</div>

<div>

[这里有一个不得不提的问题OOM（OutOfMemoryError）也就是内存溢出错误，还有一个类似的叫做StackOverflowError（栈请求溢出）] 

</div>

<div>

[1\>当请求栈的深度大于所允许的深度的时候会抛出StackOverflowError] 

</div>

<div>

[2\>当扩展栈或者为对象申请空间、为一些类文件申请空间 申请不到时会抛出
OutOfMemoryError。] 

</div>

<div>

[当我们栈容量越大，所能建立的线程数就越少。我么可以通过JVM的一些参数来调节heap空间、永生代最大空间。这一块放在这里先提一下，后续会在介绍参数的时候具体说。] 

</div>

<div>

\

</div>

<div>

[关于JVM简介、组成结构、内存模型就说到这里～] 

</div>

<div>

[注：] 

</div>

<div>

[1\> Java 8中永生代已经被完全移除了。替代者叫做Metaspace，控制Java
永生代的参数（PermSize、MaxPermSize）会被忽然，并且产生警告信息。] 

</div>

<div>

[2\> String 常量池的存放 Java 6及之前是在永生代存放的。Java 7是在Java
Heap上存放的。] 

</div>

<div>

[3\>类似于这种常量池的存放，每个版本的JVM特性都是不一致的。或多或少都会有差异。这里是依照HotSpot说的JVM基础的一些概念，具体的细节还要依据具体的VM来判断。] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>

<div>

\

</div>
