---
altitude: 56.35406112670898
author: zouzq7@163.com
created: "2017-11-09 01:54:57 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03838454724018
longitude: 116.4110107113419
source: desktop.mac
title: JVM Non-Standard Options
updated: "2017-11-11 16:58:28 +0000"
---

<div>

准备对每一个参数进行详细的介绍，并且给出一些我所了解到的小技巧。

</div>

<div>

懂原理才能更好的使用嘛，推荐大家先把Java 使用熟练，然后好好看看JVM
原理相关的东西，最后再根据原理&使用 对于JVM进行更高效的利用。

</div>

<div>

下面开始介绍。

</div>

<div>

非标准参数（Non-Standard）：

</div>

<div>

\

</div>

<div>

-XBatch

</div>

<div>

虚拟机的缺省运行方式是在后台编译类代码，然后在前台执行代码，使用-XBatch参数时，会关闭虚拟机后台编译，在前台编译完成后再执行。

</div>

<div>

\

</div>

<div>

-Xbootclasspath:path

</div>

<div>

<div>

-Xbootclasspath/a:path

</div>

</div>

<div>

-Xbootclasspath/p:path

</div>

<div>

上面三个参数其实是一个参数。什么作用呢，挺简单的。就是改变虚拟机装载jar的方式，原本虚拟机是缺省系统运行包rt.jar，指定之后按照classpath中设定的搜索路径中装载系统运行类，除非我们需要写一个运行时，否则不会用到该参数。

</div>

<div>

其中存在 /a，/p 两个选项：

</div>

<div>

/a：在缺省搜索路径后架上

</div>

<div>

\

</div>

<div>

-Xcheck:jni

</div>

<div>

是否调用JNI函数进行附加的检查，特别地虚拟机将校验传递给JNI函数参数的合法性，在本地代码中遇到非法数据时，虚拟机将会报一个致命错误然后终止。使用该参数会造成性能下降不少。谨慎使用～

</div>

<div>

\

</div>

<div>

-Xcomp、-Xmixed、-Xint

</div>

<div>

这三个参数放在一块儿说吧，作用很简单就是用来指定Java
bytecode的运行方式。

</div>

<div>

其中-Xint全部使用字节码解释执行，这个是最慢的，慢的惊人，通常要比其他方式慢一个数量级左右。

</div>

<div>

-Xcomp相反，全部被编译成机器码执行，速度是很快的，但是存在一个缺陷，-Xcomp的策略过于简单，无法使用JIT的全部特性。比如
JIT会对在运行时对每个方法做统计，然后会有一些比如分支预测之类的自优化策略，但是使用-Xcomp时这个特性就无法生效了。还存在这样的一种情况，有些方法的调用比较少，但是也去做编译和优化的话，编译带来的开销还不如直接去解释执行那一块的代码，这样就产生一些不必要的开销了。这两种方式分场景来进行使用，针对合适的场景选择对应的模式。适合的才是最好的。还有一个参数，就是一种自适应的方式，有的地方解释执行，有的地方编译执行，具体的策略要依据profile的统计分析来判断。

</div>

<div>

注：中间提到了一个JIT，可能有的同学对这个不是非常熟悉。JIT 的全称是Just
in time，也就是
"即时" ，这是一种即时编译技术，能够通过保存之前编译得到的机器码来替代对应字节码的解释执行操作及一些分支预测等自优化功能来加快Java
程序的执行速度。具体是怎么做到的呢。请看 Java 系列中的JIT技术。

</div>

<div>

\

</div>

<div>

-Xdebug

</div>

<div>

这个选项，其实什么也没做。仅仅是为了向后兼容而已。

</div>

<div>

\

</div>

<div>

-Xdiag

</div>

<div>

显示额外的诊断信息，如果出现问题你又觉着信息不够全面时。

</div>

<div>

\

</div>

<div>

-Xfuture

</div>

<div>

对类文件进行严格格式的检查，以保证类代码符合类代码规范。如果为缺省则不进行严格格式检查。

</div>

<div>

\

</div>

<div>

-Xinternalversion

</div>

<div>

显示比版本选项更详细的JVM版本信息，然后退出。

</div>

<div>

这个常用于
-version之后，看到的信息不够充分，你就可以使用它了，使用场景比较少

</div>

<div>

\

</div>

<div>

-Xloggc:filename

</div>

<div>

用于指定虚拟机每次垃圾回收时将回收的信息写到日志文件中，文件名由filename
指定

</div>

<div>

\

</div>

<div>

-Xmaxjitcodesize=size

</div>

<div>

为jit编译的代码指定最大代码缓存大小缺省单位为字节，也可以是k、m。默认的最大代码缓存大小为240
MB;如果使用选项- xx:- tiered编译禁用分层编译，那么默认大小是48 MB:

</div>

<div>

-Xmaxjitcodesize = 240

</div>

<div>

这个选项相当于- xx:ReservedCodeCacheSize（下一篇会有具体的介绍）。

</div>

<div>

\

</div>

<div>

-Xmnsize

</div>

<div>

用于设置堆内新生代的大小，缺省单位为字节，也可以是k、m，后面有对应最大的虚拟机内存堆的最大可用大小，减去-Xmn就是老年代的大小。

</div>

<div>

\

</div>

<div>

-Xmssize

</div>

<div>

设置虚拟机内存堆的初始大小，缺省单位为字节（值必须为1024的整数倍，然后大于2m），当然也可以自行指定单位，比如说
k、m。缺省时，默认大小为2m。

</div>

<div>

\

</div>

<div>

-Xmxsize

</div>

<div>

设置虚拟机内存堆的最大可用大小，缺省单位为字节（值必须为1024的整数倍，然后大于2m），当然也可以自行指定单位，比如说
k、m。缺省时 默认最大为64m。

</div>

<div>

最常见的，如果我们运行的程序体量很庞大，64m已经远不够使用时，启动一段时间后可能会出现OOM，这时候就需要手动的去设置可用的堆内存的大小了。

</div>

<div>

\

</div>

<div>

-Xnoclassgc

</div>

<div>

关闭虚拟机对Class的垃圾回收功能。

</div>

<div>

\

</div>

<div>

-Xprof

</div>

<div>

输出CPU运行时的诊断信息。

</div>

<div>

\

</div>

<div>

-Xrs

</div>

<div>

减少虚拟机中操作系统信号的使用，这种参数
通常用于以后台服务方式运行，最经典的比如Servlet

</div>

<div>

\

</div>

<div>

-Xshare:(auto、on、off、dump)

</div>

<div>

对于共享类数据的偏向的设置

</div>

<div>

auto：尽可能使用共享类的数据

</div>

<div>

on：尽可能使用共享类的数据，否则运行失败

</div>

<div>

off：尽可能不使用共享类的数据

</div>

<div>

dump：生动生成类数据共享

</div>

<div>

\

</div>

<div>

-XshowSettings:(all、locale、properties、vm)

</div>

<div>

关于显示设置：

</div>

<div>

all：显示所有设置信息，并继续

</div>

<div>

vm：显示所有与vm相关的设置信息，并继续

</div>

<div>

properties：显示所有属性设置信息，并继续

</div>

<div>

locale：显示所有与区域设置相关的信息，并继续

</div>

<div>

\

</div>

<div>

-Xsssize

</div>

<div>

设置线程栈的大小，单位方式与-Xmxsize一样，默认的线程栈的大小为1m。

</div>

<div>

Java中对于线程栈的大小还是很有好的，可以在创建线程对象的时候设置栈的大小，看一下构造函数就好了：

</div>

<div>

new Thread(ThreadGroup threadGroup,Runnable target,String
name,long stackSize)，其中stackSize 说的就是线程栈的大小。

</div>

<div>

\

</div>

<div>

-Xusealtsigs

</div>

<div>

使用替代信号而不是SIGUSR1和SIGUSR2用于JVM内部信号。这个选项相当于- xx:+
UseAltSigs。

</div>

<div>

\

</div>

<div>

-Xverify:(remote、all、none)

</div>

<div>

这同样是一个检查相关的选项，不过是对于字节码进行验证，具体的约束内容可以参照section
4.10 中的约束

</div>

<div>

其中有这么几个选项：

</div>

<div>

remote：进验证非本地加载的的字节码，这是默认行为。

</div>

<div>

all：对于所有字节都会进行验证

</div>

<div>

none：都不检查。不过 这个选项貌似是不被支持，毕竟不太合适。

</div>

<div>

\

</div>

<div>

是不是觉着这些参数，顶多规范规范JVM的一些行为，或者设置设置JVM某些抽象内存块的大小，有或者显示JVM一些运行时的具体信息，感觉没有什么卵用，没事儿，因为对于常用的需求来说，以上几个基本的参数调整我们的JVM足够了，并且在那些高级选项的参数更加具体了，但是因为非常具体，因为直观的控制一些细节，所以导致数量也大的惊人。

</div>

<div>

不过经常使用的那就那么几个，好好研究一下。如果没有对应的现实问题，别怕，没有bug、创造bug也得解决。

</div>

<div>

这些具体的参数会在后面的几篇进行对应详细描述。

</div>
