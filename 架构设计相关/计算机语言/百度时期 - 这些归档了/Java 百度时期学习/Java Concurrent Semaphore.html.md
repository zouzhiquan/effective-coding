---
author: zouzq7@163.com
created: "2018-08-20 15:14:58 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent Semaphore
updated: "2018-08-21 09:31:36 +0000"
---

<div>

Semaphore一种通常用于维持数量的信号量工具，在Java
并发中最常见的使用就是维持并发线程的个数，正如之前提到的，并不是并发场景线程越多越好，应该是跟对应的业务场景及CPU数量共同决定。而需要维持数量时，我们大致就需要一个JUC里面Semaphore这样一个信号量工具。Semaphore
同样依赖于AQS实现（shared），内部持有一个Sync对象，同样存在公平实现、非公平实现两种模式（可以在构造时指定fair参数）。

</div>

<div>

原理：

</div>

<div>

每次线程执行前夕都会从这里默认通过一下许可，然后执行完毕时释放一下许可，根据维系的数量来决定当前线程是该等待还是执行。

</div>

<div>

简单看一下源码，实现蛮简单的

</div>

<div>

首先是持有AQS对象

</div>

<div>

[public class ] [Semaphore
] [implements
] [java.io.Serializable
] 

</div>

<div>

[    ] [private static final long
] [serialVersionUID
] [=
-] 

</div>

<div>

[    /\*\* All mechanics via AbstractQueuedSynchronizer subclass
\*/] 

</div>

<div>

[    ] [private final
] [Sync
] 

</div>

<div>

取许可及释放操作

</div>

<div>

[final int
] [int
] [)
 

</div>

<div>

[    ] [for
] [)
 

</div>

<div>

[        ] [int
] available [=
] 

</div>

<div>

[        int ] remaining [=
] available [-
] 

</div>

<div>

[        if
] remaining
[\< ] [0
] 

</div>

<div>

[           
] available[,
] 

</div>

<div>

[            ] [return
] 

</div>

<div>

[    }] 

</div>

<div>

[}] 

</div>

<div>

\

</div>

<div>

[protected final boolean
] [int
] [)
 

</div>

<div>

[    ] [for
] [)
 

</div>

<div>

[        ] [int
] current [=
] 

</div>

<div>

[        int ] next [=
] current [+
] 

</div>

<div>

[        if
] next
[\< ] current[) //
overflow] 

</div>

<div>

[            ] [throw new
] [\"Maximum
permit count
exceeded\"] 

</div>

<div>

[        if
] current[,
] 

</div>

<div>

[            ] [return
true;] 

</div>

<div>

[    }] 

</div>

<div>

[}] 

</div>

<div>

公平非公平两种实现版本：

</div>

<div>

[/\*\*] 

</div>

<div>

[ \* NonFair version] 

</div>

<div>

[ \*/] 

</div>

<div>

[static final class ] [NonfairSync
] [extends
] [
 

</div>

<div>

[    ] [private static final long
] [serialVersionUID
] [=
-] 

</div>

<div>

\

</div>

<div>

[   
] [int
] [)
 

</div>

<div>

[       
] 

</div>

<div>

[    }] 

</div>

<div>

\

</div>

<div>

[    ] [protected int
] [int
] [)
 

</div>

<div>

[        ] [return
] 

</div>

<div>

[    }] 

</div>

<div>

[}] 

</div>

<div>

[/\*\*] 

</div>

<div>

[ \* Fair version] 

</div>

<div>

[ \*/] 

</div>

<div>

[static final class ] [FairSync
] [extends
] [
 

</div>

<div>

[    ] [private static final long
] [serialVersionUID
] [=
] 

</div>

<div>

\

</div>

<div>

[   
] [int
] [)
 

</div>

<div>

[       
] 

</div>

<div>

[    }] 

</div>

<div>

\

</div>

<div>

[    ] [protected int
] [int
] [)
 

</div>

<div>

[       
] [
(] [)
 

</div>

<div>

[            ] [if
] 

</div>

<div>

[                ] [return
-] 

</div>

<div>

[            int ] available [=
] 

</div>

<div>

[            int ] remaining [=
] available [-
] 

</div>

<div>

[            if] [
(] remaining [\<
] [0
] 

</div>

<div>

[               
] available[,
] 

</div>

<div>

[                ] [return
] 

</div>

<div>

[        }] 

</div>

<div>

[    }] 

</div>

<div>

[}] 

</div>

<div>

\

</div>
