---
altitude: 155.0467834472656
author: zouzq7@163.com
created: "2016-09-13 11:49:59 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19496399376877
longitude: 117.1117595321736
source: desktop.mac
title: java路径问题
updated: "2016-10-24 00:40:42 +0000"
---

<div>

./当前目录

</div>

<div>

../返回父级目录

</div>

<div>

工程中：都是以项目为根目录（当前目录）

</div>

<div>

比如：

</div>

<div>

               项目名：hello      
其中src文件夹下com.company包下的类或文件的目录就是，src/com/company/名字.扩展名

</div>

<div>

\

</div>

<div>

获取工程路径：\${pageContext.request.contextPath}:相当于／name

</div>

<div>

\

</div>

<div>

this.getServletContext().getRealPath("");//获取工程路径

</div>

<div>

\

</div>

<div>

\

</div>

<div>

```  
getResourceAsStream(fileName);
```

</div>

<div>

相当于／web－inf／classes路径

</div>

<div>

\

</div>

<div>

<http://blog.csdn.net/z69183787/article/details/22774537>

</div>

<div>

\

</div>

<div>

<http://blog.csdn.net/tragedyxd/article/details/46830943>

</div>

<div>

\

</div>

<div>

<http://blog.csdn.net/bohemia/article/details/704427>

</div>

<div>

\

</div>

<div>

\

</div>
