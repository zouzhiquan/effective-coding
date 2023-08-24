---
altitude: 45.5999870300293
author: zouzq7@163.com
created: "2017-11-12 13:28:35 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.0567533564334
longitude: 116.4061575427833
source: desktop.mac
title: JVM Advanced JIT Compiler Options
updated: "2018-08-07 07:20:45 +0000"
---

<div>

JIT相关编译选项 Advanced JIT Compiler Options

</div>

<div>

\

</div>

<div>

-XX:+AggressiveOpts

</div>

<div>

最核心的应该是加快编译，在JDK
6之后就默认启用的，启用一些诸如编译优化、偏向锁、并行化老年代收集策略。通常是不用管的，默认的优化策略，添加此参数的原因是为工程提供一个优化技术选择的空间。

</div>

<div>

\

</div>

<div>

-XX:AllocateInstancePrefetchLines=lines

</div>

<div>

在实例分配指针之前设置要预取的行数。默认情况下，要预取的行数设置为1。-
xx:AllocateInstancePrefetchLines = 1只有Java
HotSpot服务器VM支持这个选项。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

-XX:AllocatePrefetchDistance=size

</div>

<div>

设置对象分配的预取距离，每个Java线程都有自己的分配点。默认值随JVM运行的平台而异。在这个距离(以字节为单位)，在最后一个分配对象的地址之外，以新对象的值写入内存。

</div>

<div>

\

</div>

<div>

-XX:AllocatePrefetchInstr=instruction

</div>

<div>

将预取指令设置为在分配指针之前预取。只有Java HotSpot Server
VM支持此选项。可能的值从0到3。值背后的实际指令取决于平台。默认情况下，预取指令设置为0:-
xx:AllocatePrefetchInstr = 0 只有Java HotSpot服务器VM支持这个选项。

</div>

<div>

\

</div>

<div>

-XX:AllocatePrefetchLines=lines

</div>

<div>

使用JIT编译代码中生成的预取指令，在最后一个对象分配之后加载的缓存行数。如果最后分配的对象是实例，默认值为1;如果是数组，默认值为3。

</div>

<div>

\

</div>

<div>

-XX:AllocatePrefetchStepSize=size

</div>

<div>

设置顺序预取指令的步骤大小(以字节为单位)。附加字母k或k表示千字节，m或m表示兆字节，g或g表示千兆字节。默认情况下，步长设置为16字节:-
xx:AllocatePrefetchStepSize = 16,只有Java HotSpot服务器VM支持这个选项。

</div>

<div>

\

</div>

<div>

-XX:AllocatePrefetchStyle=style

</div>

<div>

为预取指令生成代码样式。

</div>

<div>

0 -无预取指令产生\*d\*，

</div>

<div>

1 -每次分配后执行预取指令，

</div>

<div>

2 -执行预取指令时，使用TLAB分配标记指针到gate。

</div>

<div>

\

</div>

<div>

-XX:+BackgroundCompilation

</div>

<div>

启动后台默认编译

</div>

<div>

\

</div>

<div>

-XX:CICompilerCount=threads

</div>

<div>

[调整编译线程的数目] 

</div>

<div>

\

</div>

<div>

-XX:CodeCacheMinimumFreeSpace=size

</div>

<div>

缓存代码最小空内存，防止内存溢出

</div>

<div>

\

</div>

<div>

-XX:CompileCommand=command,method\[,option(break、compileonly、dontinline、exclude、help、inline、log、option、print、quiet)\]

</div>

<div>

该参数用于定制编译需求，比如过滤某个方法不做JIT编译，若未指定方法描述符，则对全部同名方法执行命令操作

</div>

<div>

\

</div>

<div>

-XX:CompileCommandFile=filename

</div>

<div>

配合上述指令来使用，指定.hotspot_compiler文件为其他文件。

</div>

<div>

\

</div>

<div>

-XX:CompileOnly=methods

</div>

<div>

仅编译项

</div>

<div>

\

</div>

<div>

-XX:CompileThreshold=invocations

</div>

<div>

编译前方法调用/分支的数目【-client默认是1500】

</div>

<div>

\

</div>

<div>

-XX:+DoEscapeAnalysis

</div>

<div>

逃逸分析优化-栈上分配

</div>

<div>

\

</div>

<div>

-XX:InitialCodeCacheSize=size

</div>

<div>

设置初始代码缓存的大小

</div>

<div>

\

</div>

<div>

-XX:+Inline

</div>

<div>

使方法内联。默认情况下，这个选项可以提高性能。要禁用方法内联，指定-XX:-Inline即可。

</div>

<div>

\

</div>

<div>

-XX:InlineSmallCode=size

</div>

<div>

当生成本地代码的大小小于给定的值时，内联这个预编译方法。

</div>

<div>

\

</div>

<div>

-XX:+LogCompilation

</div>

<div>

允许将编译活动记录到名为hotspot的文件中。登录当前工作目录。可以使用-XX:LogFile选项指定不同的日志文件路径和名称。

</div>

<div>

\

</div>

<div>

默认情况下，禁用此选项，不记录编译活动。必须将-XX:+ logcompile选项与-XX:
unlockdiagnostics vmoptions选项一起使用，该选项解除诊断JVM选项的锁定。

</div>

<div>

\

</div>

<div>

您可以通过使用-XX:+
printcompile选项，在每次编译方法时，通过向控制台打印的消息来启用详细诊断输出。

</div>

<div>

\

</div>

<div>

-XX:MaxInlineSize=size

</div>

<div>

内联一个方法的最大字节码大小

</div>

<div>

\

</div>

<div>

-XX:MaxNodeLimit=nodes

</div>

<div>

设置在单个方法编译期间要使用的最大节点数。

</div>

<div>

\

</div>

<div>

-XX:MaxTrivialSize=size

</div>

<div>

设置要内联的小方法的最大字节码大小(以字节为单位)。附加字母k或k表示千字节，m或m表示兆字节，g或g表示千兆字节。

</div>

<div>

\

</div>

<div>

-XX:+OptimizeStringConcat

</div>

<div>

可能的话优化字符串连接操作（从Java6引入）

</div>

<div>

\

</div>

<div>

-XX:+PrintAssembly

</div>

<div>

允许使用外部反汇编程序打印字节码和本机方法的汇编代码。所以图书馆。这使您能够看到生成的代码，这可以帮助您诊断性能问题。

</div>

<div>

\

</div>

<div>

-XX:+PrintCompilation

</div>

<div>

当方法被编译时打印消息

</div>

<div>

\

</div>

<div>

-XX:+PrintInlining

</div>

<div>

允许打印内部信息。能够看到哪些方法是内联的。

</div>

<div>

\

</div>

<div>

-XX:ReservedCodeCacheSize=size

</div>

<div>

保留的代码缓存大小-最大的代码缓存大小

</div>

<div>

\

</div>

<div>

-XX:RTMAbortRatio=abort_ratio

</div>

<div>

RTM中止比率被指定为所有已执行的RTM事务的百分比(%)。如果许多中止的事务超过这个比率，那么编译后的代码将被取消优化。在启用-XX:+UseRTMDeoptoption时使用此比率。这个选项的默认值是50。这意味着，如果50%的事务被中止，编译后的代码将被反优化。

</div>

<div>

\

</div>

<div>

-XX:RTMRetryCount=number_of_retries

</div>

<div>

RTM锁定代码将被重新尝试，当它被中止或繁忙时，在回到正常锁定机制之前，该选项指定的次数。此选项的默认值为5。必须启用-XX:
usertmlocked选项。

</div>

<div>

\

</div>

<div>

-XX:-TieredCompilation

</div>

<div>

禁止使用分层编译。默认情况下，这个选项是启用的。只有Java
HotSpot服务器VM支持这个选项。

</div>

<div>

\

</div>

<div>

-XX:+UseAES

</div>

<div>

为Intel、AMD和SPARC硬件提供基于硬件的AES特性。Intel
Westmere(2010和更新的)，AMD推土机(2011和更新的)和SPARC
(T4和更新的)是支持的硬件。UseAES与useaesintrinsic一起使用。

</div>

<div>

\

</div>

<div>

-XX:+UseAESIntrinsics

</div>

<div>

UseAES和useaesentisics标志是默认启用的，只支持Java HotSpot Server VM
32位和64位。要禁用基于硬件的AES intrinsic，请指定-XX:-UseAES -XX:-
useaesintrinsic。例如，要启用硬件AES，请使用以下:

</div>

<div>

\- xx:+使用- xx:+ UseAESIntrinsics

</div>

<div>

为了支持UseAES和useaesendsics标志，32位和64位使用-服务器选项选择Java
HotSpot Server VM。客户端VM不支持这些。

</div>

<div>

\

</div>

<div>

-XX:+UseCodeCacheFlushing

</div>

<div>

开启了TiredCompilation,
会导致占用CodeCache增大，如果超出CodeCache的size限制，会触发：

</div>

<div>

1\. 停止新Jit，直到有空间；

</div>

<div>

2\. 启动CodeCache清理, 释放空间, 一定条件下会导致JIT被关闭

</div>

<div>

\

</div>

<div>

-XX:+UseCondCardMark

</div>

<div>

此选项在默认情况下是禁用的，应该只在具有多个套接字的机器上使用，这将提高严重依赖并发操作的Java应用程序的性能。只有Java
HotSpot服务器VM支持这个选项。

</div>

<div>

\

</div>

<div>

-XX:+UseRTMDeopt

</div>

<div>

根据中止比率自动调优RTM锁定。此比率由-XX:RTMAbortRatio选项指定。如果中止事务的数量超过了abort比率，那么包含锁的方法将被优化并以所有锁作为正常锁重新编译。默认情况下禁用此选项。必须启用-XX:+
usertmlocked选项。

</div>

<div>

\

</div>

<div>

-XX:+UseRTMLocking

</div>

<div>

为所有膨胀的锁生成受限制的事务内存(RTM)锁定代码，使用常规的锁定机制作为回退处理程序。默认情况下禁用此选项。与RTM相关的选项仅适用于支持事务同步扩展(TSX)的x86
cpu上的Java HotSpot服务器VM。

</div>

<div>

\

</div>

<div>

RTM是Intel的TSX的一部分，TSX是一个x86指令集扩展，有助于创建多线程应用程序。RTM引入了新的指令XBEGIN、XABORT、XEND和XTEST。XBEGIN和XEND指令包含一组作为事务运行的指令。如果在运行事务时没有发现冲突，则在XEND指令中一起提交内存和寄存器修改。可以使用XABORT指令显式地中止事务，并使用theXEND指令检查事务中是否正在运行一组指令。

</div>

<div>

当另一个线程试图访问相同的事务时，对事务的锁被夸大，从而阻塞原本没有请求访问事务的线程。RTM要求在事务中止或失败时指定一个备用操作集。RTM锁是已经委托给TSX系统的锁。

</div>

<div>

RTM提高了在关键区域(即不能同时被多个线程访问的代码)中具有低冲突的高争用锁的性能。RTM还提高了粗粒度锁的性能，这种锁在多线程应用程序中通常表现不佳。(粗粒度锁是长时间持有锁的策略，以最小化获取和释放锁的开销，而细粒度锁则是仅在必要时锁定和尽快解锁，以实现最大的并行性的策略。)另外，对于由不同线程使用的轻度争用锁，RTM可以减少错误的缓存线共享，也称为缓存线ping-pong键。当来自不同处理器的多个线程访问不同的资源，但是资源共享相同的缓存行时，就会发生这种情况。因此，处理器重复地使其他处理器的缓存线路无效，这迫使它们从主内存中读取，而不是从缓存中读取。

</div>

<div>

-XX:+UseSHA、-XX:+UseSHA1Intrinsics、-XX:+UseSHA256Intrinsics、-XX:+UseSHA512Intrinsics

</div>

<div>

\

</div>

<div>

-XX:+UseSuperWord

</div>

<div>

允许将标量操作转换为SuperWord操作。此选项默认启用。要禁用转换为SuperWord操作，指定-XX:-UseSuperWord即可。只有Java
HotSpot服务器VM支持这个选项。

</div>

<div>

\

</div>
