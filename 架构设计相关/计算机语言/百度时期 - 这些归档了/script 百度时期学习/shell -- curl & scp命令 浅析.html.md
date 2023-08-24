---
author: zouzq7@163.com
created: "2019-05-20 09:50:31 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: shell \-- curl & scp命令 浅析
updated: "2019-05-20 10:04:52 +0000"
---

<div>

这一篇说一下常用的传输工具，curl & scp

</div>

<div>

curl
命令在开发中的使用频次实在是太高了，所以单独拿出来介绍一下，curl是一种基于url的综合传输工具：

</div>

<div>

语法：curl \[option\] \[url\]

</div>

<div>

常用参数：（参数实在有点多，在开发的时候通常用-F、-d、-G、-H
等这些参数就能满足开发测试需要了）

</div>

<div>

-a/\--append 上传文件时，附加到目标文件

</div>

<div>

-A/\--user-agent \<string\>  设置用户代理发送给服务器

</div>

<div>

\- anyauth   可以使用"任何"身份验证方法

</div>

<div>

-b/\--cookie \<name=string/file\> cookie字符串或文件读取位置

</div>

<div>

\- basic 使用HTTP基本验证

</div>

<div>

-B/\--use-ascii 使用ASCII /文本传输

</div>

<div>

-c/\--cookie-jar \<file\> 操作结束后把cookie写入到这个文件中

</div>

<div>

-C/\--continue-at \<offset\>  断点续转

</div>

<div>

-d/\--data \<data\>   HTTP POST方式传送数据

</div>

<div>

\--data-ascii \<data\>  以ascii的方式post数据

</div>

<div>

\--data-binary \<data\> 以二进制的方式post数据

</div>

<div>

\--negotiate     使用HTTP身份验证

</div>

<div>

\--digest        使用数字身份验证

</div>

<div>

\--disable-eprt  禁止使用EPRT或LPRT

</div>

<div>

\--disable-epsv  禁止使用EPSV

</div>

<div>

-D/\--dump-header \<file\> 把header信息写入到该文件中

</div>

<div>

\--egd-file \<file\> 为随机数据(SSL)设置EGD socket路径

</div>

<div>

\--tcp-nodelay   使用TCP_NODELAY选项

</div>

<div>

-e/\--referer 来源网址

</div>

<div>

-f/\--fail          连接失败时不显示http错误

</div>

<div>

\--ftp-create-dirs 如果远程目录不存在，创建远程目录

</div>

<div>

\--ftp-method \[multicwd/nocwd/singlecwd\] 控制CWD的使用

</div>

<div>

\--ftp-pasv      使用 PASV/EPSV 代替端口

</div>

<div>

\--ftp-skip-pasv-ip 使用PASV的时候,忽略该IP地址

</div>

<div>

\--ftp-ssl       尝试用 SSL/TLS 来进行ftp数据传输

</div>

<div>

\--ftp-ssl-reqd  要求用 SSL/TLS 来进行ftp数据传输

</div>

<div>

-F/\--form \<name=content\> 模拟http表单提交数据

</div>

<div>

-form-string \<name=string\> 模拟http表单提交数据

</div>

<div>

-g/\--globoff 禁用网址序列和范围使用{}和\[\]

</div>

<div>

-G/\--get 以get的方式来发送数据

</div>

<div>

-h/\--help 帮助

</div>

<div>

-H/\--header \<line\>自定义头信息传递给服务器

</div>

<div>

\--ignore-content-length  忽略的HTTP头信息的长度

</div>

<div>

-i/\--include 输出时包括protocol头信息

</div>

<div>

-I/\--head  只显示文档信息

</div>

<div>

从文件中读取-j/\--junk-session-cookies忽略会话Cookie

</div>

<div>

\- 界面\<interface\>指定网络接口/地址使用

</div>

<div>

\- krb4 \<级别\>启用与指定的安全级别krb4

</div>

<div>

-j/\--junk-session-cookies 读取文件进忽略session cookie

</div>

<div>

\--interface \<interface\> 使用指定网络接口/地址

</div>

<div>

\--krb4 \<level\>  使用指定安全级别的krb4

</div>

<div>

-k/\--insecure 允许不使用证书到SSL站点

</div>

<div>

-K/\--config  指定的配置文件读取

</div>

<div>

-l/\--list-only 列出ftp目录下的文件名称

</div>

<div>

\--limit-rate \<rate\> 设置传输速度

</div>

<div>

-o/\--output 把输出写到该文件中

</div>

<div>

-O/\--remote-name 把输出写到该文件中，保留远程文件的文件名

</div>

<div>

-p/\--proxytunnel   使用HTTP代理

</div>

<div>

-S/\--show-error   显示错误

</div>

<div>

\--socks4 \<host\[:port\]\> 用socks4代理给定主机和端口

</div>

<div>

\--socks5 \<host\[:port\]\> 用socks5代理给定主机和端口

</div>

<div>

\--stderr \<file\>

</div>

<div>

-T/\--upload-file \<file\> 上传文件

</div>

<div>

\--url \<URL\>     Spet URL to work with

</div>

<div>

-y/\--speed-time 放弃限速所要的时间。默认为30

</div>

<div>

-Y/\--speed-limit 停止传输速度的限制，速度时间\'秒

</div>

<div>

-z/\--time-cond  传送时间设置

</div>

<div>

-0/\--http1.0  使用HTTP 1.0

</div>

<div>

-4/\--ipv4   使用IP4

</div>

<div>

-6/\--ipv6   使用IP6

</div>

<div>

\

</div>

<div>

SCP（secure copy）是一种基于ssh的cp命令，相对于cp
就是加了一层网络传输而已

</div>

<div>

常用参数

</div>

<div>

-1  强制scp命令使用协议ssh1  

</div>

<div>

-2  强制scp命令使用协议ssh2

</div>

<div>

-4  强制scp命令只使用IPv4寻址  

</div>

<div>

-6  强制scp命令只使用IPv6寻址  

</div>

<div>

-B  使用批处理模式（传输过程中不询问传输口令或短语）  

</div>

<div>

-C  允许压缩。（将-C标志传递给ssh，从而打开压缩功能）  

</div>

<div>

-p 保留原文件的修改时间，访问时间和访问权限。  

</div>

<div>

-q  不显示传输进度条。  

</div>

<div>

-r  递归复制整个目录。  

</div>

<div>

-v
详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。   

</div>

<div>

-c cipher  以cipher将数据传输进行加密，这个选项将直接传递给ssh。   

</div>

<div>

-F ssh_config  指定一个替代的ssh配置文件，此参数直接传递给ssh。  

</div>

<div>

-i
identity_file  从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。    

</div>

<div>

-l limit  限定用户所能使用的带宽，以Kbit/s为单位。     

</div>

<div>

-o ssh_option  如果习惯于使用ssh_config(5)中的参数传递方式，   

</div>

<div>

-P port  注意是大写的P, port是指定数据传输用到的端口号   

</div>

<div>

-S program  指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项

</div>

<div>

demo：

</div>

<div>

scp zouzhiquan zouzhiquan@127.0.0.1:/Users/zouzhiquan/workspace

</div>
