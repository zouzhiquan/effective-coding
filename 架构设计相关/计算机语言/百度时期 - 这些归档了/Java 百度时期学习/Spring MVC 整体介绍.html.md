---
author: zouzq7@163.com
created: "2018-08-31 02:47:38 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Spring MVC 整体介绍
updated: "2018-09-04 01:39:18 +0000"
---

<div>

Spring MVC 是以servlet
为核心进行实现的，统一入口进入拿到请求后，做一次路由，找到对应的controller及对应的RequestMapping，完成逻辑的后，返回对应的视图或者数据元，如果是顺道完成视图的解析，返回对应html，完成响应。

</div>

<div>

\

</div>

<div>

开发步骤：

</div>

<div>

1、建立Spring 工程，引入对应Spring MVC 所需要的Jar。

</div>

<div>

2、创建web.xml
文件，指定dispacherServlet，然后指定拦截的请求规则，比如说
\*.do，再配置对应的listener，还有spring 依赖的bean的配置文件

</div>

<div>

3、创建springMVC-servlet.xml
类似于这样的一个配置文件，设置启用注解，设置对应扫描的包，然后指定页面存在的位置，指定前缀后缀什么

</div>

<div>

4、在对应目录下创建一个controller
类，并指定对应注解，创建对应RequestMapping 注解的函数，设置URI。

</div>

<div>

5、编写对应逻辑。

</div>

<div>

6、部署&运行

</div>

<div>

\

</div>

<div>

内部处理流程：

</div>

<div>

1、dispacher Servlet 拦截到外部所有的请求

</div>

<div>

2、根据HandlerMapping找到对应的Handler，并将一堆拦截器和Handler封装至HandlerExecutionChain

</div>

<div>

3、带着Handler 找到对应的HandlerAdapter，完成对应的逻辑处理。

</div>

<div>

4、带着具体的Model&View返回

</div>

<div>

5、首先通过ViewResolver对于视图进行解析，并完成
对应的视图渲染，反馈给Dispacher Servlet。

</div>

<div>

6、最后dispacher Servlet 完成响应

</div>

<div>

![](Spring%20MVC%20%E6%95%B4%E4%BD%93%E4%BB%8B%E7%BB%8D.resources/D8576E67-780E-4293-BF85-03641E0D00E9.png) 
 \

</div>

<div>

这就是Spring MVC 中的一个简要流程。

</div>

<div>

\

</div>

<div>

这里的Handler
是需要我们手动实现逻辑的，也就具体的controller、然后ModleAndView
也是我们开发中所接触的ModelAndView，具体的渲染和解析的过程其实就是将对应视图变为前端可识别HTML
文档的形式，过程中将对应的对象信息解析进去就OK了。

</div>

<div>

具体的看后续操作～

</div>

<div>

这里介绍的主要是核心的分发器dispacherServlet，在说之前先来看一看常规的servlet。

</div>

<div>

[public interface ] Servlet {

</div>

<div>

    [void ] init(ServletConfig var1)
[throws
] 

</div>

<div>

\

</div>

<div>

[    ] ServletConfig
getServletConfig()[;] 

</div>

<div>

\

</div>

<div>

[    void ] service(ServletRequest
var1[, ] ServletResponse var2) [throws
] ServletException[,
] 

</div>

<div>

\

</div>

<div>

[    ] String
getServletInfo()[;] 

</div>

<div>

\

</div>

<div>

[    void
] 

</div>

<div>

}

</div>

<div>

sevlet 是Java 在Java
web开发中定义的一个规范，任何一个可以处理外部用户请求的应用组件都需要实现这个接口（即作为一种servlet
存在）。

</div>

<div>

[public abstract class
] GenericServlet [implements
] Servlet[,
] ServletConfig[,
] Serializable {

</div>

<div>

这是GenericServlet，一种servlet的通用实现，提供了servlet应该有的基础功能。

</div>

<div>

我们常用的是HTTP
servlet，一种基于HTTP协议对于GenericServlet实现的Servlet，能够对于外部HTTP请求进行对应的处理并且完成对应的响应。并且针对各种HTTP
请求原语实现了对应的处理方法（具体来说是要求使用者自己必须实现）

</div>

<div>

DispacherServlet 就是一种HTTP servlet
，它实现了对于所有符合规则的的HTTP请求进行拦截，并对具有handler的请求完成具体的逻辑。

</div>

<div>

\

</div>

<div>

Spring MVC 中 DispacherServlet的核心初始化函数是这一个：

</div>

<div>

[protected void
] initStrategies(ApplicationContext
context) {

</div>

<div>

   
[this] 

</div>

<div>

[   
this] 

</div>

<div>

[   
this] 

</div>

<div>

[   
this] 

</div>

<div>

[   
this] 

</div>

<div>

[    [//初始化处理器异常解析器，这是个集合，默认实现是AnnotationMethodHandlerExceptionResolver,ResponseStatusExceptionResolver和DefaultHandlerExceptionResolver] 

</div>

<div>

[  
 this] 

</div>

<div>

[   
this] 

</div>

<div>

[   
this] 

</div>

<div>

[   
this] 

</div>

<div>

}

</div>

<div>

通过ApplicationContext完成对应初始化，对应的调用关系可以搜一下对应DispacherServlet
然后找到对应函数 alt + F7 查看就OK了

</div>

<div>

初始化关系大体过程是这样的，到这里大体能清除整个的层级关系就好：

</div>

<div>

1、首先初始化GenericServlet

</div>

<div>

2、HttpServletBean的初始化

</div>

<div>

3、FrameworkServlet 初始化

</div>

<div>

4、完成最终DispacherServlet 初始化

</div>

<div>

Spring MVC
最核心的差不多就这么多东西，更细节的东西需要亲自动手去翻看源码～

</div>

<div>

\

</div>
