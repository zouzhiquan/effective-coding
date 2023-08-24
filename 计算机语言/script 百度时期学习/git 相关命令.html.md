---
altitude: 50.96146392822266
author: zouzq7@163.com
created: "2017-08-11 03:23:05 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 40.03847921185016
longitude: 116.4110810356631
source: desktop.mac
title: git 相关命令
updated: "2017-08-14 02:03:19 +0000"
---

<div>

git branch -a 查看分支

</div>

<div>

git pull 提交

</div>

<div>

git show 查看上次提交详情

</div>

<div>

[git status
以查看在上次提交之后是否有修改。-s输出详细信息] 

</div>

<div>

git add将内容提交到缓冲区

</div>

<div>

git commit 将缓冲区内容提交到仓库中去

</div>

<div>

例：

</div>

<div>

```  
$ git add hello.php
$ git status -s
A  README
A  hello.php
$ $ git commit -m '第一次版本提交'[master (root-commit) d32cf1f]第一次版本提交2 files changed,4 insertions(+)
 create mode 100644 README
 create mode 100644 hello.php
```

</div>

<div>

[\
] 

</div>

<div>

git commit -a 跳过之前的add 步骤

</div>

<div>

[\
] 

</div>

<div>

git reset HEAD 用于取消已缓存的内容（恢复到我们做出修改之前的样子）

</div>

<div>

[\
] 

</div>

<div>

git rm 从缓冲区中删除条目（直接移除该条目）

</div>

<div>

[\
] 

</div>

<div>

git mv 用于移动或重命名一个 文件、目录、软连接

</div>

<div>

[\
] 

</div>

<div>

git branch （branchname） 用于创建一个分支

</div>

<div>

[\
] 

</div>

<div>

git checkout （branchname） 用于切换到哪个分支

</div>

<div>

[\
] 

</div>

<div>

git merge 用于合并分支

</div>

<div>

[\
] 

</div>

<div>

[[git checkout -b (branchname)
命令来创建新分支并立即切换到该分支下，从而在该分支中操作] 

</div>

<div>

[\
] 

</div>

<div>

git branch -d （branchname） 删除某个分支

</div>

<div>

\

</div>

<div>

git log
查看提交记录 [[[[ ] \--oneline
选项 查看简介提交详情
 ] [[---reverse查看逆向所有记录 -
-author
查看指定作者的提交记录]  - -
since、- - brfore 提供按时间日期查询 （也可用- -until - -after
替代）] 

</div>

<div>

\

</div>

<div>

git tag 签订标签

</div>

<div>

例：[[[\$ git
tag] 
[-] [a
v1] 
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
