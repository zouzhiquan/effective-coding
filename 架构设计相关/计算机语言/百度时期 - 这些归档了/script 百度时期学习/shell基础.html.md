---
altitude: 141.0101928710938
author: zouzq7@163.com
created: "2017-11-26 12:11:21 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19476339396649
longitude: 117.1105588359452
source: desktop.mac
title: shell基础
updated: "2019-05-20 09:07:36 +0000"
---

<div>

\

</div>

<div>

\

</div>

<div>

Linux 内核的作用： 系统内存管理、软件程序管理、硬件管理、文件系统管理

</div>

<div>

\

</div>

<div>

系统内存管理：

</div>

<div>

最主要的是对虚拟内存的管理、当然也会对实际的物理内存进行管理。尤其是对交换空间（磁盘与主存抽象出来的一区域，比如实际使用的内存比主存要大，就是因为抽象的虚拟内存要大，一部分存在于硬盘中，换进换出即可）的管理

</div>

<div>

Linux 中每个进程的内存页是私有的，不能跨进程访问。

</div>

<div>

ipcs 产看内存中的信息。

</div>

<div>

/proc/meminfo :虚拟内存使用情况

</div>

<div>

\

</div>

<div>

\

</div>

<div>

软件程序管理：

</div>

<div>

具有一个叫做初始进程（init process）的进程，作用是启动其他进程。

</div>

<div>

/etc/inittabs 定义了初始化进程

</div>

<div>

其中Linux 软件的运行包含五个级别： 1:单用户模式 3:标准模式 5:图形化模式

</div>

<div>

ps 查看对应进程信息

</div>

<div>

\

</div>

<div>

GNU 应用程序:标准且实用的Unix
工具包，核心工具：处理文本、操作文本、进程管理

</div>

<div>

\

</div>

<div>

shell：基于命令操作符的系统操作模式

</div>

<div>

shell 种类：

</div>

<div>

ash：轻量级shell，与bash shell 完全兼容

</div>

<div>

korn：支持高级编程功能

</div>

<div>

tcsh：吸收c语言元素的shell

</div>

<div>

zsh：很溜，吸收了一堆高级shell 的功能。

</div>

<div>

\

</div>

<div>

------------------------------------------------------------------------

</div>

<div>

\

</div>

<div>

treminfo 数据库：识别终端、控制终端用。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

/etc/passwd 存储对应用户相关信息，格式： 用户名 用户ID 用户组ID 用户全名
用户默认主目录 用户默认shell程序

</div>

<div>

bash 启动时自动加载，/etc/bashrc 文件，其中包含所需的环境变量等

</div>

<div>

然后如果想设置特殊提示符，仅仅需要设置对应变量 PS1

</div>

<div>

\

</div>

<div>

文件系统：

</div>

<div>

/bin :二进制文件目录，存储GNU用户级别的实用程序。

</div>

<div>

/boot:引导文件目录

</div>

<div>

/dev :设备文件集合

</div>

<div>

/home:用户目录

</div>

<div>

/lib :库目录

</div>

<div>

/etc :配置文件

</div>

<div>

/media:媒体文件

</div>

<div>

/mnt:挂载目录

</div>

<div>

/opt:可选目录

</div>

<div>

/root:根主目录

</div>

<div>

/sbin: System bin

</div>

<div>

/tmp:临时目录

</div>

<div>

/usr:用户安装软件空间

</div>

<div>

/var:可变目录，用户经常更改的文件目录

</div>

<div>

\

</div>

<div>

------------------------------------------------------------------------

</div>

<div>

\

</div>

<div>

ls :-l 详细信息，-F 显示类型，-a显示隐藏文件，-R显示目录内容

</div>

<div>

touch:创建文件

</div>

<div>

cp : -p 保持信息一致，-R 递归复制整个目录，

</div>

<div>

\

</div>

<div>

在同一个物理介质的文件之间只能创建一个硬连接，不能在不同挂在点下使用硬链接。

</div>

<div>

硬链接：当最后一个链接文件删除时，这个文件才真正的被删除。

</div>

<div>

\

</div>

<div>

mv：最常用的应该是重命名操作吧

</div>

<div>

\

</div>

<div>

stat：显示完整的文件信息

</div>

<div>

file：确定文件类型及文件相关详细信息

</div>

<div>

top命令与ps 命令类似，但是相对于ps来说top命令是实时

</div>

<div>

\

</div>

<div>

------------------------------------------------------------------------

</div>

<div>

进程监控：

</div>

<div>

ps top 

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.07.25.png) 
 \

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.07.46.png) 
 \

</div>

<div>

\

</div>

<div>

Kill ：杀死进程，按照 pid 。默认 term
条件终止进程，如果进程已经失控，按照 kill -s 制定对应方式停止线程。

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.01.32.png) 
 \

</div>

<div>

\

</div>

<div>

\

</div>

<div>

killall 可通过进程名称停止线程

</div>

<div>

\

</div>

<div>

------------------------------------------------------------------------

</div>

<div>

\

</div>

<div>

磁盘监控：

</div>

<div>

mount  显示挂载介质

</div>

<div>

手动挂载：mount -t vfat /dev/\*\*\*\*

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.13.07.png) 
 \

</div>

<div>

\

</div>

<div>

umount 卸载介质： umount /path

</div>

<div>

\

</div>

<div>

df 查看磁盘使用情况

</div>

<div>

du 查看每个文件磁盘对应的使用情况，参数可以跟path

</div>

<div>

\

</div>

<div>

文件排序

</div>

<div>

sort -n file（按数字排序file）

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.32.45.png) 
 \

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.32.57.png) 
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

------------------------------------------------------------------------

</div>

<div>

文本操作：

</div>

<div>

grep \[options\] pattern \[file\]

</div>

<div>

\

</div>

<div>

options ：-v表示反相匹配，默认可省略

</div>

<div>

pattern：shell 正则

</div>

<div>

\

</div>

<div>

\

</div>

<div>

压缩相关：

</div>

<div>

1、常见工具

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.45.44.png) 
 \

</div>

<div>

zip、gzip、bzip2 比较常用

</div>

<div>

然后注意使用：bzcat 非解压方式查看对应文件内容

</div>

<div>

zip -r 递归压缩

</div>

<div>

\

</div>

<div>

2、标准压缩命令

</div>

<div>

tar 命令：tar function \[options\] object1 object2

</div>

<div>

\

</div>

<div>

当然啦，各项选项是可以组合使用的。

</div>

<div>

function：定义了tar命令的职责

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.53.43.png) 
 \

</div>

<div>

\

</div>

<div>

![](shell%E5%9F%BA%E7%A1%80.resources/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-11-27%20%E4%B8%8B%E5%8D%881.54.09.png) 
 \

</div>

<div>

------------------------------------------------------------------------

</div>

<div>

环境变量相关：

</div>

<div>

\

</div>

<div>

linux 权限

</div>

<div>

drwxr-xr-x     2     root     root

</div>

<div>

第1位为d，表示该文件是一个目录。

</div>

<div>

第2至4位为rwx，表示root用户拥有的权限列表（r：读，w：写，x：执行）

</div>

<div>

第5至7位为r-x，表示root组的成员只有读和执行的权限

</div>

<div>

8至10位r-x，表示不是root的用户及不属于root组的用户，拥有读，和执行的权利

</div>

<div>

这种命令结构，常使用数字表示法。

</div>

<div>

r：4，w：2，x：1

</div>

<div>

例：rwxrwxr-x：421421401:775

</div>

<div>

\

</div>

<div>

chmod :修改用户权限

</div>

<div>

文件权限：     chmod 775 filename

</div>

<div>

目录权限：     chmod 755 /devname/\*

</div>

<div>

存在子目录：     chmod 755 －R /devname/\*

</div>

<div>

chown：更改文件用户所有者

</div>
