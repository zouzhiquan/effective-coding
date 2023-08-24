---
altitude: 52.81100845336914
author: zouzq7@163.com
created: "2017-09-27 03:17:48 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03874655057724
longitude: 116.4107794461332
source: desktop.mac
title: JVM 《九 JVM 常见参数及调优》
updated: "2017-09-28 09:08:48 +0000"
---

<div>

哈哈，这一篇我实话，我想发一个空白的。并没有什么经验，有的也都是间接看书或者blog去学的。只会试调参数、知道这些参数是干啥的，会有什么相互作用。别的我真的玩不了。。。。。

</div>

<div>

\

</div>

<div>

调优什么的，等我沉淀好了再发吧。要不拿着间接经验扯一堆儿着实没什么劲。（所以这一篇
 还会有一整个系列来说单独的参数&调优）

</div>

<div>

\

</div>

<div>

这一篇先说说有哪些参数，其实啊。

</div>

<div>

\

</div>

<div>

MaxHeapFreeRatio:
GC后如果发现空闲堆内存占到整个预估堆内存的N%(百分比)，则收缩堆内存的预估最大值。

</div>

<div>

MinHeapFreeRatio: GC后如果发现空闲堆内存占到整个预估堆内存的N%(百分比),
则放大堆内存的预估最大值

</div>

<div>

MaxHeapSize: 即-Xmx, 堆内存大小的上限

</div>

<div>

InitialHeapSize: 即-Xms, 堆内存大小的初始值

</div>

<div>

NewSize: 新生代预估堆内存占用的默认值

</div>

<div>

MaxNewSize: 新生代占整个堆内存的最大值

</div>

<div>

OldSize: 老年代的默认大小, default size of the tenured generation

</div>

<div>

NewRatio:
年代对比新生代的空间大小,比如2代表老年代空间是新生代的两倍大小.

</div>

<div>

SurvivorRatio: Eden/Survivor的值.

</div>

<div>

MetaspaceSize:
分配给类元数据空间的初始大小(Oracle逻辑存储上的初始高水位，the initial
high-water-mark ).

</div>

<div>

MaxMetaspaceSize: 是分配给类元数据空间的最大值, 超过此值就会触发Full GC.
此值仅受限于系统内存的大小, JVM会动态地改变此值

</div>

<div>

CompressedClassSpaceSize: 类指针压缩空间大小, 默认为1G

</div>

<div>

G1HeapRegionSize: G1区块的大小, 取值为1M至32M.
其取值是要根据最小Heap大小划分出2048个区块.

</div>

<div>

\

</div>

<div>

这种东西不需要去百度的，只要你电脑上有Java就够了。然后通过 jmap、jstack 什么的你会了解到更多与JVM相关的东西，动手永远是学习最好的方式。下面说说我当时怎么玩的

</div>

<div>

写个程序，取一下进程ID、然后顺道跑起一个程序来。

</div>

<div>

![](JVM%20%E3%80%8A%E4%B9%9D%20JVM%20%E5%B8%B8%E8%A7%81%E5%8F%82%E6%95%B0%E5%8F%8A%E8%B0%83%E4%BC%98%E3%80%8B.resources/D3B642B8-5412-4927-8EA1-379FDE274B33.png) 
 

</div>

<div>

这里取了JVM的id

</div>

<div>

\

</div>

<div>

然后开终端

</div>

<div>

这里只是举几个小例子，其他的慢慢来

</div>

<div>

[[zouzhiquande-MacBook-Pro:\~ zouzhiquan\$ cd /\
zouzhiquande-MacBook-Pro:/ zouzhiquan\$ cd Library/\
zouzhiquande-MacBook-Pro:Library zouzhiquan\$ ls\
Application Support DirectoryServices Keychains Printers Security\
Audio Documentation LaunchAgents PrivilegedHelperTools Speech\
Caches Extensions LaunchDaemons Python Spotlight\
Catacomb Filesystems Logs QuickLook StartupItems\
ColorPickers Fonts Managed Preferences QuickTime SystemProfiler\
ColorSync Frameworks Messages Raft Updates\
Components Graphics Modem Scripts Receipts User Pictures\
Compositions Image Capture OpenDirectory Ruby Video\
Contextual Menu Items Input Methods PDF Services Sandbox WebServer\
CoreMediaIO Internet Plug-Ins Perl Screen Savers Widgets\
Desktop Pictures Java PreferencePanes ScriptingAdditions tomcat\
Developer Keyboard Layouts Preferences Scripts zookeeper-3.4.9\
zouzhiquande-MacBook-Pro:Library zouzhiquan\$ cd Java/\
zouzhiquande-MacBook-Pro:Java zouzhiquan\$ cd JavaVirtualMachines/\
zouzhiquande-MacBook-Pro:JavaVirtualMachines zouzhiquan\$ cd
jdk1.8.0_73.jdk/\
zouzhiquande-MacBook-Pro:jdk1.8.0_73.jdk zouzhiquan\$ cd Contents/\
zouzhiquande-MacBook-Pro:Contents zouzhiquan\$ cd bin\
-bash: cd: bin: No such file or directory\
zouzhiquande-MacBook-Pro:Contents zouzhiquan\$ cd Home/\
zouzhiquande-MacBook-Pro:Home zouzhiquan\$ ls\
COPYRIGHT bin lib\
LICENSE db man\
README.html include release\
THIRDPARTYLICENSEREADME-JAVAFX.txt javafx-src.zip src.zip\
THIRDPARTYLICENSEREADME.txt jre\
zouzhiquande-MacBook-Pro:Home zouzhiquan\$ cd bin/\
zouzhiquande-MacBook-Pro:bin zouzhiquan\$ jmap -heap 94680\
Attaching to process ID 94680, please wait\...\
Debugger attached successfully.\
Server compiler detected.\
JVM version is 25.73-b02\
\
using thread-local object allocation.\
Parallel GC with 4 thread(s)\
\
Heap Configuration:\
   MinHeapFreeRatio         = 0\
   MaxHeapFreeRatio         = 100\
   MaxHeapSize              = 2147483648 (2048.0MB)\
   NewSize                  = 44564480 (42.5MB)\
   MaxNewSize               = 715653120 (682.5MB)\
   OldSize                  = 89653248 (85.5MB)\
   NewRatio                 = 2\
   SurvivorRatio            = 8\
   MetaspaceSize            = 21807104 (20.796875MB)\
   CompressedClassSpaceSize = 1073741824 (1024.0MB)\
   MaxMetaspaceSize         = 17592186044415 MB\
   G1HeapRegionSize         = 0 (0.0MB)\
\
Heap Usage:\
PS Young Generation\
Eden Space:\
   capacity = 34078720 (32.5MB)\
   used     = 8276088 (7.892692565917969MB)\
   free     = 25802632 (24.60730743408203MB)\
   24.28520789513221% used\
From Space:\
   capacity = 5242880 (5.0MB)\
   used     = 0 (0.0MB)\
   free     = 5242880 (5.0MB)\
   0.0% used\
To Space:\
   capacity = 5242880 (5.0MB)\
   used     = 0 (0.0MB)\
   free     = 5242880 (5.0MB)\
   0.0% used\
PS Old Generation\
   capacity = 89653248 (85.5MB)\
   used     = 0 (0.0MB)\
   free     = 89653248 (85.5MB)\
   0.0% used\
\
941 interned Strings occupying 64560 bytes.\
zouzhiquande-MacBook-Pro:bin zouzhiquan\$ jstack -h\
Usage:\
    jstack \[-l\] \<pid\>\
        (to connect to running process)\
    jstack -F \[-m\] \[-l\] \<pid\>\
        (to connect to a hung process)\
    jstack \[-m\] \[-l\] \<executable\> \<core\>\
        (to connect to a core file)\
    jstack \[-m\] \[-l\] \[server_id@\]\<remote server IP or hostname\>\
        (to connect to a remote debug server)\
\
Options:\
    -F  to force a thread dump. Use when jstack \<pid\> does not respond
(process is hung)\
    -m  to print both java and native frames (mixed mode)\
    -l  long listing. Prints additional information about locks\
    -h or -help to print this help message\
zouzhiquande-MacBook-Pro:bin zouzhiquan\$ jstack -l 94680\
2017-09-27 11:48:49\
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.73-b02 mixed
mode):\
\
\"Attach Listener\" #10 daemon prio=9 os_prio=31 tid=0x00007f957d84a000
nid=0x1207 waiting on condition \[0x0000000000000000\]\
   java.lang.Thread.State: RUNNABLE\
\
   Locked ownable synchronizers:\
- None\
\
\"Service Thread\" #9 daemon prio=9 os_prio=31 tid=0x00007f957d057800
nid=0x4b03 runnable \[0x0000000000000000\]\
   java.lang.Thread.State: RUNNABLE\
\
   Locked ownable synchronizers:\
- None\
\
\"C1 CompilerThread2\" #8 daemon prio=9 os_prio=31
tid=0x00007f957d835800 nid=0x4903 waiting on condition
\[0x0000000000000000\]\
   java.lang.Thread.State: RUNNABLE\
\
   Locked ownable synchronizers:\
- None\
\
\"C2 CompilerThread1\" #7 daemon prio=9 os_prio=31
tid=0x00007f957d814000 nid=0x4703 waiting on condition
\[0x0000000000000000\]\
   java.lang.Thread.State: RUNNABLE\
\
   Locked ownable synchronizers:\
- None\
\
\"C2 CompilerThread0\" #6 daemon prio=9 os_prio=31
tid=0x00007f957c877800 nid=0x4503 waiting on condition
\[0x0000000000000000\]\
   java.lang.Thread.State: RUNNABLE\
\
   Locked ownable synchronizers:\
- None\
\
\"Monitor Ctrl-Break\" #5 daemon prio=5 os_prio=31
tid=0x00007f957c876000 nid=0x4303 runnable \[0x0000700009e14000\]\
   java.lang.Thread.State: RUNNABLE\
at java.net.SocketInputStream.socketRead0(Native Method)\
at java.net.SocketInputStream.socketRead(SocketInputStream.java:116)\
at java.net.SocketInputStream.read(SocketInputStream.java:170)\
at java.net.SocketInputStream.read(SocketInputStream.java:141)\
at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)\
at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)\
at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)\
- locked \<0x00000007957af7a8\> (a java.io.InputStreamReader)\
at java.io.InputStreamReader.read(InputStreamReader.java:184)\
at java.io.BufferedReader.fill(BufferedReader.java:161)\
at java.io.BufferedReader.readLine(BufferedReader.java:324)\
- locked \<0x00000007957af7a8\> (a java.io.InputStreamReader)\
at java.io.BufferedReader.readLine(BufferedReader.java:389)\
at
com.intellij.rt.execution.application.AppMainV2\$1.run(AppMainV2.java:64)\
\
   Locked ownable synchronizers:\
- None\
\
\"Signal Dispatcher\" #4 daemon prio=9 os_prio=31 tid=0x00007f957c81b800
nid=0x4103 runnable \[0x0000000000000000\]\
   java.lang.Thread.State: RUNNABLE\
\
   Locked ownable synchronizers:\
- None\
\
\"Finalizer\" #3 daemon prio=8 os_prio=31 tid=0x00007f957c815000
nid=0x3103 in Object.wait() \[0x0000700009c0e000\]\
   java.lang.Thread.State: WAITING (on object monitor)\
at java.lang.Object.wait(Native Method)\
- waiting on \<0x0000000795587110\> (a
java.lang.ref.ReferenceQueue\$Lock)\
at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:143)\
- locked \<0x0000000795587110\> (a java.lang.ref.ReferenceQueue\$Lock)\
at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:164)\
at java.lang.ref.Finalizer\$FinalizerThread.run(Finalizer.java:209)\
\
   Locked ownable synchronizers:\
- None\
\
\"Reference Handler\" #2 daemon prio=10 os_prio=31
tid=0x00007f957e02a800 nid=0x2f03 in Object.wait()
\[0x0000700009b0b000\]\
   java.lang.Thread.State: WAITING (on object monitor)\
at java.lang.Object.wait(Native Method)\
- waiting on \<0x0000000795586b50\> (a java.lang.ref.Reference\$Lock)\
at java.lang.Object.wait(Object.java:502)\
at java.lang.ref.Reference\$ReferenceHandler.run(Reference.java:157)\
- locked \<0x0000000795586b50\> (a java.lang.ref.Reference\$Lock)\
\
   Locked ownable synchronizers:\
- None\
\
\"main\" #1 prio=5 os_prio=31 tid=0x00007f957d007000 nid=0x1c03 runnable
\[0x00007000094f9000\]\
   java.lang.Thread.State: RUNNABLE\
at com.company.Main.main(Main.java:26)\
\
   Locked ownable synchronizers:\
- None\
\
\"VM Thread\" os_prio=31 tid=0x00007f957d00b000 nid=0x2d03 runnable\
\
\"GC task thread#0 (ParallelGC)\" os_prio=31 tid=0x00007f957c803000
nid=0x2503 runnable\
\
\"GC task thread#1 (ParallelGC)\" os_prio=31 tid=0x00007f957c804800
nid=0x2703 runnable\
\
\"GC task thread#2 (ParallelGC)\" os_prio=31 tid=0x00007f957c805000
nid=0x2903 runnable\
\
\"GC task thread#3 (ParallelGC)\" os_prio=31 tid=0x00007f957c805800
nid=0x2b03 runnable\
\
\"VM Periodic Task Thread\" os_prio=31 tid=0x00007f957e091800 nid=0x4d03
waiting on condition\
\
] 

</div>

<div>

[[[[JNI global references:
25] 

</div>

<div>

[[[[\
] 

</div>

<div>

\

</div>

<div>

我不信有人看完了

</div>

<div>

\

</div>
