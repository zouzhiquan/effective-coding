---
altitude: 155.0545501708984
author: zouzq7@163.com
created: "2016-09-13 11:48:56 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19496243944599
longitude: 117.1117591908852
source: desktop.mac
title: java 操作properties
updated: "2016-09-13 11:49:49 +0000"
---

<div>

```  
package com.company;

import javax.xml.parsers.ParserConfigurationException;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Properties;

public class Main {


public static void main(String[] args) throws ParserConfigurationException, IOException{
Properties prop = new Properties();//属性集合对象
FileInputStream fis = new FileInputStream("src/prop.properties");//属性文件流
prop.load(fis);//将属性文件流装载到Properties对象中
fis.close();
System.out.println("获取属性值：sitename=" + prop.getProperty("sitename"));//读

System.out.println("获取属性值：country=" + prop.getProperty("siteurl"));

// 修改sitename的属性值
prop.setProperty("sitename", "Boxcode");
// 添加一个新的属性studio
prop.setProperty("studio", "Boxcode Studio");
// 文件输出流
FileOutputStream fos = new FileOutputStream("src/prop.properties");
// 将Properties集合保存到流中
prop.store(fos, "Copyright (c) Boxcode Studio");
fos.close();// 关闭流
}
```

</div>

<div>

[[[}] 

```  
#Copyright (c) Boxcode Studio
#Tue Sep 13 19:48:28 CST 2016
sitename=Boxcode
siteurl=www.abcjava.com
```

</div>

<div>

[[[studio] [[Boxcode
Studio] 

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>

<div>

[[\
] 

</div>
