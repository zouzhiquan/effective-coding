---
altitude: 45.76701736450195
author: zouzq7@163.com
created: "2017-11-12 13:28:57 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.05661531739935
longitude: 116.4062878555837
source: desktop.mac
title: JVM JIT
updated: "2019-03-10 08:46:47 +0000"
---

<div>

JIT Just In
Time，JVM中的一种即时编译技术，目的是为了提升程序的运行效率。

</div>

<div>

\

</div>

<div>

先说一下前提，Java 是一种解释型语言，我们写完Java 代码后，编译器会把Java
编译为对应的字节码，然后由JVM解释执行。但是解释执行，每次执行时都需要对应的去逐条翻译、逐条执行，执行效率比较低下。

</div>

<div>

后来为了处理 慢 这个问题，JVM 引进了JIT。

</div>

<div>

\

</div>

<div>

JVM的对于JIT使用的运作流程是这样的：

</div>

<div>

首先我们的Java代码由javac
等编译器 编译为JVM可执行的字节码（ByteCode），然后JVM
会判断这段代码是否为热点代码，如果是那么使用JIT技术，如果不是那么解释执行，最后变成机器码，由操作系统分配然后CPU具体执行。

</div>

<div>

这里有具体的JVM参数能够调节下述流程：

</div>

<div>

-Xint全部使用字节码解释执行，这个是最慢的，慢的惊人，通常要比其他方式慢一个数量级左右。

</div>

<div>

-Xcomp相反，全部被编译成机器码执行，速度是很快的，但是存在一个缺陷，-Xcomp的策略过于简单。

</div>

<div>

-Xmixed，是一种自适应的方式，有的地方解释执行，有的地方编译执行，具体的策略要依据profile的统计分析来判断。

</div>

<div>

\

</div>

<div>

![](JVM%20JIT.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-13%20%E4%B8%8A%E5%8D%8811.07.15.png) 
 \

</div>

<div>

那具体的JIT操作，就是并非由我们的JVM去解释执行字节码，而是将得到的字节码直接编译成可执行的机器码，之后再调用、执行这一块的代码的时候直接使用机器码就ok了，省去了额外的编译或者解释。

</div>

<div>

具体的哪一类去解释，这是有具体的热点代码的判断策略来决定的。最常见的类型
比如循环体，高频执行，还有比如一些热点函数。

</div>

<div>

那既然这样快，为什么不全都去编译执行呢，因为我们程序中有挺大一部分调用频次比较低，并且一次编译的花销要比解释执行大不少，还会有额外的操作。这时去使用JIT就得不偿失了。

</div>

<div>

\

</div>

<div>

除了上述主要的特点外，JVM还有一个很棒的特性叫做自优化，当JIT对一块代码结构非常了解后，会做出具体的优化策略（比如何时从寄存器取值、何时从主存取值）

</div>

<div>

\

</div>

<div>

具体对于我们应该怎么使用或者说利用呢，下面马上介绍

</div>

<div>

\

</div>

<div>

如何选择：

</div>

<div>

JIT在运行时分为两种模式，客户端&服务端模式（-client、-server），这两种区别还是很大的。-client时会采用一个叫做C1的轻量级编译器，-server时会采用一个叫做C2的重量级编译器。-server
相对于
-client来说编译更彻底，性能也更好。在我们查看电脑的Java会有一个-server或者-client的标示。

</div>

<div>

这一块需要我们去调配JVM，选择适合我们的编译模式。具体的指定方式：-cient，-server
或是-xx:+TieredCompilation，长时运行就选择server多层编译方式，如果仅仅是临时执行或者短暂执行去选择-client。

</div>

<div>

\

</div>

<div>

如何调优过程：

</div>

<div>

优化代码缓存相关。

</div>

<div>

当JVM编译代码时会将汇编指令集保存在代码缓存中，并且这个缓存大小是固定的，当刚开始的时候把这块缓存填满后JVM后续除已缓存的代码外，只能通过解释来执行了。

</div>

<div>

这其实就是选择-server或者-client
的主要依据了，因为-server方式对热点代码要求会比较高，而-client相对来说就比较简单了对应的会产生大量的热点代码。

</div>

<div>

除此之外，默认的缓存大小总是少得可怜，我们在运行一些大的、长时项目是时需要适当的调大这一块区域。

</div>

<div>

-XX:InitialCodeCacheSize=N 修改默认缓存大小

</div>

<div>

--XX:ReservedCodeCacheSize=N 修改最大缓存大小。

</div>

<div>

\

</div>

<div>

编译阀值相关。

</div>

<div>

在JVM中存在这么两类计数器，一种是函数的调用次数，另一种是方法中循环被回弹的次数。（这里敲下黑板，回弹低点可不仅仅只有循环体结束处，还有可能是continue）

</div>

<div>

这两个技术相关的，就是用来判断是否达到标准去编译它。如果有，排队编译。

</div>

<div>

如果一个函数调用过程中，或者一个循环体执行过程中，虽然马上还要调用或者还会继续下一次循环，这时候也是会触发一个编译操作。将编译的结果替换当前执行的部分，这个操作叫做栈上替换。

</div>

<div>

具体使用：

</div>

<div>

-XX:CompileThreshold=Nflag 指定需要的编译次数，-client
默认应该是1500，-server为10000。这东西的修改需要一点点试调。

</div>

<div>

如果想监控或者观察JVM的编译过程，可以指定-XX:+PrintCompilation，来打印JVM中的编译过程，这一块的信息通过jstat就能看到

</div>

<div>

jstat -compiler 进称id

</div>

<div>

编译过程很直观，显然是异步的，具体的数量通过-XX:CICompilerCount=N 调整。

</div>

<div>

具体的场景，需要具体的调节，没有最好的，只有最合适的。

</div>

<div>

\

</div>

<div>

其实 除了官方的HotSpot JVM，IBM的J9
JVM也是非常值得看一看，对了还有一个叫做classic JVM。

</div>

<div>

比较着去学习，感觉效果更明显～

</div>
