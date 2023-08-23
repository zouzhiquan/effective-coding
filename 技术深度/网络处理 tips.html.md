---
author: zouzq7@163.com
created: "2022-06-29 04:04:29 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: 网络处理 tips
updated: "2022-06-29 04:04:47 +0000"
---

<div>

tomcat NIO（水平触发）处理链接并发
内部业务逻辑处理（servlet规范仍然是阻塞模式）

</div>

<div>

netty NIO（边缘触发）
整体请求处理逻辑并发（充分发挥NIO也就是epoll的能力）

</div>

<div>

java-Grpc netty（边缘触发的epoll） + http 2.0 + 无需业务阻塞模式

</div>

<div>

nginx 边缘触发的epoll 无需处理业务

</div>

<div>

\

</div>

<div>

\

</div>

<div>

netty 本质上属于对Java NIO 一种边缘触发的补充，tomcat
性能差本质是内部处理模式阻塞的原因，Grpc
之所以快，得益于很多方面（协议、网络处理、内部处理模型）

</div>

<div>

Go快的原因，链接处理上协程代替线程，业务处理模式非阻塞，同样是IO模型，也快

</div>
