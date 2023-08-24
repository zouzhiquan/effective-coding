---
altitude: 56.43725967407227
author: zouzq7@163.com
created: "2017-11-13 07:22:54 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03840315396977
longitude: 116.41100419284
source: desktop.mac
title: JVM Advanced Serviceability Options
updated: "2018-04-07 14:12:04 +0000"
---

<div>

Advanced Serviceability Options

</div>

<div>

这个系列的参数其实就是用来问题发生时来打印详细信息帮助分析的。

</div>

<div>

\

</div>

<div>

-XX:+ExtendedDTraceProbes

</div>

<div>

启动Dtrace探测器。

</div>

<div>

注：Dtrace，Dynamic tracing
即动态跟踪。通常用于性能跟踪，方便发现系统的瓶颈，及待提升之处。 

</div>

<div>

\

</div>

<div>

-XX:+HeapDumpOnOutOfMemory

</div>

<div>

当发生OOM时转储Heap，这个选项最好开一下。有利于分析问题。

</div>

<div>

\

</div>

<div>

-XX:HeapDumpPath=path

</div>

<div>

堆转储文件路径

</div>

<div>

\

</div>

<div>

\

</div>

<div>

-XX:LogFile=path

</div>

<div>

log 文件路径

</div>

<div>

\

</div>

<div>

-XX:+PrintClassHistogram

</div>

<div>

打印实例的数量还有所占空间的大小

</div>

<div>

\

</div>

<div>

-XX:+PrintConcurrentLocks

</div>

<div>

在服务退出时，打印concurrent下的锁信息。

</div>

<div>

\

</div>

<div>

-XX:+UnlockDiagnosticVMOptions

</div>

<div>

解锁任何额外的隐藏JVM参数

</div>
