---
altitude: 55.02896881103516
author: zouzq7@163.com
created: "2017-11-08 02:51:16 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03838119229489
longitude: 116.4110217220843
source: desktop.mac
title: JVM 参数列表
updated: "2017-11-12 13:28:24 +0000"
---

<div>

这一篇说一下JVM 参数相关，首先JVM参数中包含着么几类
标准参数（Standard）、非标准参数（Non-Standard）、高级运行时选项、JIT相关编译高级选项、适用性高级选项、GC相关高级参数、不建议使用的参数（可能会被弃用）

</div>

<div>

后续会对每个系列的参数还有使用技巧进行详细的剖析，这一篇仅仅是个引导。

</div>

<div>

但是这个系列，对于不感兴趣的同学可能会无聊至极。不过掌握一些技巧还是蛮有趣的。

</div>

<div>

懂原理，才能更好的使用嘛～

</div>

<div>

\

</div>

<div>

其中

</div>

<div>

标准参数：开头直接为参数名、

</div>

<div>

非标准参数： -X 开头

</div>

<div>

高级选项：-XX：开头 后跟+为开启，-为关闭

</div>

<div>

\

</div>

<div>

下面是具体的参数：

</div>

<div>

大家可以对应这个参数列表对应自己看着感兴趣的看看使用，或者检查一下自己了解哪些。

</div>

<div>

标准参数（Standard）：

</div>

<div>

-agentlib:libname

</div>

<div>

-agentpath:pathname

</div>

<div>

-client

</div>

<div>

-Dproperty=value

</div>

<div>

-d32

</div>

<div>

-d64

</div>

<div>

-disableassertions\[:\[packagename\]...\|:classname\]

</div>

<div>

-da\[:\[packagename\]...\|:classname\]

</div>

<div>

-disablesystemassertions

</div>

<div>

-dsa

</div>

<div>

-enableassertions\[:\[packagename\]...\|:classname\]

</div>

<div>

-ea\[:\[packagename\]...\|:classname\]

</div>

<div>

-enablesystemassertions

</div>

<div>

-esa

</div>

<div>

-help

</div>

<div>

-?

</div>

<div>

-jar filename

</div>

<div>

-javaagent:jarpath\[=options\]

</div>

<div>

-jre-restrict-search

</div>

<div>

-no-jre-restrict-search

</div>

<div>

-server

</div>

<div>

-showversion

</div>

<div>

-splash:imgname

</div>

<div>

-verbose:class

</div>

<div>

-verbose:gc

</div>

<div>

-verbose:jni

</div>

<div>

-version

</div>

<div>

-version:release

</div>

<div>

这类标准参数，作用信息非常明确，而且大都有具体的选项。会放在最后介绍。

</div>

<div>

\

</div>

<div>

非标准参数（Non-Standard）：

</div>

<div>

-X

</div>

<div>

-XBatch

</div>

<div>

-Xbootclasspath:path

</div>

<div>

-Xbootclasspath/a:path

</div>

<div>

-Xbootclasspath/p:path

</div>

<div>

-Xcheck:jni

</div>

<div>

-Xcomp

</div>

<div>

-Xdebug

</div>

<div>

-Xdiag

</div>

<div>

-Xfuture

</div>

<div>

-Xint

</div>

<div>

-Xinternalversion

</div>

<div>

-Xloggc:filename

</div>

<div>

-Xmaxjitcodesize=size

</div>

<div>

-Xmixed

</div>

<div>

-Xmnsize

</div>

<div>

-Xmssize

</div>

<div>

-Xmxsize

</div>

<div>

-Xnoclassgc

</div>

<div>

-Xprof

</div>

<div>

-Xrs

</div>

<div>

-Xshare:(mode、auto、on、off、dump)

</div>

<div>

-XshowSettings:(category、all、locale、properties、vm)

</div>

<div>

-Xsssize

</div>

<div>

-Xusealtsigs

</div>

<div>

-Xverify:(mode、remote、all、none)

</div>

<div>

这一块儿会首先说明，因为我们日常用的频次高的大部分是这一片参数

</div>

<div>

\

</div>

<div>

\

</div>

<div>

高级运行时选项（Advanced Runtime Options）：

</div>

<div>

-XX:+CheckEndorsedAndExtDirs

</div>

<div>

-XX:+DisableAttachMechanism

</div>

<div>

-XX:ErrorFile=filename

</div>

<div>

-XX:+FailOverToOldVerifier

</div>

<div>

-XX:+FilghtRecorder

</div>

<div>

-XX:-FilghtRecorder

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

-XX:LargePageSizeInBytes=size

</div>

<div>

-XX:MaxDirectMemorySize=size

</div>

<div>

-XX:NativeMemoryTracking={ mode、off、summary、detail }

</div>

<div>

-XX:ObjectAlignmentInBytes=alignment

</div>

<div>

-XX:OnError=string

</div>

<div>

-XX:OnOutOfMemoryError=string

</div>

<div>

-XX:+PerfDataSaveToFile

</div>

<div>

-XX:+PrintCommandLineFlags

</div>

<div>

-XX:+PrintNMTStatistics

</div>

<div>

-XX:+RelaxAccessControlCheck

</div>

<div>

-XX:+ResourceManagement

</div>

<div>

-XX:ResourceManagementSampleInterval=value (milliseconds)

</div>

<div>

-XX:SharedArchiveFile=path

</div>

<div>

-XX:SharedClassListFile=file_name

</div>

<div>

-XX:+ShowMessageBoxOnError

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

-XX:ThreadStackSize=size

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

-XX:+UnlockCommercialFeatures

</div>

<div>

-XX:+UseAltSigs

</div>

<div>

-XX:+UseAppCDS

</div>

<div>

-XX:-UseBiasedLocking

</div>

<div>

-XX:-UseCompressedOops

</div>

<div>

-XX:+UseHugeTLBFS

</div>

<div>

-XX:+UseLargePages

</div>

<div>

-XX:+UseMembar

</div>

<div>

-XX:+UsePerfData

</div>

<div>

-XX:+UseTransparentHugePages

</div>

<div>

-XX:+AllowUserSignalHandlers

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

JIT相关编译高级选项 Advanced JIT Compiler Options

</div>

<div>

\

</div>

<div>

\

</div>

<div>

-XX:+AggressiveOpts

</div>

<div>

-XX:AllocateInstancePrefetchLines=lines

</div>

<div>

-XX:AllocatePrefetchDistance=size

</div>

<div>

-XX:AllocatePrefetchInstr=instruction

</div>

<div>

-XX:AllocatePrefetchLines=lines

</div>

<div>

-XX:AllocatePrefetchStepSize=size

</div>

<div>

-XX:AllocatePrefetchStyle=style

</div>

<div>

-XX:+BackgroundCompilation

</div>

<div>

-XX:CICompilerCount=threads

</div>

<div>

-XX:CodeCacheMinimumFreeSpace=size

</div>

<div>

-XX:CompileCommand=command,method\[,option(break、compileonly、dontinline、exclude、help、inline、log、option、print、quiet)\]

</div>

<div>

-XX:CompileCommandFile=filename

</div>

<div>

-XX:CompileOnly=methods

</div>

<div>

-XX:CompileThreshold=invocations

</div>

<div>

-XX:+DoEscapeAnalysis

</div>

<div>

-XX:InitialCodeCacheSize=size

</div>

<div>

-XX:+Inline

</div>

<div>

-XX:InlineSmallCode=size

</div>

<div>

-XX:+LogCompilation

</div>

<div>

-XX:MaxInlineSize=size

</div>

<div>

-XX:MaxNodeLimit=nodes

</div>

<div>

-XX:MaxTrivialSize=size

</div>

<div>

-XX:+OptimizeStringConcat

</div>

<div>

-XX:+PrintAssembly

</div>

<div>

-XX:+PrintCompilation

</div>

<div>

-XX:+PrintInlining

</div>

<div>

-XX:ReservedCodeCacheSize=size

</div>

<div>

-XX:RTMAbortRatio=abort_ratio

</div>

<div>

-XX:RTMRetryCount=number_of_retries

</div>

<div>

-XX:-TieredCompilation

</div>

<div>

-XX:+UseAES

</div>

<div>

-XX:+UseAESIntrinsics

</div>

<div>

-XX:+UseCodeCacheFlushing

</div>

<div>

-XX:+UseCondCardMark

</div>

<div>

-XX:+UseRTMDeopt

</div>

<div>

-XX:+UseRTMLocking

</div>

<div>

-XX:+UseSHA

</div>

<div>

-XX:+UseSHA1Intrinsics

</div>

<div>

-XX:+UseSHA256Intrinsics

</div>

<div>

-XX:+UseSHA512Intrinsics

</div>

<div>

-XX:+UseSuperWord

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

适用性高级选项 Advanced Serviceability Options

</div>

<div>

\

</div>

<div>

-XX:+ExtendedDTraceProbes

</div>

<div>

-XX:+HeapDumpOnOutOfMemory

</div>

<div>

-XX:HeapDumpPath=path

</div>

<div>

-XX:LogFile=path

</div>

<div>

-XX:+PrintClassHistogram

</div>

<div>

-XX:+PrintConcurrentLocks

</div>

<div>

-XX:+UnlockDiagnosticVMOptions

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

GC相关高级参数 Advanced Garbage Collection Options

</div>

<div>

\

</div>

<div>

-XX:+AggressiveHeap

</div>

<div>

-XX:+AlwaysPreTouch

</div>

<div>

-XX:+CMSClassUnloadingEnabled

</div>

<div>

-XX:CMSExpAvgFactor=percent

</div>

<div>

-XX:CMSInitiatingOccupancyFraction=percent

</div>

<div>

-XX:+CMSScavengeBeforeRemark

</div>

<div>

-XX:CMSTriggerRatio=percent

</div>

<div>

-XX:ConcGCThreads=threads

</div>

<div>

-XX:+DisableExplicitGC

</div>

<div>

-XX:+ExplicitGCInvokesConcurrent

</div>

<div>

-XX:+ExplicitGCInvokesConcurrentAndUnloadsClasses

</div>

<div>

-XX:G1HeapRegionSize=size

</div>

<div>

-XX:+G1PrintHeapRegions

</div>

<div>

-XX:G1ReservePercent=percent

</div>

<div>

-XX:InitialHeapSize=size

</div>

<div>

-XX:InitialSurvivorRatio=ratio

</div>

<div>

-XX:InitiatingHeapOccupancyPercent=percent

</div>

<div>

-XX:MaxGCPauseMillis=time

</div>

<div>

-XX:MaxHeapSize=size

</div>

<div>

-XX:MaxHeapFreeRatio=percent

</div>

<div>

-XX:MaxMetaspaceSize=size

</div>

<div>

-XX:MaxNewSize=size

</div>

<div>

-XX:MaxTenuringThreshold=threshold

</div>

<div>

-XX:MetaspaceSize=size

</div>

<div>

-XX:MinHeapFreeRatio=percent

</div>

<div>

-XX:NewRatio=ratio

</div>

<div>

-XX:NewSize=size

</div>

<div>

-XX:ParallelGCThreads=threads

</div>

<div>

-XX:+ParallelRefProcEnabled

</div>

<div>

-XX:+PrintAdaptiveSizePolicy

</div>

<div>

-XX:+PrintGC

</div>

<div>

-XX:+PrintGCApplicationConcurrentTime

</div>

<div>

-XX:+PrintGCApplicationStoppedTime

</div>

<div>

-XX:+PrintGCDateStamps

</div>

<div>

-XX:+PrintGCDetails

</div>

<div>

-XX:+PrintGCTaskTimeStamps

</div>

<div>

-XX:+PrintGCTimeStamps

</div>

<div>

-XX:+PrintStringDeduplicationStatistics

</div>

<div>

-XX:+PrintTenuringDistribution

</div>

<div>

-XX:+ScavengeBeforeFullGC

</div>

<div>

-XX:SoftRefLRUPolicyMSPerMB=time

</div>

<div>

-XX:StringDeduplicationAgeThreshold=threshold

</div>

<div>

-XX:SurvivorRatio=ratio

</div>

<div>

-XX:TargetSurvivorRatio=percent

</div>

<div>

-XX:TLABSize=size

</div>

<div>

-XX:+UseAdaptiveSizePolicy

</div>

<div>

-XX:+UseCMSInitiatingOccupancyOnly

</div>

<div>

-XX:+UseConcMarkSweepGC

</div>

<div>

-XX:+UseG1GC

</div>

<div>

-XX:+UseGCOverheadLimit

</div>

<div>

-XX:+UseNUMA

</div>

<div>

-XX:+UseParallelGC

</div>

<div>

-XX:+UseParallelOldGC

</div>

<div>

-XX:+UseParNewGC

</div>

<div>

-XX:+UseSerialGC

</div>

<div>

-XX:+UseSHM

</div>

<div>

-XX:+UseStringDeduplication

</div>

<div>

-XX:+UseTLAB

</div>

<div>

\

</div>

<div>

\

</div>

<div>

不建议使用的参数 Deprecated and Removed Options

</div>

<div>

\

</div>

<div>

-Xincgc

</div>

<div>

-Xrunlibname

</div>

<div>

-XX:CMSIncrementalDutyCycle=percent

</div>

<div>

-XX:CMSIncrementalDutyCycleMin=percent

</div>

<div>

-XX:+CMSIncrementalMode

</div>

<div>

-XX:CMSIncrementalOffset=percent

</div>

<div>

-XX:+CMSIncrementalPacing

</div>

<div>

-XX:CMSIncrementalSafetyFactor=percent

</div>

<div>

-XX:CMSInitiatingPermOccupancyFraction=percent

</div>

<div>

-XX:MaxPermSize=size

</div>

<div>

-XX:PermSize=size

</div>

<div>

-XX:+UseSplitVerifier

</div>

<div>

-XX:+UseStringCache

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

<div>

\

</div>
