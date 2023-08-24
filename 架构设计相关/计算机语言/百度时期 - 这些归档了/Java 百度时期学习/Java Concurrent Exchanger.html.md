---
author: zouzq7@163.com
created: "2018-08-21 09:29:53 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Java Concurrent Exchanger
updated: "2018-08-21 11:28:59 +0000"
---

<div>

Exchanger是JUC里提供的供两个线程之间交换数据或者交互的一个并发工具，API也非常简单就两个重载的exchange泛型方法。

</div>

<div>

使用的场景：

</div>

<div>

通常用于将写读任务与任务逻辑处理任务分开，一个专门处理、一个专门操作。当一个线程带着数据调用exchange方法后，除非中断到达时间等，否则会一直等在另一个线程，并接受其数据。

</div>

<div>

看一下demo：

</div>

<div>

[import
] 

</div>

<div>

\

</div>

<div>

[public class
] [
 

</div>

<div>

[    ] [static
] [Exchanger
] exchanger [= new
] 

</div>

<div>

[    static void
] 

</div>

<div>

[        ] [new
] [new
] [()
 

</div>

<div>

[           
] 

</div>

<div>

[            ] [public void
] [()
 

</div>

<div>

[                ] [String
] 

</div>

<div>

[               
] [\"ThreadA
temp:\"] 

</div>

<div>

[               
for] [int
] 

</div>

<div>

[                   
] [\"线程A
已执行\"] 

</div>

<div>

[                    if
] 

</div>

<div>

[                       
] [
 

</div>

<div>

[                            ] temp[=
] 

</div>

<div>

[                           
] 

</div>

<div>

[                           
] [\"ThreadA
temp:\"] 

</div>

<div>

[                      ] [ 
}] [catch
] [InterruptedException
] [)
 

</div>

<div>

[                           
] 

</div>

<div>

[                        }] 

</div>

<div>

[                    }] 

</div>

<div>

[                   
] [
 

</div>

<div>

[                       
] 

</div>

<div>

[                  ] [ 
}] [catch
] [InterruptedException
] [)
 

</div>

<div>

[                       
] 

</div>

<div>

[                    }] 

</div>

<div>

[                }] 

</div>

<div>

[            }] 

</div>

<div>

[       
})] 

</div>

<div>

[    }] 

</div>

<div>

[    ] [static void
] 

</div>

<div>

[        ] [new
] [new
] [()
 

</div>

<div>

[           
] 

</div>

<div>

[            ] [public void
] [()
 

</div>

<div>

[                ] [String
] 

</div>

<div>

[               
] [\"ThreadB
temp:\"] 

</div>

<div>

[               
for] [int
] 

</div>

<div>

[                   
] [\"线程B
已执行\"] 

</div>

<div>

[                    if] [
(] 

</div>

<div>

[                        ] [try
] 

</div>

<div>

[                            ] temp[=
] 

</div>

<div>

[                           
] 

</div>

<div>

[                           
] [\"ThreadB
temp:\"] 

</div>

<div>

[      ] [                 
}] [
(] [InterruptedException
] [)
 

</div>

<div>

[                           
] 

</div>

<div>

[                        }] 

</div>

<div>

[                    }] 

</div>

<div>

[                   
] [
 

</div>

<div>

[                       
] 

</div>

<div>

[            ] [        }
] [catch
] [InterruptedException
] [)
 

</div>

<div>

[                       
] 

</div>

<div>

[                    }] 

</div>

<div>

[                }] 

</div>

<div>

[            }] 

</div>

<div>

[       
})] 

</div>

<div>

[    }] 

</div>

<div>

\

</div>

<div>

[    ] [public static void
] [String
] [)
 

</div>

<div>

[       
] 

</div>

<div>

[       
] 

</div>

<div>

[    }] 

</div>

<div>

[}] 

</div>

<div>

核心的实现函数就这两个，有兴趣可以看看。

</div>

<div>

<div>

[arenaExchange] ([Object
] [, boolean
] [, long
] )

</div>

</div>

<div>

<div>

[] ([Object
] [, boolean
] [, long
] )

</div>

</div>

<div>

\

</div>
