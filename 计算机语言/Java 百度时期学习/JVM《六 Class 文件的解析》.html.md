---
altitude: 53.06462097167969
author: zouzq7@163.com
created: "2017-09-25 09:15:14 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03831200305581
longitude: 116.4111223353096
source: desktop.mac
title: JVM《六 Class 文件的解析》
updated: "2017-09-26 10:47:59 +0000"
---

<div>

上一篇说的是类加载机制，其中刚开始装载阶段是对二进制流进行装载或者解析对应的
.class 文件。

</div>

<div>

而这一篇要说的就是那个被加载的 .class
文件，也顺道说一下 .Java 文件的编译过程，也就是如何变为 .class 文件的。

</div>

<div>

\

</div>

<div>

Class
文件组成是由JVM字节码组成的，这些字节码组成了我们的这个类中的所有内容。

</div>

<div>

\

</div>

<div>

说一下大体的结构&组成。（装作英文很好的样子）

</div>

<div>

1、按照惯例，开头几位是标示为class 文件的魔数（magic）。

</div>

<div>

2、然后是版本号，主版本号，次版本号（version）

</div>

<div>

3、然后是常量池入口（constant_pool）

</div>

<div>

　　其中呢，常量池主要存放两大类常量：字面量（"123"
这就是个字面量）、符号引用

</div>

<div>

　　其中符号引用呢，就是类或者接口的全限定标示、字段名称&描述符（public
static 这种）、方法名&描述符

</div>

<div>

　　　　其中呢，JVM是在加载Class
文件时才会进行动态链接，也就是说我们class
文件中压根仅仅是存储而非保存最终的一些内存布局信息（这个是JVM通过解析文件然后分类信息，然后进行标示存放的）

</div>

<div>

             
 说到了符号引用，还有一个类似的东西叫做直接引用，直接引用是一种能直接找到目标的指针或者偏移量、再或者句柄。如果产生直接引用了，也基本上就是在运行状态了。

</div>

<div>

\

</div>

<div>

4、访问表示（access_flag）

</div>

<div>

比如这个class
是public、final、abstrack什么的。同样也是一个十六进制的标示

</div>

<div>

\

</div>

<div>

5、然后是这个类一些外围关系吧（this_class、super_class、interface）

</div>

<div>

\

</div>

<div>

6、fields 字段表。这就是存放该class
类变量、对象变量的地方。这个并不完全是按照Java
文件来的。也就是说会默认为我们添加一些东西（注意
这里添加其实是一些补充（比如内部类对于外围类的指向）、而非这种从父类继承来的东西）

</div>

<div>

\

</div>

<div>

7、然后是方法表 （methods） 

</div>

<div>

就是用来存放方法的。这一块，同样会添加一些原本没有的函数，比如Clinit
、init 。

</div>

<div>

\

</div>

<div>

 8、属性表（attributes）

</div>

<div>

上面描述的几类都会有自己的属性表集合，做用呢，就是描述特殊场景的专有信息。（比如异常啊、内部类啊、编译器添加啊、源码与编译后的行号关系啊、源码文件的名称啊这类的东西）

</div>

<div>

\

</div>

<div>

然后放一个demo更清晰吧

</div>

<div>

![](JVM%E3%80%8A%E5%85%AD%20Class%20%E6%96%87%E4%BB%B6%E7%9A%84%E8%A7%A3%E6%9E%90%E3%80%8B.resources/352FAD41-B431-4BCC-BBAC-65E479C51C22.png) 
 

</div>

<div>

\

</div>

<div>

这么一个类，它的class文件是这样的

</div>

<div>

\

</div>

<div>

![](JVM%E3%80%8A%E5%85%AD%20Class%20%E6%96%87%E4%BB%B6%E7%9A%84%E8%A7%A3%E6%9E%90%E3%80%8B.resources/1.class.png) 
 

</div>

<div>

\

</div>

<div>

这就是Class 文件的一个大体的结构

</div>

<div>

具体的解析实际上就是把我们静态存储结构校验并且转为运行时数据结构的过程。由存储结构到可执行结构。然后可执行结构再由JVM 驱动解释执行。

</div>

<div>

\

</div>

<div>

那 .Java 是如何变为 .class 的呢

</div>

<div>

其实这个过程不同的编译器具体过程是不同的，但是还是大体能总结出一个流程。

</div>

<div>

画个图吧。

</div>

<div>

\

</div>

<div>

![](JVM%E3%80%8A%E5%85%AD%20Class%20%E6%96%87%E4%BB%B6%E7%9A%84%E8%A7%A3%E6%9E%90%E3%80%8B.resources/3084D9AA-2DC9-41F7-ACDA-87B00AD096F1.png) 
 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

然后在中间过程中编译器会对我们的代码进行一种叫做编译优化的优化。比如字面量拼接啊之类的。这个与内容无关不赘述。

</div>

<div>

\

</div>

<div>

\

</div>

<div>

![](JVM%E3%80%8A%E5%85%AD%20Class%20%E6%96%87%E4%BB%B6%E7%9A%84%E8%A7%A3%E6%9E%90%E3%80%8B.resources/6CADC505-4DD9-4897-BFF0-28D21C36E275.png) 
 

</div>

<div>

\

</div>

<div>

然后Java 文件什么结构我们很清楚、Java文件怎么变为class
文件现在也已经都知道了，那class
文件产生的指令如何驱动我们计算机执行呢，这个其实就是执行引擎来驱动执行的。有兴趣可以去看看，也挺好玩的，不过感觉这个虽说是JVM很重要的一部分，但是感觉已经不属于我们能左右的Java的部分了～

</div>

<div>

\

</div>
