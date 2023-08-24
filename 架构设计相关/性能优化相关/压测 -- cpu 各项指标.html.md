---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-10-10 02:50:35 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: 压测 \-- cpu 各项指标
updated: "2019-10-10 03:28:22 +0000"
---

:::  
### cpu 指标  

压测过程中很重要的一点是观察cpu的各项指标，比如说cpu
idel、user、cpu.load等，这些指标也间接反映了一个系统的抗压能力（容量）。\
在linux内核中，每个进程都会被分配一个固定的时间片，默认是10ms，在这10ms中，该进程享有cpu的使用权，如果该进程用完了10ms,或者有其他优先级高的进程发出请求，系统会触发一个中断，内核重新接管cpu，内核分配cpu给其他进程。\
cpu的使用时主要分为用户态、内核态，对于使用率来说主要分为用户态、系统态、空闲态。\
![](%E5%8E%8B%E6%B5%8B%20--%20cpu%20%E5%90%84%E9%A1%B9%E6%8C%87%E6%A0%87.resources/E9F6AAD6-A744-4381-9B9D-F5CDFA670905.png) 
 \
***user***\
user
选项表示cpu一种花了占比多少的时间运营在用户态空间，我们查用的web程序、shell脚本、数据库应用（mysql、redis）等都是工作在用户态的。\
***Nice***\
Nice表示用户空间内进程的cpu调度优先级，范围是\[-20,19\]\
***System***\
system选项表示cpu花了多少比例的时间在内核空间运行，典型的内核态操作主要是分配内存、IO操作（很多时候太多io操作时System的值会很高）、fork进程等。\
***Wait***\
很多时候cpu发起io操作后，因为速度上的差异，需要等待数据载入内存，这段时间cpu通常无事可做，这部分等待时间由wait参数来衡量。\
***idel***\
idel是指空闲时间的占比，user + system + idel
大致等于cpu的总时间，也就是100%，idel是基于/proc/stat计算出来的。\
idel是压测时最常用指标，通常来说我们cpu
idel压到50%基本就是该系统的极限了。\
***load***\
cpu.load指的是系统平均负载，定义是在特定时间间隔内运行队列中的平均进程数。没有在等待I/O操作的结果、没有主动进入等待状态、没有被停止则认为处于运行队列中。\
load指标是通过/proc/loadavg得到的.\
![](%E5%8E%8B%E6%B5%8B%20--%20cpu%20%E5%90%84%E9%A1%B9%E6%8C%87%E6%A0%87.resources/1ABA98C3-B925-4A10-9DFA-6CFC84FCF3F0.png) 
 

### cpu配置  

cpu的配置信息存放于/proc/cpuinfo中，可以通过cat /proc/cpuinfo查看：\
![](%E5%8E%8B%E6%B5%8B%20--%20cpu%20%E5%90%84%E9%A1%B9%E6%8C%87%E6%A0%87.resources/2EBC85C6-C4DB-43F6-AF37-BA4BD07F87CF.png) 
 \
processor指的是cpu，有几个就代表有几个cpu，cores代表的是cpu核心线程数，还有其他几项信息，意思都很明了了。\
物理CPU个数： cat /proc/cpuinfo \|grep \"physical id\"\|sort \|uniq\|wc
-l\
每个CPU物理核数： cat /proc/cpuinfo \|grep \"cpu cores\"\|uniq\
每个CPU逻辑核数： cat /proc/cpuinfo \|grep \"siblings\"\|uniq\
总CPU逻辑核数： cat /proc/cpuinfo \|grep -c \"processor\"
:::

 
