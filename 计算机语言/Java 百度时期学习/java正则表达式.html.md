---
altitude: 155.8661346435547
author: zouzq7@163.com
created: "2016-10-11 13:20:46 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19502065861865
longitude: 117.1117847214101
source: desktop.mac
title: java正则表达式
updated: "2016-10-11 13:21:02 +0000"
---

<div>

[//
反斜杠] 

<div>

[/t 间隔
(\'/u0009\')] 

</div>

<div>

[/n 换行
(\'/u000A\')] 

</div>

<div>

[/r 回车
(\'/u000D\')] 

</div>

<div>

[/d 数字
等价于\[0-9\]] 

</div>

<div>

[/D 非数字
等价于\[\^0-9\]] 

</div>

<div>

[/s 空白符号
\[/t/n/x0B/f/r\]] 

</div>

<div>

[/S 非空白符号
\[\^/t/n/x0B/f/r\]] 

</div>

<div>

[/w 单独字符
\[a-zA-Z_0-9\]] 

</div>

<div>

[/W 非单独字符
\[\^a-zA-Z_0-9\]] 

</div>

<div>

[/f
换页符] 

</div>

<div>

[/e
Escape] 

</div>

<div>

[/b
一个单词的边界] 

</div>

<div>

[/B
一个非单词的边界] 

</div>

<div>

[/G
前一个匹配的结束] 

</div>

<div>

\

</div>

<div>

[\^为限制开头] 

</div>

<div>

[\^java    
条件限制为以Java为开头字符] 

</div>

<div>

[\$为限制结尾] 

</div>

<div>

[java\$    
条件限制为以java为结尾字符] 

</div>

<div>

[. 
条件限制除/n以外任意一个单独字符] 

</div>

<div>

[java..    
条件限制为java后除换行外任意两个字符] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[加入特定限制条件「\[\]」] 

</div>

<div>

[\[a-z\]     条件限制在小写a to
z范围中一个字符] 

</div>

<div>

[\[A-Z\]     条件限制在大写A to
Z范围中一个字符] 

</div>

<div>

[\[a-zA-Z\] 条件限制在小写a to z或大写A to
Z范围中一个字符] 

</div>

<div>

[\[0-9\]     条件限制在小写0 to
9范围中一个字符] 

</div>

<div>

[\[0-9a-z\] 条件限制在小写0 to 9或a to
z范围中一个字符] 

</div>

<div>

[\[0-9\[a-z\]\] 条件限制在小写0 to 9或a to
z范围中一个字符(交集)] 

</div>

<div>

\

</div>

<div>

[\[\]中加入\^后加再次限制条件「\[\^\]」] 

</div>

<div>

[\[\^a-z\]     条件限制在非小写a to
z范围中一个字符] 

</div>

<div>

[\[\^A-Z\]     条件限制在非大写A to
Z范围中一个字符] 

</div>

<div>

[\[\^a-zA-Z\] 条件限制在非小写a to z或大写A to
Z范围中一个字符] 

</div>

<div>

[\[\^0-9\]     条件限制在非小写0 to
9范围中一个字符] 

</div>

<div>

[\[\^0-9a-z\] 条件限制在非小写0 to 9或a to
z范围中一个字符] 

</div>

<div>

[\[\^0-9\[a-z\]\] 条件限制在非小写0 to 9或a to
z范围中一个字符(交集)] 

</div>

<div>

\

</div>

<div>

[在限制条件为特定字符出现0次以上时，可以使用「\*」] 

</div>

<div>

[J\*    
0个以上J] 

</div>

<div>

[.\*    
0个以上任意字符] 

</div>

<div>

[J.\*D    
J与D之间0个以上任意字符] 

</div>

<div>

\

</div>

<div>

[在限制条件为特定字符出现1次以上时，可以使用「+」] 

</div>

<div>

[J+    
1个以上J] 

</div>

<div>

[.+    
1个以上任意字符] 

</div>

<div>

[J.+D    
J与D之间1个以上任意字符] 

</div>

<div>

\

</div>

<div>

[在限制条件为特定字符出现有0或1次以上时，可以使用「?」] 

</div>

<div>

[JA?    
J或者JA出现] 

</div>

<div>

\

</div>

<div>

[限制为连续出现指定次数字符「 

</div>

<div>

[J{2}    
JJ] 

</div>

<div>

[J{3}    
JJJ] 

</div>

<div>

[文字a个以上，并且「 

</div>

<div>

[J{3,}    
JJJ,JJJJ,JJJJJ,???(3次以上J并存)] 

</div>

<div>

[文字个以上，b个以下「 

</div>

<div>

[J{3,5}    
JJJ或JJJJ或JJJJJ] 

</div>

<div>

[两者取一「\|」] 

</div>

<div>

[J\|A    
J或A] 

</div>

<div>

[Java\|Hello    
Java或Hello] 

</div>

<div>

[ ] 

</div>

<div>

[「()」中规定一个组合类型] 

</div>

<div>

[比如，我查询\<a href=/\"index.html/\"\>index\</a\>中\<a
href\>\</a\>间的数据，可写作\<a.\*href=/\".\*/\"\>(.+?)\</a\>] 

</div>

<div>

\

</div>

<div>

[在使用Pattern.compile函数时，可以加入控制正则表达式的匹配行为的参数：] 

</div>

<div>

[Pattern Pattern.compile(String regex, int
flag)] 

</div>

<div>

\

</div>

<div>

[flag的取值范围如下：] 

</div>

<div>

[Pattern.CANON_EQ     当且仅当两个字符的\"正规分解(canonical
decomposition)\"都完全相同的情况下，才认定匹配。比如用了这个标志之后，表达式\"a/u030A\"会匹配\"?\"。默认情况下，不考虑\"规
范相等性(canonical
equivalence)\"。] 

</div>

<div>

[Pattern.CASE_INSENSITIVE(?i)    
默认情况下，大小写不明感的匹配只适用于US-ASCII字符集。这个标志能让表达式忽略大小写进行匹配。要想对Unicode字符进行大小不明感的匹
配，只要将UNICODE_CASE与这个标志合起来就行了。] 

</div>

<div>

[Pattern.COMMENTS(?x)    
在这种模式下，匹配时会忽略(正则表达式里的)空格字符(译者注：不是指表达式里的\"//s\"，而是指表达式里的空格，tab，回车之类)。注释从#开始，一直到这行结束。可以通过嵌入式的标志来启用Unix行模式。] 

</div>

<div>

[Pattern.DOTALL(?s)    
在这种模式下，表达式\'.\'可以匹配任意字符，包括表示一行的结束符。默认情况下，表达式\'.\'不匹配行的结束符。] 

</div>

<div>

[Pattern.MULTILINE] 

</div>

<div>

[(?m)    
在这种模式下，\'\^\'和\'\$\'分别匹配一行的开始和结束。此外，\'\^\'仍然匹配字符串的开始，\'\$\'也匹配字符串的结束。默认情况下，这两个表达式仅仅匹配字符串的开始和结束。] 

</div>

<div>

[Pattern.UNICODE_CASE] 

</div>

<div>

[(?u)    
在这个模式下，如果你还启用了CASE_INSENSITIVE标志，那么它会对Unicode字符进行大小写不明感的匹配。默认情况下，大小写不敏感的匹配只适用于US-ASCII字符集。] 

</div>

<div>

[Pattern.UNIX_LINES(?d)    
在这个模式下，只有\'/n\'才被认作一行的中止，并且与\'.\'，\'\^\'，以及\'\$\'进行匹配。] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[抛开空泛的概念，下面写出几个简单的Java正则用例：] 

</div>

<div>

\

</div>

<div>

[◆比如，在字符串包含验证时] 

</div>

<div>

\

</div>

<div>

[//查找以Java开头,任意结尾的字符串] 

</div>

<div>

[  Pattern pattern =
Pattern.compile(\"\^Java.\*\");] 

</div>

<div>

[  Matcher matcher =
pattern.matcher(\"Java不是人\");] 

</div>

<div>

[  boolean b=
matcher.matches();] 

</div>

<div>

[ 
//当条件满足时，将返回true，否则返回false] 

</div>

<div>

[ 
System.out.println(b);] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[◆以多条件分割字符串时] 

</div>

<div>

[Pattern pattern = Pattern.compile(\"\[,
\|\]+\");] 

</div>

<div>

[String\[\] strs = pattern.split(\"Java Hello World 
Java,Hello,,World\|Sun\");] 

</div>

<div>

[for (int i=0;i\<strs.length;i++)
 

</div>

<div>

[   
System.out.println(strs\[i\]);] 

</div>

<div>

[} ] 

</div>

<div>

\

</div>

<div>

[◆文字替换（首次出现字符）] 

</div>

<div>

[Pattern pattern =
Pattern.compile(\"正则表达式\");] 

</div>

<div>

[Matcher matcher = pattern.matcher(\"正则表达式 Hello World,正则表达式
Hello
World\");] 

</div>

<div>

[//替换第一个符合正则的数据] 

</div>

<div>

[System.out.println(matcher.replaceFirst(\"Java\"));] 

</div>

<div>

\

</div>

<div>

[◆文字替换（全部）] 

</div>

<div>

[Pattern pattern =
Pattern.compile(\"正则表达式\");] 

</div>

<div>

[Matcher matcher = pattern.matcher(\"正则表达式 Hello World,正则表达式
Hello
World\");] 

</div>

<div>

[//替换第一个符合正则的数据] 

</div>

<div>

[System.out.println(matcher.replaceAll(\"Java\"));] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[◆文字替换（置换字符）] 

</div>

<div>

[Pattern pattern =
Pattern.compile(\"正则表达式\");] 

</div>

<div>

[Matcher matcher = pattern.matcher(\"正则表达式 Hello World,正则表达式
Hello World
\");] 

</div>

<div>

[StringBuffer sbr = new
StringBuffer();] 

</div>

<div>

[while (matcher.find())
 

</div>

<div>

[    matcher.appendReplacement(sbr,
\"Java\");] 

</div>

<div>

[}] 

</div>

<div>

[matcher.appendTail(sbr);] 

</div>

<div>

[System.out.println(sbr.toString());] 

</div>

<div>

\

</div>

<div>

[◆验证是否为邮箱地址] 

</div>

<div>

\

</div>

<div>

[String
<str=%22ceponline@yahoo.com.cn>\";] 

</div>

<div>

[Pattern pattern =
Pattern.compile(\"\[//w//.//-\]+@(\[//w//-\]+//.)+\[//w//-\]+\",Pattern.CASE_INSENSITIVE);] 

</div>

<div>

[Matcher matcher =
pattern.matcher(str);] 

</div>

<div>

[System.out.println(matcher.matches());] 

</div>

<div>

\

</div>

<div>

[◆去除html标记] 

</div>

<div>

[Pattern pattern = Pattern.compile(\"\<.+?\>\",
Pattern.DOTALL);] 

</div>

<div>

[Matcher matcher = pattern.matcher(\"\<a
href=/\"index.html/\"\>主页\</a\>\");] 

</div>

<div>

[String string =
matcher.replaceAll(\"\");] 

</div>

<div>

[System.out.println(string);] 

</div>

<div>

\

</div>

<div>

[◆查找html中对应条件字符串] 

</div>

<div>

[Pattern pattern =
Pattern.compile(\"href=/\"(.+?)/\"\");] 

</div>

<div>

[Matcher matcher = pattern.matcher(\"\<a
href=/\"index.html/\"\>主页\</a\>\");] 

</div>

<div>

[if(matcher.find())] 

</div>

<div>

[ 
System.out.println(matcher.group(1));] 

</div>

<div>

[}] 

</div>

<div>

\

</div>

<div>

[◆截取http://地址] 

</div>

<div>

[//截取url] 

</div>

<div>

[Pattern pattern =
Pattern.compile(\"(http://\|https://) 

</div>

<div>

[Matcher matcher =
pattern.matcher(\"dsdsds\<<http://dsds//gfgffdfd>\>fdf\");] 

</div>

<div>

[StringBuffer buffer = new
StringBuffer();] 

</div>

<div>

[while(matcher.find()) 

</div>

<div>

[   
buffer.append(matcher.group());        ] 

</div>

<div>

[   
buffer.append(\"/r/n\");              ] 

</div>

<div>

[System.out.println(buffer.toString());] 

</div>

<div>

[}] 

</div>

<div>

[        ] 

</div>

<div>

[◆替换指定 

</div>

<div>

\

</div>

<div>

[String str =
\"Java目前的发展史是由 

</div>

<div>

[String\[\]\[\] object= ,new
String\[\] 

</div>

<div>

[System.out.println(replace(str,object));] 

</div>

<div>

\

</div>

<div>

[public static String replace(final String sourceString,Object\[\]
object)
 

</div>

<div>

[            String
temp=sourceString;    ] 

</div>

<div>

[            for(int
i=0;i\<object.length;i++) 

</div>

<div>

[                      String\[\]
result=(String\[\])object\[i\];] 

</div>

<div>

[               Pattern    pattern =
Pattern.compile(result\[0\]);] 

</div>

<div>

[               Matcher matcher =
pattern.matcher(temp);] 

</div>

<div>

[              
temp=matcher.replaceAll(result\[1\]);] 

</div>

<div>

[           
}] 

</div>

<div>

[            return
temp;] 

</div>

<div>

[}] 

</div>

<div>

\

</div>

<div>

\

</div>

<div>

[◆以正则条件查询指定目录下文件] 

</div>

<div>

\

</div>

<div>

[ //用于缓存文件列表] 

</div>

<div>

[        private ArrayList files = new
ArrayList();] 

</div>

<div>

[       
//用于承载文件路径] 

</div>

<div>

[        private String
\_path;] 

</div>

<div>

[       
//用于承载未合并的正则公式] 

</div>

<div>

[        private String
\_regexp;] 

</div>

<div>

[        ] 

</div>

<div>

[        class MyFileFilter implements FileFilter
 

</div>

<div>

\

</div>

<div>

[             
/\*\*] 

</div>

<div>

[               \*
匹配文件名称] 

</div>

<div>

[              
\*/] 

</div>

<div>

[              public boolean accept(File file)
 

</div>

<div>

[                try
 

</div>

<div>

[                  Pattern pattern =
Pattern.compile(\_regexp);] 

</div>

<div>

[                  Matcher match =
pattern.matcher(file.getName());                ] 

</div>

<div>

[                  return
match.matches();] 

</div>

<div>

[                } catch (Exception e)
 

</div>

<div>

[                  return
true;] 

</div>

<div>

[               
}] 

</div>

<div>

[             
}] 

</div>

<div>

[           
}] 

</div>

<div>

[        ] 

</div>

<div>

[       
/\*\*] 

</div>

<div>

[         \*
解析输入流] 

</div>

<div>

[         \* \@param
inputs] 

</div>

<div>

[         \*/] 

</div>

<div>

[        FilesAnalyze (String path,String
regexp) 

</div>

<div>

[           
getFileName(path,regexp);] 

</div>

<div>

[       
}] 

</div>

<div>

[        ] 

</div>

<div>

[       
/\*\*] 

</div>

<div>

[         \*
分析文件名并加入files] 

</div>

<div>

[         \* \@param
input] 

</div>

<div>

[         \*/] 

</div>

<div>

[        private void getFileName(String path,String regexp)
 

</div>

<div>

[           
//目录] 

</div>

<div>

[             
\_path=path;] 

</div>

<div>

[             
\_regexp=regexp;] 

</div>

<div>

[              File directory = new
File(\_path);] 

</div>

<div>

[              File\[\] filesFile = directory.listFiles(new
MyFileFilter());] 

</div>

<div>

[              if (filesFile == null)
return;] 

</div>

<div>

[              for (int j = 0; j \< filesFile.length; j++)
 

</div>

<div>

[               
files.add(filesFile\[j\]);] 

</div>

<div>

[             
}] 

</div>

<div>

[             
return;] 

</div>

<div>

[           
}] 

</div>

<div>

[    ] 

</div>

<div>

[       
/\*\*] 

</div>

<div>

[         \*
显示输出信息] 

</div>

<div>

[         \* \@param
out] 

</div>

<div>

[        
\*/] 

</div>

<div>

[        public void print (PrintStream out)
 

</div>

<div>

[            Iterator elements =
files.iterator();] 

</div>

<div>

[            while (elements.hasNext())
 

</div>

<div>

[                File file=(File)
elements.next();] 

</div>

<div>

[                   
out.println(file.getPath());    ] 

</div>

<div>

[           
}] 

</div>

<div>

[       
}] 

</div>

<div>

\

</div>

<div>

[        public static void output(String path,String regexp)
 

</div>

<div>

\

</div>

<div>

[            FilesAnalyze fileGroup1 = new
FilesAnalyze(path,regexp);] 

</div>

<div>

[           
fileGroup1.print(System.out);] 

</div>

<div>

[       
}] 

</div>

<div>

[    ] 

</div>

<div>

[        public static void main (String\[\] args)
 

</div>

<div>

[           
output(\"C://\",\"\[A-z\|.\]\*\");] 

</div>

</div>

<div>

[       
}] 

</div>
