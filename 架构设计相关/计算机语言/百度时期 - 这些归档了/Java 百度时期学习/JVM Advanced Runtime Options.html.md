---
altitude: 45.34604263305664
author: zouzq7@163.com
created: "2017-11-09 12:56:20 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.05661219040291
longitude: 116.4063481680826
source: desktop.mac
title: JVM Advanced Runtime Options
updated: "2019-07-18 08:53:35 +0000"
---

<div>

高级运行时选项（Advanced Runtime Options）：

</div>

<div>

-XX:+UnlockCommercialFeatures

</div>

<div>

开启商业选项，许多商业特性都需要这个选项的支持。

</div>

<div>

\

</div>

<div>

-XX:+CheckEndorsedAndExtDirs

</div>

<div>

jdk
8中新增加的一个参数，有兴趣的可以去看看openjdk中的关于这一块的实现（http://hg.openjdk.java.net/jdk8u/hs-dev/hotspot/rev/fa6adc194d48）

</div>

<div>

这个参数是用来阻止Java 命令运行应用（除非没有用到endorsed-standards
override机制&扩展机制）。

</div>

<div>

同时，这个选项会检查应用是否启动了以下机制

</div>

<div>

1、java.ext.dirs 或 java.endorsed.dirs 属性被设置

</div>

<div>

2、lib/endorsed 目录存在 && 不为空

</div>

<div>

3、lib/ext 目录下包含了除JDK以外的JAR

</div>

<div>

4、系统范围内 特定于平台的扩展目录中包含任何JAR文件

</div>

<div>

\

</div>

<div>

-XX:+DisableAttachMechanism

</div>

<div>

启动此参数之后，JVM将禁止任何工具连接，通常情况下这个选项是关闭的。外部工具指的是
jstack、jmap、jinfo等JVM辅助分析工具。

</div>

<div>

\

</div>

<div>

-XX:ErrorFile=filename

</div>

<div>

用于当出现致命错误时，指定一个目录，用来存储Error信息。默认为当前目录下的hs_err_pidpid.log，也就是
filename=./hs_err_pidpid.log

</div>

<div>

\

</div>

<div>

-XX:+FailOverToOldVerifier

</div>

<div>

当新的类型检查失败时，自动使用老的验证器。默认这个是关闭的，但是当我们需要时使用老版本的字节码的时候则需要开启这个选项。

</div>

<div>

\

</div>

<div>

-XX:+FilghtRecorder

</div>

<div>

嗯。Java 就是性能记录。这是一个商业特性，和
-XX:+UnlockCommercialFeatures
选项一起使用如果这个选项开启了，那么JVM的性能记录是不可用的。

</div>

<div>

\

</div>

<div>

-XX:-FilghtRecorder

</div>

<div>

嗯，又是性能记录。关闭了

</div>

<div>

\

</div>

<div>

-XX:FilghtRecorderOptions={

</div>

<div>

parameter=value、

</div>

<div>

defaultrecording={true\|false}、

</div>

<div>

disk={true\|false}、

</div>

<div>

dumponexit={true\|false}、

</div>

<div>

dumponexitpath=path、

</div>

<div>

globalbuffersize=size

</div>

<div>

loglevel={quiet\|error\|warning\|info\|debug\|trace}

</div>

<div>

maxage=time

</div>

<div>

maxchunksize=size

</div>

<div>

maxsize=size

</div>

<div>

repository=path

</div>

<div>

samplethreads={true\|false}

</div>

<div>

settings=path

</div>

<div>

stackdepth=depth

</div>

<div>

threadbuffersize=size

</div>

<div>

}

</div>

<div>

defaultrecording:
指定是否在后台一只记录还是只运行一段时间，默认这个参数的值是false。如果要一直开启，请设置为true。

</div>

<div>

disk：是否JRE持续的把记录写到硬盘中，默认false，如果想要持续记录，需要设置为true。

</div>

<div>

dumponexit：是否在JVM终止的时候记录JFE的数据

</div>

<div>

dumponexitpath：JVM终止是记录JFE的数据的路径，如果指定的是一个目录
JVM会自动创建一个文件（文件名一般是以当前时间生成），若是文件名，如果这个文件名已经存在了，通常会加一个时间后缀来区分。这个参数如果不生效，上一个参数的选项也是不成立的

</div>

<div>

globalbuffersize=size：指定保留数据的总大小。

</div>

<div>

loglevel：JFE日志的日志级别，默认 Info

</div>

<div>

maxage：设置数据对大的保留时间

</div>

<div>

maxchunksize=size：设置数据最大块的大小

</div>

<div>

maxsize=size：设置数据在硬盘的最大容量，默认容量没有限制，前提：仅当disk=true时，此选项可用。

</div>

<div>

respository=path：设置临时仓库，默认使用系统的临时路径

</div>

<div>

samplethreads：设置是否进行线程抽样，默认为true

</div>

<div>

setting=path：设置事件配置文件，默认是使用JAVA_HOME/jre/lib/default.jfc

</div>

<div>

stackdepth=depth：设置对应栈追踪的深度，默认深度为64

</div>

<div>

threadbuffersize=size：指定每个线程的本地缓冲的大小，默认大小为5k

</div>

<div>

\

</div>

<div>

\

</div>

<div>

-XX:LargePageSizeInBytes=size

</div>

<div>

设置内存页的大小，但是注意，在Solaris上这个参数应该为2的幂。并且可以使用后缀单位，默认值为0，JVM自动选择大小。

</div>

<div>

\

</div>

<div>

-XX:MaxDirectMemorySize=size

</div>

<div>

设置直接内存的最大容量（NIO），这一点需要注意一下，尤其是项目中经常存在IO操作时。

</div>

<div>

有时候NIO用起来，异常坑。

</div>

<div>

\

</div>

<div>

-XX:NativeMemoryTracking={ off、summary、detail }

</div>

<div>

指定追踪JVM本地方法使用的模式，

</div>

<div>

off：不跟踪本地方法的使用，这是默认的模式

</div>

<div>

summary：仅仅追踪JVM子系统的内存使用，比如java 堆，类，代码，线程

</div>

<div>

detail：除了跟踪内存使用的JVM子系统，通过单个调用callSite跟踪内存使用量、单个虚拟机区域&其委托区域

</div>

<div>

\

</div>

<div>

-XX:ObjectAlignmentInBytes=alignment

</div>

<div>

设置Java
对象的内存对齐，默认为8字节，制定值必须为2的幂，且必须在8～256之间。

</div>

<div>

没想到吧，这个竟然也能改

</div>

<div>

\

</div>

<div>

-XX:OnError=string

</div>

<div>

设置自定义的命令（使用逗号分隔），当发生致命错误时被执行

</div>

<div>

\

</div>

<div>

-XX:OnOutOfMemoryError=string

</div>

<div>

当发生OOM时所执行的命令

</div>

<div>

\

</div>

<div>

-XX:+PerfDataSaveToFile

</div>

<div>

在退出时保存jstat的二进制数据，名称：hsperfdata\_。

</div>

<div>

当然也可以在jstat 保存数据

</div>

<div>

\

</div>

<div>

-XX:+PrintCommandLineFlags

</div>

<div>

输出JVM设置的选项和值，默认关闭

</div>

<div>

\

</div>

<div>

-XX:+PrintNMTStatistics

</div>

<div>

如果-XX：NativeMemoryTracking选线开启，那么这个选项会在JVM退出时打印本地内存的手机数据。默认关闭

</div>

<div>

\

</div>

<div>

-XX:+RelaxAccessControlCheck

</div>

<div>

较少访问控制检查，默认关闭

</div>

<div>

\

</div>

<div>

-XX:+ResourceManagement

</div>

<div>

在运行期间，启动资源管理

</div>

<div>

这是个商业特性，如果需要使用的话需要开启-XX:+UnlockCommercialFeatures

</div>

<div>

\

</div>

<div>

-XX:ResourceManagementSampleInterval=value (milliseconds)

</div>

<div>

设置资源管理的采样间隔，只有启动采样时这个选项才有作用（-XX：+RecourseManagement）

</div>

<div>

\

</div>

<div>

-XX:SharedArchiveFile=path

</div>

<div>

指定类数据分享文档目录

</div>

<div>

\

</div>

<div>

-XX:SharedClassListFile=file_name

</div>

<div>

指定一个文本文件，其中包含要存储到类共享文档中的类名。并且在命名规则中可以使用'／'替代'.'

</div>

<div>

这些在文件中指定的类是被经常使用的类。

</div>

<div>

\

</div>

<div>

-XX:+ShowMessageBoxOnError

</div>

<div>

当JVM出现致命错误时，弹出一Msg
阻止JVM的退出。这时候我们可以使用JVM的外部调试工具连接JVM来查看对应的原因。这个在JVM排错时比较有用，尤其是无法直观获得错误原因时。默认关闭

</div>

<div>

\

</div>

<div>

-XX:StartFlightRecording={

</div>

<div>

parameter=value、

</div>

<div>

compress={true\|false}、

</div>

<div>

defaultrecording={true\|false}、

</div>

<div>

delay=time、

</div>

<div>

dumponexit={true\|false}、

</div>

<div>

duration=time、

</div>

<div>

filename=path、

</div>

<div>

name=identifier、

</div>

<div>

maxage=time、

</div>

<div>

maxsize=size、

</div>

<div>

settings=path

</div>

<div>

}

</div>

<div>

开启性能记录选项，这是一个商业特性同样的需要开启-XX:+UnlockCommercialFeatures，监控&后期分析JVM行为是比较有用的。

</div>

<div>

compress：是否执行压缩操作

</div>

<div>

defaultrecording：持续开启，还是只开一段时间。设置为true 时是持续开启的

</div>

<div>

delay：开启JVM后多久开始性能记录，默认值为0，即马上开始记录

</div>

<div>

dumponexit：在退出时是否产生性能记录

</div>

<div>

duration：性能记录的执行时长

</div>

<div>

filename：JFR记录的文件&路径

</div>

<div>

name：指定JFR记录的标示

</div>

<div>

maxage：性能记录的保存时间

</div>

<div>

setting=path：设置事件配置文件，默认是使用JAVA_HOME/jre/lib/default.jfc

</div>

<div>

以上为性能记录的启动参数选项，跟-XX:FilghtRecorderOptions参数的选项很多重复项，其实说的就是一个东西，一个是性能记录的基础的参数设置，一个是启动时设定罢了

</div>

<div>

\

</div>

<div>

-XX:ThreadStackSize=size

</div>

<div>

设置线程栈的深度或者大小。

</div>

<div>

之前有一个非标准参数 -Xss 和这个功能是相同的

</div>

<div>

\

</div>

<div>

-XX:+TraceClassLoading

</div>

<div>

-XX:+TraceClassLoadingPreorder

</div>

<div>

-XX:+TraceClassResolution

</div>

<div>

-XX:+TraceClassUnloading

</div>

<div>

-XX:+TraceLoaderConstraints

</div>

<div>

以上都为JVM的跟踪行为，只不过每一项指定的跟踪项不同

</div>

<div>

Loading 为类加载跟踪

</div>

<div>

LoadingPreorder 按照引用顺序跟踪类加载

</div>

<div>

Resolution 跟踪常量池行为

</div>

<div>

Unloading 跟踪类的卸载行为

</div>

<div>

LoaderConstraints 跟踪类加载器的约束行为

</div>

<div>

\

</div>

<div>

-XX:+UseAltSigs

</div>

<div>

\

</div>

<div>

\

</div>

<div>

-XX:+UseAppCDS

</div>

<div>

这也是个商业特性，同样需要开启-XX:+UnlockCommercialFeatures。

</div>

<div>

UseAppCDS属于体验特性，将来应该会发生变化。

</div>

<div>

作用：启动应用类数据共享（AppCDS），为了使用AppCDS，必须制定-XX:SharedClassListFile和-XX:SharedArchiveFile
两个选项。

</div>

<div>

\

</div>

<div>

-XX:-UseBiasedLocking

</div>

<div>

禁止使用偏向锁，这个还是经常使用的。

</div>

<div>

\

</div>

<div>

-XX:-UseCompressedOops

</div>

<div>

这个选项只用在64位操作系统下使用，默认压缩指针开启的。

</div>

<div>

当开启时，对象引用是32位的而不是64位，能够减少内存的使用。

</div>

<div>

\

</div>

<div>

-XX:+UseHugeTLBFS

</div>

<div>

在Linux中，这个选项相当于指定- xx:+
UseLargePages。实验特性，默认情况下是禁用的。此选项预先分配所有大页面，当内存保留时;因此，JVM不能动态地增长或缩小大页内存区域;看到-
xx:UseTransparentHugePages如果你想要这种行为。

</div>

<div>

\

</div>

<div>

-XX:+UseLargePages

</div>

<div>

启动大的内存分页，默认关闭

</div>

<div>

\

</div>

<div>

-XX:+UseMembar

</div>

<div>

在线程转换时使用内存栅栏，默认关闭。

</div>

<div>

\

</div>

<div>

-XX:+UsePerfData

</div>

<div>

使用PerfData特性，默认开启

</div>

<div>

\

</div>

<div>

-XX:+UseTransparentHugePages

</div>

<div>

在Linux 中，允许使用动态增长或者扩展的页面。

</div>

<div>

算是实验特性，默认是被禁用的，因为当操作系统移动其他页面以创建巨大的页面时，可能会遇到巨大页面创建的性能问题。

</div>

<div>

\

</div>

<div>

-XX:+AllowUserSignalHandlers

</div>

<div>

允许使用信号处理器，默认为不开启。

</div>

<div>

\

</div>
