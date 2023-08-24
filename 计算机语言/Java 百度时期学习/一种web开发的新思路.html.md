---
altitude: 56.67254638671875
author: zouzq7@163.com
created: "2017-09-29 06:15:04 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03839074445712
longitude: 116.4109467591837
source: desktop.mac
title: 一种web开发的新思路
updated: "2017-09-29 06:15:30 +0000"
---

<div>

<div>

\

</div>

<div>

后台分为：数据、生成动作

</div>

<div>

前段分为：数据、解析动作

</div>

<div>

\

</div>

<div>

通过一个动作指令，得到对应的数据结果。

</div>

<div>

\

</div>

<div>

需要完成两个方面：0=qw

</div>

<div>

\

</div>

<div>

数据结构优化，新定义结构

</div>

<div>

解析动作，产生对应动作组合（反射&自组合调用）

</div>

<div>

\

</div>

<div>

过程：

</div>

<div>

前端发起一个请求，后台对应于请求的规则及数据进行解析，完成对应的数据操作动作。反馈前端具体的Data
及相应的解析方式，前端获取进行解析。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

框架要做的事：

</div>

<div>

对于元数据的管理及掌控。

</div>

<div>

对于动作，不再是一个个具体的响应请求（程序员不需要去编写这些复杂的业务逻辑）。而是框架对于这些请求进行一些基层函数的动态组合调用。（各项设计模式封装及管理基础操作）

</div>

<div>

程序员需要 梳理的是业务逻辑，而非代码逻辑。

</div>

<div>

\

</div>

<div>

具体：

</div>

<div>

组合规则，动态调用及服务发现方式。

</div>

<div>

底层操作的封装及管理（足够精炼及简化、还有性能）。

</div>

<div>

元数据的管理。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

程序员写的是具体动作，后台解析到的是序号，不同的操作对应相应的序号，根据序号调用。（字典或map形式）

</div>

<div>

如果不使用反射，如何来进行规则调用（可选方式有：反射、复杂if-else 或
switch）

</div>

<div>

\

</div>

<div>

面向这么几种服务

</div>

<div>

\

</div>

<div>

RPC、注册中心 这些东西都是必须要有的

</div>

<div>

\

</div>

<div>

简单web 服务：添加界面解析

</div>

<div>

数据管理

</div>

<div>

数据计算

</div>

<div>

后台服务

</div>

</div>

<div>

\

</div>
