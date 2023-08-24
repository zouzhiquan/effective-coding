---
author: zouzq7@163.com
created: "2018-08-06 09:59:16 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
reminder-order: "2018-11-13 16:06:50 +0000"
source: desktop.mac
title: Java Concurrent Atomic实现原理&源码解读（JDK 10）
updated: "2019-02-25 07:09:49 +0000"
---

<div>

JDK 10，可以说是很新了，比起JDK
8更新了不少实现，比如说下面会讲到VarHandle

</div>

<div>

说了这么多篇原理类的，终于要开始看源码了。这一篇描述atomic，主要简述Concurrent里面的核心使用的类，其他相关的，大家看的时候可以顺便进去看两眼内容不多，还可以。

</div>

<div>

是时候看一眼JDK了，忽然发现IDEA可能是读源码最好用的工具了，sun包的暂时不看就单说Concurrent里面的
。

</div>

<div>

![](Java%20Concurrent%20Atomic%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86&%E6%BA%90%E7%A0%81%E8%A7%A3%E8%AF%BB%EF%BC%88JDK%2010%EF%BC%89.resources/67FBCDD0-7A06-4DE2-A0F4-4FB2DFE0FF7D.png) 
 \

</div>

<div>

\

</div>

<div>

\

</div>

<div>

AtomicBoolean、AtomicInteger等是atomic中比较经典的一类，这里不描述API，就单说源码实现。

</div>

<div>

拿AtomicBoolean来说，首先看到的是：

</div>

<div>

[public class ] AtomicBoolean
[implements ] java.io.Serializable {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
[4654671469794556979L] 

</div>

<div>

[    private static final ] VarHandle
[VALUE] 

</div>

<div>

[    static ] {

</div>

<div>

        [try ] {

</div>

<div>

            MethodHandles.Lookup l =
MethodHandles.[lookup] 

</div>

<div>

[            ] [VALUE
] =
l.findVarHandle(AtomicBoolean.[class,
] [,
int] 

</div>

<div>

[        ] } [catch
] (ReflectiveOperationException e) {

</div>

<div>

            [throw new
] 

</div>

<div>

[        ] }

</div>

<div>

    }

</div>

:::  
不仅是AtomicBoolean，其他的原子类也都是可序列化，并且持有一个通过JNI调用本地方法的VarHandle，这个类实在sun.lang里实现的，也不复杂有兴趣可以去看看。首先完成静态属性的初始化，（这里可以顺道提一点类的初始化顺序：[父类静态变量，] 
:::

:::  
[再往下看：] 
:::

:::  
<div>

[private volatile int
] 

</div>

<div>

[一个非对象类型的volatile int
value值，这个值是整个AtomicBoolean逻辑的核心，后续的实现的功能函数都会围绕它展开。1:true
0:false，同时volatile避免的必要是的可见性及防止重排序。] 

</div>
:::

:::  
<div>

[/\*\*] 

</div>

<div>

[ \* Creates a new
 [\@code
] [AtomicBoolean}
with the given initial
value.] 

</div>

<div>

[
\*] 

</div>

<div>

[ \*
] [\@param
] [initialValue
] [the
initial
value] 

</div>

<div>

[
\*/] 

</div>

<div>

[public
] [boolean
] [initialValue)
 

</div>

<div>

[    ] [value
] [=
initialValue ? ] [1
] [:
] 

</div>

<div>

[}] 

</div>

<div>

[/\*\*] 

</div>

<div>

[ \* Creates a new
 [\@code
] [AtomicBoolean}
with initial value
 [\@code
] 

</div>

<div>

[
\*/] 

</div>

<div>

[public
] [()
 

</div>

<div>

[}] 

</div>

<div>

这个是构造函数，可以清晰看到value的使用。

</div>
:::

:::  
\
:::

:::  
<div>

[/\*\*] 

</div>

<div>

[ \* Returns the current
value,] 

</div>

<div>

[ \* with memory effects as specified by
 [\@link
] 

</div>

<div>

[ \*] 

</div>

<div>

[ \* ] [\@return
] [the
current value] 

</div>

<div>

[ \*/] 

</div>

<div>

[public final boolean
] ()
{

</div>

<div>

    [return ] [value
] !=
[0] 

</div>

<div>

}

</div>

<div>

get函数直接返回就ok，因为是volatile，volatile的直接写操作是原子的，保证了return的值绝对是各线程更新后最新的，不存在在工作内存为刷入主存，而导致的value值不一致的情况。问题不大，继续向下：

</div>

<div>

\

</div>

<div>

[/\*\*] 

</div>

<div>

[ \* Atomically sets the value to
 [\@code
] 

</div>

<div>

[ \* if the current value
 [\@code
] [==
expectedValue},] 

</div>

<div>

[ \* with memory effects as specified by
 [\@link
] 

</div>

<div>

[ \*] 

</div>

<div>

[ \* ] [\@param
] [expectedValue
] [the expected
value] 

</div>

<div>

[ \* ] [\@param
] [newValue
] [the new
value] 

</div>

<div>

[ \* ] [\@return
] [\@code
] [true}
if successful. False return indicates
that] 

</div>

<div>

[ \* the actual value was not equal to the expected
value.] 

</div>

<div>

[ \*/] 

</div>

<div>

[public final boolean
] ([boolean
] expectedValue[, boolean
] newValue) {

</div>

<div>

    [return
] 

</div>

<div>

[                              
] (expectedValue ? [1
] :
[0] 

</div>

<div>

[                              
] (newValue ? [1
] :
[0] 

</div>

<div>

}

</div>

<div>

这里开始使用JNI调用本地函数了，进到源码里看一眼：这里我就不放注释了，着实太长。（这个方法在VarHandle里）

</div>

<div>

[public final native] 

</div>

<div>

[\@MethodHandle.PolymorphicSignature] 

</div>

<div>

[\@HotSpotIntrinsicCandidate] 

</div>

<div>

[boolean
] (Object\...
args)[;] 

</div>

<div>

可以看到修饰这个函数的关键词，native 关键词，native主要有两个作用

</div>
:::

:::  
1、JDK依赖于C++函数直接完成某些系统调用及函数.
:::

:::  
2、Java
C++联合开发时，可以顺道调用C++工具函数，当然了驱动外部程序现在有了ProcessBuilder但是两者还是存在很大区别，JNI是直接调用的函数，而ProcessBuilder是完成外部完整程序的运行，比如说一条shell，一个Cpp文件，给我们直观的感受是开了个进程。
:::

:::  
这里native 的使用场景，完全符合第一种场景。
:::

:::  
Java
8及之前底层能保证比较并操作原子性的方式有这么几种：（因为在底层讨论，所以AtomicInteger属于上层实现了，这里不算是一种，但在上层应用中还是算一种原子的）
:::

<div>

1、使用原子性的FieldUpdaters，利用了反射机制，操作开销也会更大；

</div>

:::  
<div>

2、使用sun.misc.Unsafe提供的JVM内置函数API，虽然这种方式比较快，但它会损害安全性和可移植性。

</div>
:::

:::  
在Java 9时出现了VarHandle来部分替代java.util.concurrent.atomic 和
sun.misc.Unsafe。
:::

:::  
<div>

VarHandle
可以与任何字段、数组元素或静态变量关联，支持在不同访问模型下对这些类型变量的访问，包括简单的
read/write 访问，volatile 类型的 read/write 访问，和
CAS(compare-and-swap)等。虽然unsafe仍旧保留使用，但Java
9之后用的大部分就都是VarHandle了，官方称为变量句柄，然后VarHandle依赖于VarForm使用，然后VarForm包含一下两个变量及相关操作以完成VarHandle的支持。

</div>

<div>

[final ] [\@Stable
] MethodType\[\]
[methodType_table] 

</div>

<div>

[final ] [\@Stable
] MemberName\[\]
[memberName_table] 

</div>

<div>

主要支持链接所有签名的多态方法，注意compare的第一个注解。

</div>

<div>

然后第二个注解的含义：

</div>

<div>

如果使用@SetNativeMethodPrefix注解本地方法，run的时候就会得到一个警告。

</div>

<div>

比如：

</div>

<div>

For instance for Thread::isInterrupted:

</div>

<div>

Compiler intrinsic is defined for method

</div>

<div>

\[java.lang.Thread.isInterrupted(Z)Z\], but the method is not annotated
with

</div>

<div>

\@HotSpotIntrinsicCandidate. Method will not be inlined.

</div>
:::

:::  
<div>

\

</div>
:::

:::  
[然后boolean类型原子性的保证到这里就看完了。] 
:::

:::  
然后是AtomicInteger：
:::

:::  
<div>

[public class ] AtomicInteger [extends
] Number [implements
] java.io.Serializable {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
[6214790243416807050L] 

</div>

<div>

\

</div>

<div>

[   
] 

</div>

<div>

[     \* This class intended to be implemented using VarHandles, but
there] 

</div>

<div>

[     \* are unresolved cyclic startup
dependencies.] 

</div>

<div>

[     \*/] 

</div>

<div>

[    ] [private static final
] jdk.internal.misc.Unsafe [U
] =
jdk.internal.misc.Unsafe.[getUnsafe] 

</div>

<div>

[    private static final long
] [VALUE
] =
[U] .objectFieldOffset(AtomicInteger.[class,
] 

</div>

<div>

\

</div>

<div>

[    private volatile int
] 

</div>

<div>

\

</div>
:::

:::  
跟boolean一样，支持序列化，同样维护着一个[    private volatile int
] ，不同的是AtomicInteger是依赖于UnSafe调用外部方法实现的。
:::

:::  
下面是Unsafe中compareAndSet的实现：
:::

:::  
<div>

[/\*\*] 

</div>

<div>

[ \* Atomically updates Java variable to
 [\@code
] [x}
if it is
currently] 

</div>

<div>

[ \* holding
 [\@code
] 

</div>

<div>

[ \*] 

</div>

<div>

[ \*
] [This
operation has memory semantics of a
 [\@code
] [volatile}
read] 

</div>

<div>

[ \* and write.  Corresponds to C11
atomic_compare_exchange_strong.] 

</div>

<div>

[ \*] 

</div>

<div>

[ \* ] [\@return
] [\@code
] [true}
if successful] 

</div>

<div>

[ \*/] 

</div>

<div>

[\@HotSpotIntrinsicCandidate] 

</div>

<div>

[public final native boolean
] (Object
o[, long
] 

</div>

<div>

[                                             int
] 

</div>

<div>

[                                             int
] 

</div>

<div>

\

</div>
:::

:::  
同样调用本地方法，不多赘述。
:::

:::  
其他方法的使用，不如说+1、-1等看官方API就可以了，底层的函数比较简单通俗易懂。
:::

:::  
Atomic\*Array：VarHandle实现，其中维护了一个数组对象，但是相对于其他对象来说，使用了MethodHandles.[arrayElementVarHandle] )来完成初始。
:::

:::  
<div>

[public class ] AtomicIntegerArray
[implements ] java.io.Serializable {

</div>

<div>

    [private static final long
] [serialVersionUID
] =
[2862133569453604235L] 

</div>

<div>

[    private static final ] VarHandle
[AA] 

</div>

<div>

[        ] =
MethodHandles.[arrayElementVarHandle] 

</div>

<div>

[    private final int] \[\]
[array] 

</div>

<div>

其中所调用的本地方法也与其他不同，volatile相关特性依赖于native函数实现。

</div>

<div>

[public final native] 

</div>

<div>

[\@MethodHandle.PolymorphicSignature] 

</div>

<div>

[\@HotSpotIntrinsicCandidate] 

</div>

<div>

Object [getVolatile] (Object\...
args)[;] 

</div>

<div>

\

</div>
:::

:::  
AtomicBoolean：VarHandle
:::

<div>

AtomicInteger：UnSafe

</div>

<div>

AtomicLong：UnSafe

</div>

<div>

AtomicReference：Varhandle，但与AtomicBoolean不同的是AtomicReference多了一个泛型处理。

</div>

:::  
<div>

Atomic里面的实现总来说就是样样的。

</div>
:::

<div>

\

</div>
