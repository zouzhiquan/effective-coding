---
author: zouzq7@163.com
created: "2018-07-12 02:17:53 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java 调用外部程序
updated: "2018-07-12 02:20:28 +0000"
---

<div>

Java 执行外部脚本相关技术点：

</div>

<div>

\

</div>

<div>

java 调用外部程序：

</div>

<div>

Runtime、Process、ProcessBuilder

</div>

<div>

\

</div>

<div>

\

</div>

<div>

启动：可以通过传递shell命令的形式驱动脚本的执行

</div>

<div>

查看：可以通过传递shell命令的形式查看当前shell脚本的进程状态（main指向的子进程）

</div>

<div>

终止：强制传递kill该子进程或通过调用process的函数终止外部的shell进程

</div>

<div>

\

</div>

<div>

\

</div>

<div>

Runtime（使用方式）：

</div>

<div>

1、exec方法接收一个命令然后执行，通过该方法可以执行和cmd中用法一样命令。比如，Runtime.getRuntime().exec("ls")，就和cmd中执行ls效果一样了。

</div>

<div>

2、freeMemory()可以查看当前虚拟机内存中空闲内存还有多少。

</div>

<div>

3、totalMemory()可以查看当前虚拟机使用的总内存大小。

</div>

<div>

4、maxMemory()可以查看JVM的最终可以使用的最大内存是多少。

</div>

<div>

5、availableProcessors()可以查看本机有多少处理器，即本机处理器是多少核。

</div>

<div>

6、exit(int)方法可以退出当前Java程序的运行，System.exit(int)方法就是调用了Runtime.exit(int)方法来退出运行的。

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

process（依赖于Runtime）：

</div>

<div>

1、waitFor()是让当前主进程等待这个process指向的子进程执行完成。

</div>

<div>

2、exitValue()可以查看process指向的子进程执行完的退出值，0代表是正常运行结束。

</div>

<div>

3、destroy()和destroyForcibly()可以终止process子进程的运行，后者是强制终止，前者与平台终止进程的具体实现有关。

</div>

<div>

但通过实验发现，process中存在bug，destroy杀死进程时，不能终止其子孙进程的执行。

</div>

<div>

\

</div>

<div>

ProcessBuilder

</div>

<div>

1、创建ProcessBuilder不需要通过Runtime，而Runtime.getRimtime().exec(string)正是调用了ProcessBuilder的构造方法来创建子进程并执行的。

</div>

<div>

2、ProcessBuilder的构造方法接收一个命令参数的数组形式，其中，第一个元素代表要执行的系统命令，后面的元素代表要传给该命令的参数。

</div>

<div>

3、调用.start()方法运行之后，就可以获得该子进程的Process引用了，然后就可以调用Process的方法进行处理。

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

传参问题：当传递的参数字符串中包含空格时，上边的方法会把参数截断，默认为参数只到空格处。

</div>

<div>

解决：将shell 命令或脚本 和参数 放在一个
数组中，然后将数组传入exec()方法中。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

权限问题：存在脚本无权限执行的问题

</div>

<div>

解决：ProcessBuilder builder = new ProcessBuilder(\"/bin/chmod\",
\"755\",scriptPath);

</div>

<div>

\

</div>

<div>

注意：

</div>

<div>

process创建的子进程没有自己的控制台或终端，其所有的io操作都是通过（输入流、输出流、错误流）重定向到父进程中

</div>

<div>

\

</div>

<div>

\

</div>

<div>

更加合适的选择：

</div>

<div>

Java ProcessBuilder，但需要注意Error信息的收集

</div>

<div>

接口信息：

</div>

<div>

abstract public OutputStream getOutputStream();

</div>

<div>

\

</div>

<div>

abstract public InputStream getInputStream();

</div>

<div>

\

</div>

<div>

abstract public InputStream getErrorStream();

</div>

<div>

\

</div>

<div>

abstract public int waitFor() throws InterruptedException;

</div>

<div>

\

</div>

<div>

abstract public int exitValue();

</div>

<div>

\

</div>

<div>

abstract public void destroy();

</div>

<div>

\

</div>

<div>

远程调用：通过构造连接器通过ssh连接目标机器，然后进行后续过程

</div>

<div>

远程调用详细：

</div>

<div>

Ganymed SSH-2 for
Java是用纯Java实现SSH-2协议的一个包。可以利用它直接在Java程序中连接SSH服务器。Ganymed
SSH-2支持SSH对话(远程命令执行和shell访问),本地和远程端口转发，本地数据流转发，X11转发和SCP。这些都没有依赖任何JCE
provider，而且所有这些都包含加密的功能。

</div>

<div>

\

</div>

<div>

<https://blog.csdn.net/c315838651/article/details/72085739>

</div>

<div>

<https://blog.csdn.net/luckyion/article/details/14002743>

</div>

<div>

<https://blog.csdn.net/x380481791/article/details/77089739>

</div>

<div>

<https://blog.csdn.net/u012373815/article/details/78771689>

</div>

<div>

<https://blog.csdn.net/jackydai987/article/details/38059325>

</div>
