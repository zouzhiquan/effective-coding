---
altitude: 145
author: zouzq7@163.com
created: "2016-10-24 07:02:41 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19380817680663
longitude: 117.1082030691728
source: desktop.mac
title: 关于异步提交form表单时的问题
updated: "2016-10-24 07:06:42 +0000"
---

<div>

1、当form表单内嵌在table中时，ajax提交文件会失败。原因未知

</div>

<div>

\

</div>

<div>

ajax代码：

</div>

<div>

```  

function mainAjax_(data,url){
    $.ajax({
        url: url,
type: 'POST',
data:data,
async:true,
cache: false,
contentType : false,
processData:false,
success: function(response){
            alert(response);
},
error: function(XMLHttpRequest, textStatus) {
            alert("error");
}
    })
}



function lilungzl() {
    var formdata=new FormData($("#lilungzl")[0]);
mainAjax_(formdata,lilugzlUrl);
```

</div>

<div>

[[[}] 

</div>

<div>

[[\
] 

</div>

<div>

上面是ajax异步提交表单

</div>

<div>

\

</div>

<div>

页面代码：

</div>

<div>

\

</div>

<div>

```  
<tr>
        <form id="lilungzl" method="post" enctype="multipart/form-data">
    <td>教师每学期理论工作量：</td>
    <td><input  type="file" class="form-control" name="num"></td>
    <td class="pull-right"><a onclick="lilungzl();" class="btn btn-default">上传</a></td>
    </form>
```

</div>

<div>

[[[\</tr\>] 

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>
