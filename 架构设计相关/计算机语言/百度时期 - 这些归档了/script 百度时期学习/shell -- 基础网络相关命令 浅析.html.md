---
author: zouzq7@163.com
created: "2019-05-20 09:22:06 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: shell \-- 基础网络相关命令 浅析
updated: "2019-05-20 09:50:32 +0000"
---

<div>

大家接触到的第一个命令可能是ping，来测试接口通不通，但是网络相关的命令可远不止ping：

</div>

<div>

从ping开始说，ping通常是用来检测网络（TCP/IP）联通性和联通速度的。

</div>

<div>

![](shell%20--%20%E5%9F%BA%E7%A1%80%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4%20%E6%B5%85%E6%9E%90.resources/7537482D-FD1A-479C-9345-4ACD6A9ABB92.png) 
 

</div>

<div>

hostname 通常用来查看自身的主机名，也可以追加一些参数显示相关ip等信息(-d
显示机器所属域名、-f显示完整的主机名&域名、-i显示当前机器的ip地址)

</div>

<div>

![](shell%20--%20%E5%9F%BA%E7%A1%80%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4%20%E6%B5%85%E6%9E%90.resources/4A461466-1912-446A-9CD6-8CC36CCA3522.png) 
 

</div>

<div>

lsof 用于查看监听端口的进程信息(lsof
是一个比较强大的命令，除了端口占用还可以查看正在谁打开的文件，某个进程使用的文件，列出tcp信息、列出udp信息，有兴趣的话可以直接man
看一下)

</div>

<div>

![](shell%20--%20%E5%9F%BA%E7%A1%80%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4%20%E6%B5%85%E6%9E%90.resources/97F32D5A-A270-435E-B9B2-A84D0ABBE56F.png) 
 

</div>

<div>

ifconfig用于查看忘了的一些常见配置信息,类似的命令还有iwconfig，下面是我电脑一点基础的信息

</div>

<div>

![](shell%20--%20%E5%9F%BA%E7%A1%80%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4%20%E6%B5%85%E6%9E%90.resources/7544BCD5-A3CF-483E-8912-5BB002D7F215.png) 
 

</div>

<div>

nslookup用于在有ip地址时用来显示主机名:

</div>

<div>

![](shell%20--%20%E5%9F%BA%E7%A1%80%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4%20%E6%B5%85%E6%9E%90.resources/46EBBF8C-677B-4DBC-85FE-131BCE0AE833.png) 
 

</div>

<div>

netstat感觉时最常用的一个命令了，用于发现主机连接最通用的命令，先看一下参数：

</div>

<div>

--a :
显示所有的有效连接信息，包括已建立的连接（ESTABLISHED）和监听的连接请求（LISTENING）。

</div>

<div>

--ac :
以连续的形式显示所有的有效连接信息，每隔1秒刷新1次显示，直到用户中断显示。

</div>

<div>

-i  : 显示系统中所有网络接口信息，包括物理网卡、网卡别名和本地回环网卡。

</div>

<div>

--n : 显示系统中所有已建立的连接。

</div>

<div>

--r : 显示路由表。

</div>

<div>

--ta : 显示有效的TCP连接。

</div>

<div>

--ua : 显示有效的UDP连接。

</div>

<div>

--s : 显示各个协议的统计信息。

</div>

<div>

netstat -nap \| grep port 将会显示使用该端口的应用程序的进程 id 

</div>

<div>

netstat -a or netstat --all 将会显示包括 TCP 和 UDP 的所有连接

</div>

<div>

netstat --tcp or netstat --t 将会显示 TCP 连接

</div>

<div>

netstat --udp or netstat --u 将会显示 UDP 连接

</div>

<div>

netstat -g 将会显示该主机订阅的所有多播网络。

</div>

<div>

例如：

</div>

<div>

![](shell%20--%20%E5%9F%BA%E7%A1%80%E7%BD%91%E7%BB%9C%E7%9B%B8%E5%85%B3%E5%91%BD%E4%BB%A4%20%E6%B5%85%E6%9E%90.resources/2ADE2809-39CB-4DB6-825B-57DBAEB90908.png) 
 

</div>

<div>

\

</div>

<div>

其他命令平时可能记不住，用到的时候看下api就好了。

</div>
