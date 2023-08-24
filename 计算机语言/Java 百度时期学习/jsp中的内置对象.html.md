---
altitude: 158.1359558105469
author: zouzq7@163.com
created: "2017-04-02 10:05:06 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19541661588755
longitude: 117.1116560273827
source: desktop.mac
title: jsp中的内置对象
updated: "2017-04-02 10:13:48 +0000"
---

:::  
<div>

先说一下这些常见的内置对象。然后说一下什么是上下文，其实我一直搞不清楚，为什么翻译成上下文。

</div>

<div>

\

</div>
:::

:::  
:::

<div>

[1.request对象] 

</div>

<div>

[     客户端的请求信息被封装在request对象中，通过它才能了解到客户的需求，然后做出响应。它是HttpServletRequest类的实例。] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[2.response对象] 

</div>

</div>

<div>

[     response对象包含了响应客户请求的有关信息，但在JSP中很少直接用到它。它是HttpServletResponse类的实例。] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[3.session对象] 

</div>

</div>

<div>

[     session对象指的是客户端与服务器的一次会话，从客户连到服务器的一个WebApplication开始，直到客户端与服务器断开连接为] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[4.out对象] 

</div>

</div>

<div>

[     out对象是JspWriter类的实例,是向客户端输出内容常用的对象] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[5.page对象] 

</div>

</div>

<div>

[     page对象就是指向当前JSP页面本身，有点象类中的this指针，它是java.lang.Object类的实例] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[6.application对象] 

</div>

</div>

<div>

[     application对象实现了用户间数据的共享，可存放全局变量。它开始于服务器的启动，直到服务器的关闭，在此期间，此对象将一直存在；这样在用户的前后连接或不同用户之间的连接中，可以对此对象的同一属性进行操作；在任何地方对此对象属性的操作，都将影响到其他用户对此的访问。服务器的启动和关闭决定了application对象的生命。它是ServletContext类的实例。] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[7.exception对象] 

</div>

</div>

<div>

[   exception对象是一个例外对象，当一个页面在运行过程中发生了例外，就产生这个对象。如果一个JSP页面要应用此对象，就必须把isErrorPage设为true，否则无法编译。他实际上是java.lang.Throwable的对象] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[8.pageContext对象] 

</div>

</div>

<div>

[pageContext对象提供了对JSP页面内所有的对象及名字空间的访问，也就是说他可以访问到本页所在的SESSION，也可以取本页面所在的application的某一属性值，他相当于页面中所有功能的集大成者，它的本
类名也叫pageContext。] 

</div>

<div>

[[\
] 

</div>

<div>

<div>

[9.config对象] 

</div>

</div>

<div>

[config对象是在一个Servlet初始化时，JSP引擎向它传递信息用的，此信息包括Servlet初始化时所要用到的参数（通过属性名和属性值构成）以及服务器的有关信息（通过传递一个ServletContext对象）] 

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>

<div>

上下文，应该就是一个存储初始化信息的容器（全局）吧。

</div>

<div>

先获取系统上下文，然后从上下文中取出信息，进行相应的初始化。

</div>

<div>

哈哈，我也没有上下文的准确理解。。。。。。。。。。。。。。。。。。。。。

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>
