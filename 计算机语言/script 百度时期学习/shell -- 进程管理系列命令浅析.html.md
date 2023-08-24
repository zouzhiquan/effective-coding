---
author: zouzq7@163.com
created: "2019-05-20 09:05:35 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: shell \-- 进程管理系列命令浅析
updated: "2019-05-20 09:06:39 +0000"
---

<div>

linux
常用的命令工具非常多，除了cd、ls、mkdir、cp、mv这些非常常用和简单的命令，这里对于开发和排查问题过程中进程相关常用的几个命令进行介绍

</div>

<div>

ps 是查看进程状态的一个命令，类似的还有top等，具体来说：

</div>

<div>

查看类命令：

</div>

<div>

ps,pstree,pidof,pgrep\\pkill,top,htop,glances,dstat,vmstat,pmap

</div>

<div>

管理类命令：

</div>

<div>

bg,fg,jobs,kill,killall,nohup,nice,renice

</div>

<div>

先看ps，常用参数：

</div>

<div>

-A ：所有的进程均显示出来，与 -e 具有同样的效用；

</div>

<div>

-a ： 显示现行终端机下的所有进程，包括其他用户的进程；

</div>

<div>

-u ：以用户为主的进程状态 ；

</div>

<div>

x ：通常与 a 这个参数一起使用，可列出较完整信息。

</div>

<div>

输出格式：

</div>

<div>

l ：较长、较详细的将该PID 的的信息列出；

</div>

<div>

j ：工作的格式 (jobs format)

</div>

<div>

-f ：做一个更为完整的输出。

</div>

<div>

下面来看一下demo 和示例

</div>

<div>

![](shell%20--%20%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86%E7%B3%BB%E5%88%97%E5%91%BD%E4%BB%A4%E6%B5%85%E6%9E%90.resources/ACD27F05-9CB8-4726-B792-494785A43BA8.png) 
 

</div>

<div>

然后看一下各个详细信息的示例：

</div>

<div>

F 代表这个程序的旗标 (flag)， 4 代表使用者为 superuser；

</div>

<div>

S 代表这个程序的状态 (STAT)；

</div>

<div>

UID 代表执行者身份

</div>

<div>

PID 进程的ID号！

</div>

<div>

PPID 父进程的ID；

</div>

<div>

C CPU使用的资源百分比

</div>

<div>

PRI指进程的执行优先权(Priority的简写)，其值越小越早被执行；

</div>

<div>

NI 这个进程的nice值，其表示进程可被执行的优先级的修正数值。

</div>

<div>

ADDR 这个是内核函数，指出该程序在内存的那个部分。如果是个执行
的程序，一般就是『 - 』

</div>

<div>

SZ 使用掉的内存大小；

</div>

<div>

WCHAN 目前这个程序是否正在运作当中，若为 - 表示正在运作；

</div>

<div>

TTY 登入者的终端机位置；

</div>

<div>

TIME 使用掉的 CPU 时间。

</div>

<div>

CMD 所下达的指令名称

</div>

<div>

STAT：

</div>

<div>

R    运行状态；running or runnable (on run queue)

</div>

<div>

S    可中断睡眠态； interruptible sleep (waiting for an event to
complete)

</div>

<div>

D    不可终端睡眠态； uninterruptible sleep (usually IO)

</div>

<div>

t    在跟踪期间被停止；stopped by debugger during the tracing

</div>

<div>

T    停止进程控制信号；stopped by job control signal

</div>

<div>

X    死进程； dead (should never be seen)

</div>

<div>

Z    僵尸进程； defunct (\"zombie\") process, terminated but not reaped
by its parent

</div>

<div>

\<    高优先级； high-priority (not nice to other users)

</div>

<div>

N    低优先级； low-priority (nice to other users)

</div>

<div>

L    页面是否锁进内存中；has pages locked into memory (for real-time and
custom IO)

</div>

<div>

s    会话领导； is a session leader

</div>

<div>

l    多线程；is multi-threaded (using CLONE_THREAD, like NPTL pthreads
do)

</div>

<div>

+    前进程组； is in the foreground process group

</div>

<div>

列出我们想要的进程之后，通常下一步就是进行操作了，最常用的就是kill了

</div>

<div>

kill命令的格式：kill \[-s signal\|-p\] \[\--\] pid\...

</div>

<div>

当在一个linux操作系统上想执行kill
时，需要先看一下当前系统支持哪些信号量（kill -l），比如说我的电脑：

</div>

<div>

![](shell%20--%20%E8%BF%9B%E7%A8%8B%E7%AE%A1%E7%90%86%E7%B3%BB%E5%88%97%E5%91%BD%E4%BB%A4%E6%B5%85%E6%9E%90.resources/53D2346F-9EDF-4C6B-9589-0058C035F2B2.png) 
 

</div>

<div>

常用的信号：

</div>

<div>

  1) SIGHUP: 无需关闭对应进程而直接让其重新读取其自身的配置文件；

</div>

<div>

  2) SIGINT：终止运行于前台的进程，使用Ctrl+c组合键，可以发送此信号；

</div>

<div>

  9) SIGKILL:
没有任何预兆终止正在运行的进程，进程所有占用的资源都不会被立即回收；

</div>

<div>

  15) SIGTERM：kill命令默认发送的信号；终止进程的运行，

</div>

<div>

  18) SIGCONT：让转入后台的进程在后台继续运行；

</div>

<div>

  19) SIGSTOP：让转入后台的进程在后台停止运行；

</div>

<div>

然后就可以使用 kill -方式 pid 来杀死进程了。

</div>

<div>

\

</div>
