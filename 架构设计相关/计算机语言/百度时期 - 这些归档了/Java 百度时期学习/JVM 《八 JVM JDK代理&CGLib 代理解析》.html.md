---
altitude: 50.70237731933594
author: zouzq7@163.com
created: "2017-09-26 08:07:28 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03824427313821
longitude: 116.4109577604472
source: desktop.mac
title: JVM 《八 JVM JDK代理&CGLib 代理解析》
updated: "2017-11-25 14:05:47 +0000"
---

<div>

前言&背景

</div>

<div>

现在Spring 什么的好火。

</div>

<div>

Spring 很经典的一个特性是AOP

</div>

<div>

AOP 的基础是代理。

</div>

<div>

代理分为静态代理、动态代理。

</div>

<div>

静态代理，不好处挺明显的，工程中各式各样的代理代码，过于冗余。

</div>

<div>

动态代理显得就简洁多了。

</div>

<div>

动态代理在java中现在大约有这么两种技术。

</div>

<div>

JDK 原生反射

</div>

<div>

字节码操作增强反射（我第一次学到这个的时候感觉特别特别高大上，虽然现在也这么感觉

</div>

<div>

然后字节码操作技术 比较火的就是ASM了，也就是CGLib所使用的技术。

</div>

<div>

\

</div>

<div>

至于啥是代理，就不多解释了。就是产生一个代理操作来替代原核心操作并且增加一些便利的具有各个场景特性的附加操作。

</div>

<div>

然后JDK&CGLib表象上或者语法上的区别，一个面向接口、一个面向类、Spring
根据情况使用也可强制。然后单独使用的时候写法儿上的不同。

</div>

<div>

\

</div>

<div>

那在JVM层面去看待这个问题是怎样的呢？

</div>

<div>

其实本质要讨论的就是Java 反射、字节码操作具体是怎么样的，有什么区别。

</div>

<div>

\

</div>

<div>

先说一个反射的总体概念

</div>

<div>

反射，顾名思义
反着射，跟常规编写、编译、解释执行不同。我当时看反射的时候挺蒙蔽的，反射？反？射？到系统的看编程思想的RTTI那一块，对于反射的定义还有这个名字多少有点认可了。

</div>

<div>

\

</div>

<div>

下面是百度的解释，感觉挺糟糕的

</div>

<div>

也就是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为java语言的反射机制。

</div>

<div>

\

</div>

<div>

一个比较可取的解释

</div>

<div>

Instances of the class Class represent classes and interfaces in a
running Java application. An enum is a kind of class and an annotation
is a kind of interface. Every array also belongs to a class that is
reflected as a Class object that is shared by all arrays with the same
element type and number of dimensions The primitive Java types (boolean,
byte, char, short, int, long, float, anddouble), and the keyword void
are also represented as Class objects.

</div>

<div>

\

</div>

<div>

综合起来看，也就是运行期间根据名称去动态的加载及使用一个编译期未知的类，然后根据加载进来的Class对象，来调用该类具体的方法的行为。（静态加载、根据已知信息直接使用）

</div>

<div>

\

</div>

<div>

为什么是反着呢，因为我们常规写程序都是既定使用的类&方法然后import、然后加载产生Class对象&使用类的具体信息
驱动执行。（已知信息使用）

</div>

<div>

而反射是在运行时除了名称完全未知，通过名称获取&加载进来，然后产生Class对象，通过Class
对象的信息来获取调用这个类具体的内容。（动态加载、透过 Class
获取信息去调用）

</div>

<div>

当然，这个类通常是远程调用或者别的什么方式来获取使用的。

</div>

<div>

\

</div>

<div>

Java反射也就是上面说的常规过程，来加载&使用一个完全未知的类。

</div>

<div>

而且JDK代理有个特点，是针对接口来实现的，也就是面向接口来编程，这也就导致了
接口一致而非对象一致。并且Java 反射效率总体来说是比较低下。

</div>

<div>

\

</div>

<div>

为了解决这些个问题，ASM之类的字节码操作技术出现了。

</div>

<div>

这里拿ASM 来说

</div>

<div>

\

</div>

<div>

这些类似的字节码操作技术其实也就是对 Class
文件的CURD来改变类的信息还有一些行为，甚至会产生一新类。来完成我们运行时的的一些动态加载需求。

</div>

<div>

其中ASM核心类有 ClassLoader（解析Class
文件用）、ClassWriter（重新构建编译的类）、ClassAdapter（委托方法调用给同样实现ClassVister接口的对象）

</div>

<div>

有兴趣可以去看看ASM，各式各样的设计模式。什么 访问者、职责链、桥接
都有。读码学习～

</div>

<div>

\

</div>

<div>

然后ASM使用还是很广泛的，CGLib、Hibernate的懒加载都用到了，熟悉Hibernate的同学应该很清楚（在内存中生成的对象并不是你实现的那个类）。ASM效果很明显，事件驱动、可以只关心对其编程有意义的地方，效率还高。

</div>

<div>

\

</div>

<div>

然后这一篇放在这里说感觉比较合适，因为前刚推完加载过程、Class文件、对象什么，对于理解反射更合适吧

</div>

<div>

\

</div>
