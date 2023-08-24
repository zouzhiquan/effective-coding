---
altitude: 153.1301116943359
author: zouzq7@163.com
created: "2016-09-12 13:06:12 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19493111211531
longitude: 117.1117885160189
source: desktop.mac
title: controller详解
updated: "2016-09-12 13:17:25 +0000"
---

<div>

一个controller处理多个请求，在一个controller中设定多个requestMapping。

</div>

<div>

一个controller处理一个请求的不同提交方式：

</div>

<div>

```  
@RequestMapping("/bbtForum.do")  // <—— ① 指定控制器对应URL请求
```

</div>

<div>

\

</div>

<div>

[  // \<------ ②
如果URL请求中包括\"method=listAllBoard\"的参数，由本方法进行处理    
\@RequestMapping(params =
\"method=listAllBoard\")] 

</div>

<div>

```  
    // <—— ③ 如果URL请求中包括"method=listBoardTopic"的参数，由本方法进行处理
    @RequestMapping(params = "method=listBoardTopic")
```

</div>

<div>

[controller 处理方法特定的HTTP请求方法] 

</div>

<div>

[例：] 

</div>

<div>

[\@RequestMapping(params = \"method=createTopic\",method =
RequestMethod.POST)] 

</div>

<div>

[\
] 

</div>

<div>

[[只有当method=createTopic 请求以 HTTP POST 方式提交时，createTopic()
方法才会进行处理] \
] 

</div>

<div>

[\
] 

</div>

<div>

[\
] 

</div>

<div>

[\
] 

</div>

<div>

[\
] 

</div>
