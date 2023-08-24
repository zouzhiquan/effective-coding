---
author: zouzq7@163.com
created: "2018-08-30 10:30:57 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Spring MVC 浅谈
updated: "2018-08-31 02:47:37 +0000"
---

<div>

MVC这个词儿，最早的定义应该是作为一种软件架构设计模式出现在软工里面的，即使用model、view、controller
来设计及定义web软件的。

</div>

<div>

Spring MVC 是指Java 当前阶段最大的开源项目Spring 对于MVC
做出了具体的实现，叫做Spring MVC，作为Spring
的一个子集存在。顺道提一下Spring，现阶段的Spring
已经不再是当年那个Ioc、AOP、MVC的简单的"小"web框架了。看一下Spring
官网的一个图： 又差不多25个应用。覆盖的应用面已经达到了Java
服务器端开发的方方面面，几乎已经是Java 服务器端开发的规范。

</div>

<div>

![](Spring%20MVC%20%E6%B5%85%E8%B0%88.resources/F783C97A-7200-4100-A212-E3B3730A4AA7.png) 
 \

</div>

<div>

回到MVC，MVC上文说到了不是Java 所特有的，它是一种软件架构的设计模式。

</div>

<div>

主要通过
1、拦截外部请求。2、完成对应逻辑处理。3、返回试图。而去看一个MVC框架也主要是从这几点出发去探究。

</div>

<div>

那么这种模式除了Spring MVC还有哪些类似的实现，可以对比的去看。

</div>

<div>

比如

</div>

<div>

Java 中除Spring MVC，几年前还有一个叫做 struts

</div>

<div>

Struts：1、依赖于servlet API实现（支持每一个模块有单独的Request
Processors（生命周期），但是模块中的所有Action必须共享相同的生命周期
），经典的单例模式，需要注意Action资源的并发安全问题。2、结合JSTL（一种比较方便的表达式语言），以JSP为基础实现视图。

</div>

<div>

Struts2：2、依赖于过滤器API
进行实现，每个线程所持有的资源都是私有的，不用担心并发问题。2、支持JSTL、OGNL，使用ValueStack将页面与相关对象解绑，使得部分属性对象可以复用。被诟病也被实证这种方式存在严重的方式遭到弃用。

</div>

<div>

C# 中的ASP.NET，Golang中的Beego，PHP中的yii。

</div>

<div>

就性能而言，除了yii，另外几个差异也还好，而且相对于最多的业务场景，瓶颈通常在数据库那儿，因为压根不是一个数量级，这方面基本可以忽略不计吧。通常一个框架封装程度越高相对复杂，性能一般是成反比的。

</div>

<div>

就是开发效率，给人直观感觉这几个基本相近的，Java 中的Struts
系列开发效率较低。

</div>

<div>

就生态而言：Java
无疑是当前阶段最繁荣，然后PHP&Golang，然后C#，不过PHP里面感觉好多东西有点封装过度，灵活性什么说实话没那么强。

</div>

<div>

这方面的比较仅仅为个人观点，最简单的方式就是写个hello world
demo，亲自感受下吧。

</div>

<div>

这里的主题是Java中的Spring MVC 所以话题回到Java 这边

</div>

<div>

Java 生态内比较的话，Spring MVC相对于struts：

</div>

<div>

1、struts 主要是类级别的拦截，而spring
MVC主要是方法级别的拦截，较开发而言，Spring MVC更加方便管理一些。

</div>

<div>

2、struts
基于过滤器实现，而过滤器这个东西压根就不应该作为请求的拦截及对应的分发（个人观点），而Spring
MVC 基于原生servlet
作为请求统一的入口，然后分发到各个controller中的request mapping。

</div>

<div>

3、struts 生态圈着实不如Spring MVC，Spring MVC
无缝集成生态内的其他功能框架，一直到今天的Spring Boot，Java
对于业务场景已经近乎简化到了极致。

</div>

<div>

4、开发效率上，指定一下controller，requestmapping，几行业务代码，基本就完成了一个小的HTTP
服务，在Spring Boot
就是一个注解加一个逻辑函数，连最基本的配置都省去了。Spring是占绝对优势的。

</div>

<div>

5、Spring 不再依赖于JSP 那种古老的模式，而struts
那种描述视图还有开发模式仍然跟JSP类似。

</div>

<div>

\

</div>

<div>

至于Spring MVC 的进一步分析，请看后期的文章

</div>
