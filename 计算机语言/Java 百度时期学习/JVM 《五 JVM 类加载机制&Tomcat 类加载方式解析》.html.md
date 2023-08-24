---
altitude: 51.67683029174805
author: zouzq7@163.com
created: "2017-09-05 10:54:16 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03840387027333
longitude: 116.4110662330526
source: desktop.mac
title: JVM 《五 JVM 类加载机制&Tomcat 类加载方式解析》
updated: "2017-09-26 08:50:51 +0000"
---

<div>

这一篇要说的是类加载机制。

</div>

<div>

所谓类加载机制也就是Java 虚拟机从磁盘装载 .class
文件或者网络中二进制字节流并且加载Java类的方式或者过程。

</div>

<div>

具体就是这个图～

</div>

<div>

![](JVM%20%E3%80%8A%E4%BA%94%20JVM%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6&Tomcat%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%96%B9%E5%BC%8F%E8%A7%A3%E6%9E%90%E3%80%8B.resources/26D32C6B-300F-4F4E-B117-0DA25AD2D161.png) 
 

</div>

<div>

\

</div>

<div>

当我们写一个类，并且将其装配到内存中由JVM驱动执行，要说的就是如何去装配。如何由静态的文件结构变为运行时数据结构。

</div>

<div>

类加载机制就这么几个过程：

</div>

<div>

加载（Loading）、验证（Verification）、准备(Preparation)、解析(Resolution)、初始化(Initialization)、使用(Using)和卸载(Unloading)7个阶段。其中准备、验证、解析3个部分统称为连接（Linking）

</div>

<div>

\

</div>

<div>

![](JVM%20%E3%80%8A%E4%BA%94%20JVM%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6&Tomcat%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%96%B9%E5%BC%8F%E8%A7%A3%E6%9E%90%E3%80%8B.resources/437AFC96-161D-426D-B8A8-5696D9DBF7D0.png) 
 

</div>

<div>

加载阶段：

</div>

<div>

1、图里面的加载就是指从网络或者文件中装载class的过程，也就是先无关对错将内容先装载进来。

</div>

<div>

这个过程是有三件主要的事儿要做的：

</div>

-   通过全限定名称来装载class
-   静态存储结构向运行时数据结构转化
-   产生对应的Class对象

<div>

\

</div>

<div>

连接阶段

</div>

<div>

2、验证阶段主要针对class 文件进行验证，是否合规。

</div>

<div>

主要就是这几种验证过程：文件格式验证（其实大多文件的区分是通过开头几位的魔术来验证的并非扩展名）、元数据验证（语义解析阶段）、字节码验证（语义验证）、符号饮用验证（验证动作是否可正常执行）

</div>

<div>

\

</div>

<div>

3、准备阶段说白了就是真正为类变量在方法区中分配内存并设置类变量初始值的阶段。这时候进行内存分配的仅包括类变量（被static修饰的变量），而不包括实例变量，实例变量将会在对象实例化时随着对象一起分配在堆中。

</div>

<div>

\

</div>

<div>

4、解析阶段就是JVM将常量池内的符号引用转换为直接引用的过程。解析动作针对的符号引用主要是有：类、接口、字段、类方法、接口方法、方法类型、方法句柄、调用点限制符。

</div>

<div>

\

</div>

<div>

初始化阶段

</div>

<div>

\

</div>

<div>

5、初始化阶段是类加载过程的最后一步。加载、连接阶段很显然都是JVM主动的在为我们的class作出一些动作，我们Class
里面的代码并没有执行。但是到了初始化阶段就真正开始执行class
中的代码了，执行顺序呢，自上而下根据加载顺序来。这也就是static
代码块执行的来源了。具体来说是由\<clinit\>(
)方法执行初始化的，然后这个方式是JVM可选的，若一个类中并没有类变量也就并不存在什么clinit方法了。当然啦，初始化阶段是并发安全的，这一点就不用赘述了，JVM
自身来保证这一点。利用这一点我们可以在并发中做很多事情的～

</div>

<div>

\

</div>

<div>

\

</div>

<div>

注：加载和连接并不是完全同步的，没有绝对的顺序，做完这个然后下一个。而是相对先后的。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

动态装载 是Java 很大的一个特性，也是非常好的一个特性。那触发加载Class
的时机是什么时候呢，显然易见就是用着这个类的时候。大约有这么几种情况

</div>

-   碰到 new,getstatic,putstatic,invokestatic
    这些需要使用类的指令但是JVM中并没有将这些类装载时
-   使用反射调用时
-   子类初始化，但是父类不在时
-   刚开始加载的主类

<div>

\

</div>

<div>

很显然的特点，想使用但是它不在的时候就会触发加载。

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

触发，加载过程都已经说清楚了。

</div>

<div>

\

</div>

<div>

使用不用说，就是说我们Class 中代码执行的过程

</div>

<div>

最后是卸载阶段。卸载的过程其实就是方法区中废弃类被清扫的过程。

</div>

<div>

被废弃的类，这个的判断标准之前应该是说过。

</div>

<div>

<div>

1\>该类所有的实例都已经被回收

</div>

<div>

2\>加载该方法的ClassLoader已经被回收

</div>

</div>

<div>

3\>该类对应的Class对象已无引用，并且无法通过反射访问。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

说的差不多了，然后再说一点，双亲委派模型～这是JVM 类加载的一种推荐的模型，而非标准化或者强制化模型。大多数都是按照JVM推荐的方式进行的，比如说tomcat。当然也有非双亲委派的，比如eclipse。

</div>

<div>

\

</div>

<div>

什么叫做双亲委派呢。

</div>

<div>

双亲其实和我们常用的双亲节点一个意思，就是指的父节点。也就是子节点能完成的加载动作并不会去执行，而是交给他的父亲，如果父亲完成不了再自己去加载。以此类推。

</div>

<div>

\

</div>

<div>

画个经典图看看吧～

</div>

<div>

![](JVM%20%E3%80%8A%E4%BA%94%20JVM%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6&Tomcat%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%96%B9%E5%BC%8F%E8%A7%A3%E6%9E%90%E3%80%8B.resources/DB321036-FB6A-4DD8-BD0F-5D775BCDF6A2.png) 
 

</div>

<div>

\

</div>

<div>

其中绿色框框为JVM默认的类加载器，红色的为tomcat自己的

</div>

<div>

蓝色箭头为继承方向，绿色箭头为委派方向。

</div>

<div>

\

</div>

<div>

为什么要这么做？

</div>

<div>

因为不同工程需要共享一些类，封闭一些类。

</div>

<div>

一个类是由
加载它的类加载器及这个类的全限定名称来标示的，也就是说我们的一个class
文件中的类由不同加载器加载，是不在一个类空间的。

</div>

<div>

当然啦tomcat中还有针对Jsp这类文件的解析和加载方式。

</div>

<div>

\

</div>

<div>

我觉着这个图我画的特好，再放一遍，差不多就这些～

</div>

<div>

![](JVM%20%E3%80%8A%E4%BA%94%20JVM%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6&Tomcat%20%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%96%B9%E5%BC%8F%E8%A7%A3%E6%9E%90%E3%80%8B.resources/26D32C6B-300F-4F4E-B117-0DA25AD2D161.png) 
 

</div>

<div>

\

</div>

<div>

\

</div>
