---
altitude: 149.177978515625
author: zouzq7@163.com
created: "2016-10-29 11:04:58 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19533753611506
longitude: 117.1115454530738
source: desktop.mac
title: java 操作EXCEL
updated: "2016-11-08 13:54:40 +0000"
---

<div>

一、JSP生成简单的Excel文件

:::  
:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::

```  
package beans.excel;

import java.io.IOException;
import java.io.OutputStream;

import jxl.Workbook;
import jxl.write.Label;
import jxl.write.WritableSheet;
import jxl.write.WritableWorkbook;
import jxl.write.WriteException;

public class SimpleExcelWrite {
    public void createExcel(OutputStream os) throws WriteException,IOException{
        //创建工作薄
        WritableWorkbook workbook = Workbook.createWorkbook(os);
        //创建新的一页
        WritableSheet sheet = workbook.createSheet("First Sheet",0);
        //创建要显示的内容,创建一个单元格，第一个参数为列坐标，第二个参数为行坐标，第三个参数为内容
        Label xuexiao = new Label(0,0,"学校");
        sheet.addCell(xuexiao);
        Label zhuanye = new Label(1,0,"专业");
        sheet.addCell(zhuanye);
        Label jingzhengli = new Label(2,0,"专业竞争力");
        sheet.addCell(jingzhengli);
        
        Label qinghua = new Label(0,1,"清华大学");
        sheet.addCell(qinghua);
        Label jisuanji = new Label(1,1,"计算机专业");
        sheet.addCell(jisuanji);
        Label gao = new Label(2,1,"高");
        sheet.addCell(gao);
        
        Label beida = new Label(0,2,"北京大学");
        sheet.addCell(beida);
        Label falv = new Label(1,2,"法律专业");
        sheet.addCell(falv);
        Label zhong = new Label(2,2,"中");
        sheet.addCell(zhong);
        
        Label ligong = new Label(0,3,"北京理工大学");
        sheet.addCell(ligong);
        Label hangkong = new Label(1,3,"航空专业");
        sheet.addCell(hangkong);
        Label di = new Label(2,3,"低");
        sheet.addCell(di);
        
        //把创建的内容写入到输出流中，并关闭输出流
        workbook.write();
        workbook.close();
        os.close();
    }
    
}
```

:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::
:::

SimpleExcelWrite.jsp

:::  
:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::

```  
<%@ page language="java" import="java.util.*" pageEncoding="gb2312"%>
<%@ page import="java.io.*" %>
<%@ page import="beans.excel.*" %>
<% 
    String fname = "学校竞争力情况";
    OutputStream os = response.getOutputStream();//取得输出流
    response.reset();//清空输出流
    
    //下面是对中文文件名的处理
    response.setCharacterEncoding("UTF-8");//设置相应内容的编码格式
    fname = java.net.URLEncoder.encode(fname,"UTF-8");
    response.setHeader("Content-Disposition","attachment;filename="+new String(fname.getBytes("UTF-8"),"GBK")+".xls");
    response.setContentType("application/msexcel");//定义输出类型
    SimpleExcelWrite sw = new SimpleExcelWrite();
    sw.createExcel(os);

 %>
<html>
  <head>
    
    <title></title>

  </head>
  
  <body>
  </body>
</html>
```

:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::
:::

![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/54B366E9-420B-4F80-99D2-A8E0D5B2222A.png) 
 

 二、生成复杂数据格式Excel文件

:::  
:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::

```  
package beans.excel;

import java.io.IOException;
import java.io.OutputStream;
import java.util.Calendar;
import java.util.Date;

import jxl.Workbook;
import jxl.write.Boolean;
import jxl.write.DateFormats;
import jxl.write.DateTime;
import jxl.write.Label;
import jxl.write.Number;
import jxl.write.WritableCellFormat;
import jxl.write.WritableSheet;
import jxl.write.WritableWorkbook;
import jxl.write.WriteException;

public class ComplexDataExcelWrite {
    public void createExcel(OutputStream os) throws WriteException,IOException {
        //创建工作薄
        WritableWorkbook workbook = Workbook.createWorkbook(os);
        //创建新的一页
        WritableSheet sheet = workbook.createSheet("First Sheet", 0);
        //创建要显示的具体内容
        Label formate = new Label(0,0,"数据格式");
        sheet.addCell(formate);
        Label floats = new Label(1,0,"浮点型");
        sheet.addCell(floats);
        Label integers = new Label(2,0,"整型");
        sheet.addCell(integers);
        Label booleans = new Label(3,0,"布尔型");
        sheet.addCell(booleans);
        Label dates = new Label(4,0,"日期格式");
        sheet.addCell(dates);
        
        Label example = new Label(0,1,"数据示例");
        sheet.addCell(example);
        //浮点数据
        Number number = new Number(1,1,3.1415926535);
        sheet.addCell(number);
        //整形数据
        Number ints = new Number(2,1,15042699);
        sheet.addCell(ints);
        Boolean bools = new Boolean(3,1,true);
        sheet.addCell(bools);
        //日期型数据
        Calendar c = Calendar.getInstance();
        Date date = c.getTime();
        WritableCellFormat cf1 = new WritableCellFormat(DateFormats.FORMAT1);
        DateTime dt = new DateTime(4,1,date,cf1);
        sheet.addCell(dt);
        //把创建的内容写入到输出流中，并关闭输出流
        workbook.write();
        workbook.close();
        os.close();
        
    }
}
```

:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::
:::

![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/2466BDF1-FFA5-48A4-A64A-1DEE580E22F1.png) 
 

 三、生成复杂布局和样式的Excel文件

:::  
:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::

```  
package beans.excel;

import java.io.IOException;
import java.io.OutputStream;
import java.util.Calendar;
import java.util.Date;

import jxl.Workbook;
import jxl.format.Colour;
import jxl.format.UnderlineStyle;
import jxl.write.Boolean;
import jxl.write.DateFormats;
import jxl.write.DateTime;
import jxl.write.Label;
import jxl.write.Number;
import jxl.write.WritableCellFormat;
import jxl.write.WritableFont;
import jxl.write.WritableSheet;
import jxl.write.WritableWorkbook;
import jxl.write.WriteException;

public class MutiStyleExcelWrite {
    public void createExcel(OutputStream os) throws WriteException,IOException {
        //创建工作薄
        WritableWorkbook workbook = Workbook.createWorkbook(os);
        //创建新的一页
        WritableSheet sheet = workbook.createSheet("First Sheet", 0);
        //构造表头
        sheet.mergeCells(0, 0, 4, 0);//添加合并单元格，第一个参数是起始列，第二个参数是起始行，第三个参数是终止列，第四个参数是终止行
        WritableFont bold = new WritableFont(WritableFont.ARIAL,10,WritableFont.BOLD);//设置字体种类和黑体显示,字体为Arial,字号大小为10,采用黑体显示
        WritableCellFormat titleFormate = new WritableCellFormat(bold);//生成一个单元格样式控制对象
        titleFormate.setAlignment(jxl.format.Alignment.CENTRE);//单元格中的内容水平方向居中
        titleFormate.setVerticalAlignment(jxl.format.VerticalAlignment.CENTRE);//单元格的内容垂直方向居中
        Label title = new Label(0,0,"JExcelApi支持数据类型详细说明",titleFormate);
        sheet.setRowView(0, 600, false);//设置第一行的高度
        sheet.addCell(title);
        
        //创建要显示的具体内容
        WritableFont color = new WritableFont(WritableFont.ARIAL);//选择字体
        color.setColour(Colour.GOLD);//设置字体颜色为金黄色
        WritableCellFormat colorFormat = new WritableCellFormat(color);
        Label formate = new Label(0,1,"数据格式",colorFormat);
        sheet.addCell(formate);
        Label floats = new Label(1,1,"浮点型");
        sheet.addCell(floats);
        Label integers = new Label(2,1,"整型");
        sheet.addCell(integers);
        Label booleans = new Label(3,1,"布尔型");
        sheet.addCell(booleans);
        Label dates = new Label(4,1,"日期格式");
        sheet.addCell(dates);
        
        Label example = new Label(0,2,"数据示例",colorFormat);
        sheet.addCell(example);
        //浮点数据
        //设置下划线
        WritableFont underline= new WritableFont(WritableFont.ARIAL,WritableFont.DEFAULT_POINT_SIZE,WritableFont.NO_BOLD,false,UnderlineStyle.SINGLE);
        WritableCellFormat greyBackground = new WritableCellFormat(underline);
        greyBackground.setBackground(Colour.GRAY_25);//设置背景颜色为灰色
        Number number = new Number(1,2,3.1415926535,greyBackground);
        sheet.addCell(number);
        //整形数据
        WritableFont boldNumber = new WritableFont(WritableFont.ARIAL,10,WritableFont.BOLD);//黑体
        WritableCellFormat boldNumberFormate = new WritableCellFormat(boldNumber);
        Number ints = new Number(2,2,15042699,boldNumberFormate);
        sheet.addCell(ints);
        //布尔型数据
        Boolean bools = new Boolean(3,2,true);
        sheet.addCell(bools);
        //日期型数据
        //设置黑体和下划线
        WritableFont boldDate = new WritableFont(WritableFont.ARIAL,WritableFont.DEFAULT_POINT_SIZE,WritableFont.BOLD,false,UnderlineStyle.SINGLE);
        WritableCellFormat boldDateFormate = new WritableCellFormat(boldDate,DateFormats.FORMAT1);
        Calendar c = Calendar.getInstance();
        Date date = c.getTime();
        DateTime dt = new DateTime(4,2,date,boldDateFormate);
        sheet.addCell(dt);
        //把创建的内容写入到输出流中，并关闭输出流
        workbook.write();
        workbook.close();
        os.close();
        
    }
}
```

:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::
:::

![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/BCC53615-5BEF-41E9-B381-AD38A0BA1611.png) 
 

 四、JSP读取Excel报表

:::  
:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::

```  
<%@ page language="java" import="java.util.*" pageEncoding="gb2312"%>
<%@ page import="java.io.File" %>
<%@ page import="jxl.Cell" %>
<%@ page import="jxl.Sheet" %>
<%@ page import="jxl.Workbook" %>
<html>
  <head>
    <title></title>
  </head>
  <body>
    <font size="2">
    <%
        String fileName = "D:/学校竞争力情况.xls";
        File file = new File(fileName);//根据文件名创建一个文件对象
        Workbook wb = Workbook.getWorkbook(file);//从文件流中取得Excel工作区对象
        Sheet sheet = wb.getSheet(0);//从工作区中取得页，取得这个对象的时候既可以用名称来获得，也可以用序号。
        String outPut = "";
        
        outPut = outPut + "<b>" + fileName + "</b><br>";
        outPut = outPut + "第一个sheet的名称为：" + sheet.getName() + "<br>";
        outPut = outPut + "第一个sheet共有：" + sheet.getRows() + "行" + sheet.getColumns() + "列<br>";
        outPut = outPut + "具体内容如下：<br>";
        for(int i=0; i < sheet.getRows(); i++){
            for(int j=0; j < sheet.getColumns(); j++){
                Cell cell = sheet.getCell(j,i);
                outPut = outPut + cell.getContents() + " ";
            }
            outPut = outPut + "<br>";
        }
        out.println(outPut);
     %>
     </font>
  </body>
</html>
```

:::  
[[![](java%20%E6%93%8D%E4%BD%9CEXCEL.resources/9072370F-BEB5-4CDF-B700-419DD8738228.gif) 
 ] 
:::
:::

 

**示例1：读取本地Excel文件F:\\红楼人物.xls**

1.       新建Excel文件F:\\红楼人物.xls

内容如下：

 

2.       Java通过jexcelapi包操作excel文件：

:::  
:::  
1.  //in ExcelOperater   
2.    
3.  import java.io.File;   
4.    
5.  import java.io.FileInputStream;   
6.    
7.  import java.io.InputStream;   
8.    
9.      
10.   
11. import jxl.Cell;   
12.   
13. import jxl.CellType;   
14.   
15. import jxl.Sheet;   
16.   
17. import jxl.Workbook;   
18.   
19. import jxl.write.Label;   
20.   
21.     
22.   
23. public class ExcelOperater    
24.   
25. {   
26.   
27.     public static void main(String\[\] args)    
28.   
29.     {   
30.   
31.         jxl.Workbook readwb = null;   
32.   
33.         try    
34.   
35.         {   
36.   
37.             //构建Workbook对象, 只读Workbook对象   
38.   
39.             //直接从本地文件创建Workbook   
40.   
41.             InputStream instream = new FileInputStream(\"F:/红楼人物.xls\");   
42.   
43.             readwb = Workbook.getWorkbook(instream);   
44.   
45.     
46.   
47.             //Sheet的下标是从0开始   
48.   
49.             //获取第一张Sheet表   
50.   
51.             Sheet readsheet = readwb.getSheet(0);   
52.   
53.             //获取Sheet表中所包含的总列数   
54.   
55.             int rsColumns = readsheet.getColumns();   
56.   
57.             //获取Sheet表中所包含的总行数   
58.   
59.             int rsRows = readsheet.getRows();   
60.   
61.             //获取指定单元格的对象引用   
62.   
63.             for (int i = 0; i \< rsRows; i++)   
64.   
65.             {   
66.   
67.                 for (int j = 0; j \< rsColumns; j++)   
68.   
69.                 {   
70.   
71.                     Cell cell = readsheet.getCell(j, i);   
72.   
73.                     System.out.print(cell.getContents() + \" \");   
74.   
75.                 }   
76.   
77.                 System.out.println();   
78.   
79.             }   
80.   
81.                
82.   
83.             //利用已经创建的Excel工作薄,创建新的可写入的Excel工作薄   
84.   
85.             jxl.write.WritableWorkbook wwb = Workbook.createWorkbook(new File(   
86.   
87.                     \"F:/红楼人物1.xls\"), readwb);   
88.   
89.             //读取第一张工作表   
90.   
91.             jxl.write.WritableSheet ws = wwb.getSheet(0);   
92.   
93.             //获得第一个单元格对象   
94.   
95.             jxl.write.WritableCell wc = ws.getWritableCell(0, 0);   
96.   
97.             //判断单元格的类型, 做出相应的转化   
98.   
99.             if (wc.getType() == CellType.LABEL)    
100.   
101.             {   
102.   
103.                 Label l = (Label) wc;   
104.   
105.                 l.setString(\"新姓名\");   
106.   
107.             }   
108.   
109.             //写入Excel对象   
110.   
111.             wwb.write();   
112.   
113.             wwb.close();   
114.   
115.         } catch (Exception e) {   
116.   
117.             e.printStackTrace();   
118.   
119.         } finally {   
120.   
121.             readwb.close();   
122.   
123.         }   
124.   
125. }   
126.   
127. }   
128.   
:::
:::

3.       结果：

①     控制台输出：

人物 等级 大观园位置 金陵十二钗

林黛玉 小姐 潇湘馆 正册

妙玉 世外 栊翠庵 正册

晴雯 丫鬟 怡红院 副册

香菱 妾 蘅芜苑 又副册

②     创建文件F:\\红楼人物1.xls

 

4.       程序解析：

所引用的包：

①     Workbook对象，需要jxl.Workbook包；

②    
InputStream、FileInputStream对象：需要java.io.FileInputStream和java.io.InputStream包。

③     Sheet对象：jxl.Sheet包；注意excel中sheet表单的行列从0开始计数。

④     Cell对象：jxl.Cell包；对单元进行处理

⑤     Label：选择jxl.write.label包

⑥     WritableWorkbook、WritableSheet、WritableCelll对象

**实例二：3个功能\-\-\-\--从excel文件F:\\红楼人物.xls读取数据；生成新的excel文件F:\\红楼人物2.xls；修改原excel一个单元并输出为F:\\红楼人物3.xls。**

原始文件：F:\\红楼人物.xls

 

运行结果：

①     控制台输出：

人物 等级 大观园位置 金陵十二钗

林黛玉 小姐 潇湘馆 正册

妙玉 世外 栊翠庵 正册

晴雯 丫鬟 怡红院 副册

香菱 妾 蘅芜苑 又副册

②     写入输出Excel文件：F:\\红楼人物2.xls

 

③     修改输出文件 F:\\红楼人物3.xls （加修饰后输出）

示例程序：

:::  
:::  
1.  //in ExcelHandle   
2.    
3.  import jxl.\*;   
4.    
5.  import jxl.format.UnderlineStyle;   
6.    
7.  import jxl.write.\*;   
8.    
9.  import jxl.write.Number;   
10.   
11. import jxl.write.Boolean;   
12.   
13. import jxl.Cell;   
14.   
15.     
16.   
17. import java.io.\*;   
18.   
19.     
20.   
21. public class ExcelHandle   
22.   
23. {   
24.   
25.     public ExcelHandle()   
26.   
27.     {   
28.   
29.     }   
30.   
31.     /\*\*\*读取Excel\*/  
32.   
33.     public static void readExcel(String filePath)   
34.   
35.     {   
36.   
37.         try  
38.   
39.         {   
40.   
41.             InputStream is = new FileInputStream(filePath);   
42.   
43.             Workbook rwb = Workbook.getWorkbook(is);   
44.   
45.             //这里有两种方法获取sheet表:名字和下标（从0开始）   
46.   
47.             //Sheet st = rwb.getSheet(\"original\");   
48.   
49.             Sheet st = rwb.getSheet(0);   
50.   
51.             /\*\*  
52.  
53.             //获得第一行第一列单元的值  
54.  
55.             Cell c00 = st.getCell(0,0);  
56.  
57.             //通用的获取cell值的方式,返回字符串  
58.  
59.             String strc00 = c00.getContents();  
60.  
61.             //获得cell具体类型值的方式  
62.  
63.             if(c00.getType() == CellType.LABEL)  
64.  
65.             {  
66.  
67.                 LabelCell labelc00 = (LabelCell)c00;  
68.  
69.                 strc00 = labelc00.getString();  
70.  
71.             }  
72.  
73.             //输出  
74.  
75.             System.out.println(strc00);\*/  
76.   
77.             //Sheet的下标是从0开始   
78.   
79.             //获取第一张Sheet表   
80.   
81.             Sheet rst = rwb.getSheet(0);   
82.   
83.             //获取Sheet表中所包含的总列数   
84.   
85.             int rsColumns = rst.getColumns();   
86.   
87.             //获取Sheet表中所包含的总行数   
88.   
89.             int rsRows = rst.getRows();   
90.   
91.             //获取指定单元格的对象引用   
92.   
93.             for (int i = 0; i \< rsRows; i++)   
94.   
95.             {   
96.   
97.                 for (int j = 0; j \< rsColumns; j++)   
98.   
99.                 {   
100.   
101.                     Cell cell = rst.getCell(j, i);   
102.   
103.                     System.out.print(cell.getContents() + \" \");   
104.   
105.                 }   
106.   
107.                 System.out.println();   
108.   
109.             }             
110.   
111.             //关闭   
112.   
113.             rwb.close();   
114.   
115.         }   
116.   
117.         catch(Exception e)   
118.   
119.         {   
120.   
121.             e.printStackTrace();   
122.   
123.         }   
124.   
125.     }   
126.   
127.     /\*\*输出Excel\*/  
128.   
129.     public static void writeExcel(OutputStream os)   
130.   
131.     {   
132.   
133.         try  
134.   
135.         {   
136.   
137.    /\*\* 只能通过API提供的
     工厂方法来创建Workbook，而不能使用WritableWorkbook的构造函数，因为类WritableWorkbook的构造函数为
     protected类型：方法一：直接从目标文件中读取
     WritableWorkbook wwb = Workbook.createWorkbook(new File(targetfile));方法
     二：如下实例所示 将WritableWorkbook直接写入到输出流\*/  
138.   
139.             WritableWorkbook wwb = Workbook.createWorkbook(os);   
140.   
141.             //创建Excel工作表 指定名称和位置   
142.   
143.             WritableSheet ws = wwb.createSheet(\"Test Sheet 1\",0);   
144.   
145.             /\*\*\*\*\*\*\*\*\*\*\*\*\*\*往工作表中添加数据\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*/  
146.   
147.             //1.添加Label对象   
148.   
149.             Label label = new Label(0,0,\"测试\");   
150.   
151.             ws.addCell(label);   
152.   
153.             //添加带有字型Formatting对象   
154.   
155.             WritableFont wf = new WritableFont(WritableFont.TIMES,18,WritableFont.BOLD,true);   
156.   
157.             WritableCellFormat wcf = new WritableCellFormat(wf);   
158.   
159.             Label labelcf = new Label(1,0,\"this is a label test\",wcf);   
160.   
161.             ws.addCell(labelcf);   
162.   
163.             //添加带有字体颜色的Formatting对象   
164.   
165.             WritableFont wfc = new WritableFont(WritableFont.ARIAL,10,WritableFont.NO_BOLD,false,   
166.   
167.                     UnderlineStyle.NO_UNDERLINE,jxl.format.Colour.DARK_YELLOW);   
168.   
169.             WritableCellFormat wcfFC = new WritableCellFormat(wfc);   
170.   
171.             Label labelCF = new Label(1,0,\"Ok\",wcfFC);   
172.   
173.             ws.addCell(labelCF);   
174.   
175.               
176.   
177.             //2.添加Number对象   
178.   
179.             Number labelN = new Number(0,1,3.1415926);   
180.   
181.             ws.addCell(labelN);   
182.   
183.             //添加带有formatting的Number对象   
184.   
185.             NumberFormat nf = new NumberFormat(\"#.##\");   
186.   
187.             WritableCellFormat wcfN = new WritableCellFormat(nf);   
188.   
189.             Number labelNF = new jxl.write.Number(1,1,3.1415926,wcfN);   
190.   
191.             ws.addCell(labelNF);   
192.   
193.                
194.   
195.             //3.添加Boolean对象   
196.   
197.             Boolean labelB = new jxl.write.Boolean(0,2,true);   
198.   
199.             ws.addCell(labelB);   
200.   
201.             Boolean labelB1 = new jxl.write.Boolean(1,2,false);   
202.   
203.             ws.addCell(labelB1);             
204.   
205.             //4.添加DateTime对象   
206.   
207.             jxl.write.DateTime labelDT = new jxl.write.DateTime(0,3,new java.util.Date());   
208.   
209.             ws.addCell(labelDT);   
210.   
211.               
212.   
213.             //5.添加带有formatting的DateFormat对象   
214.   
215.             DateFormat df = new DateFormat(\"dd MM yyyy hh:mm:ss\");   
216.   
217.             WritableCellFormat wcfDF = new WritableCellFormat(df);   
218.   
219.             DateTime labelDTF = new DateTime(1,3,new java.util.Date(),wcfDF);   
220.   
221.             ws.addCell(labelDTF);   
222.   
223.             //6.添加图片对象,jxl只支持png格式图片   
224.   
225.             File image = new File(\"f:\\\\1.png\");   
226.   
227.             WritableImage wimage = new WritableImage(0,4,6,17,image);   
228.   
229.             ws.addImage(wimage);   
230.   
231.             //7.写入工作表   
232.   
233.             wwb.write();   
234.   
235.             wwb.close();   
236.   
237.         }   
238.   
239.         catch(Exception e)   
240.   
241.         {   
242.   
243.             e.printStackTrace();   
244.   
245.         }   
246.   
247.     }   
248.   
249.     /\*\* 将file1拷贝后,进行修改并创建输出对象file2  
250.  
251.      \* 单元格原有的格式化修饰不能去掉，但仍可将新的单元格修饰加上去，  
252.  
253.      \* 以使单元格的内容以不同的形式表现  
254.  
255.      \*/  
256.   
257.     public static void modifyExcel(File file1,File file2)   
258.   
259.     {   
260.   
261.         try  
262.   
263.         {   
264.   
265.             Workbook rwb = Workbook.getWorkbook(file1);   
266.   
267.             WritableWorkbook wwb = Workbook.createWorkbook(file2,rwb);//copy   
268.   
269.             WritableFont wfc = new WritableFont(WritableFont.ARIAL,10,WritableFont.NO_BOLD,false,   
270.   
271.                     UnderlineStyle.NO_UNDERLINE,jxl.format.Colour.BLUE);   
272.   
273.             WritableCellFormat wcfFC = new WritableCellFormat(wfc);    
274.   
275.             WritableSheet ws = wwb.getSheet(0);   
276.   
277.             WritableCell wc = ws.getWritableCell(0,0);   
278.   
279.             //判断单元格的类型,做出相应的转换   
280.   
281.             if(wc.getType() == CellType.LABEL)   
282.   
283.             {   
284.   
285.                 Label labelCF =new Label(0,0,\"人物（新）\",wcfFC);   
286.   
287.                 ws.addCell(labelCF);     
288.   
289.               //Label label = (Label)wc;   
290.   
291.               //label.setString(\"被修改\");   
292.   
293.             }   
294.   
295.           wwb.write();   
296.   
297.             wwb.close();   
298.   
299.             rwb.close();   
300.   
301.         }   
302.   
303.         catch(Exception e)   
304.   
305.         {   
306.   
307.             e.printStackTrace();   
308.   
309.         }   
310.   
311.     }   
312.   
313.     //测试   
314.   
315.     public static void main(String args\[\])   
316.   
317.     {   
318.   
319.         try  
320.   
321.         {   
322.   
323.             //读EXCEL   
324.   
325.         ExcelHandle.readExcel(\"F:/红楼人物.xls\");   
326.   
327.             //输出EXCEL   
328.   
329.         File filewrite=new File(\"F:/红楼人物2.xls\");   
330.   
331.         filewrite.createNewFile();   
332.   
333.         OutputStream os=new FileOutputStream(filewrite);   
334.   
335.         ExcelHandle.writeExcel(os);   
336.   
337.             //修改EXCEL   
338.   
339.         ExcelHandle.modifyExcel(new File(\"F:/红楼人物.xls\"), new File(\"F:/红楼人物3.xls\"));   
340.   
341.         }   
342.   
343.         catch(Exception e)   
344.   
345.         {   
346.   
347.         e.printStackTrace();   
348.   
349.         }   
350.   
351.     }   
352.   
353. }   
354.   
:::
:::

附：

调用流程如下:

[1.打开工作文件Workbook，在此之前先用java的io流创建或者读取文件] \
[2.打开工作表Sheet] \
[3.读行，然后读列。注意，行和列是从零开始的] \
[4.取得数据进行操作] \
[ ] 

**来自网络à读取Excel数据表**

第一步：创建Workbook（术语：工作薄）

2种方法：Workbook，就可以通过它来访问Excel Sheet（术语：工作表）：

:::  
:::  
1.  //从输入流创建Workbook读取excel数据表   
2.    
3.      InputStream is = new FileInputStream(sourcefile);   
4.    
5.      jxl.Workbook workbook = Workbook.getWorkbook(is);   
6.    
7.  //直接从本地文件（.xls）创建Workbook   
8.    
9.  Workbook workbook = Workbook.getWorkbook(new File(excelfile));   
:::
:::

一旦创建了

第二步：访问sheet。

2种方法：通过sheet的名称；或者通过下标，下标从0开始。

:::  
:::  
1.  //获取第一张Sheet表   
2.    
3.  Sheet rs = workbook.getSheet(0);   
4.    
5.  一旦得到了Sheet，就可以通过它来访问Excel Cell（术语：单元格）。   
6.    
7.  第三步：访问单元格cell   
8.    
9.  //获取第一行，第一列的值   
10.   
11. Cell c00 = rs.getCell(0, 0);   
12.   
13. String strc00 = c00.getContents();   
14.   
15. //获取第一行，第二列的值   
16.   
17. Cell c10 = rs.getCell(1, 0);   
18.   
19. String strc10 = c10.getContents();   
20.   
21. //获取第二行，第二列的值   
22.   
23. Cell c11 = rs.getCell(1, 1);   
24.   
25. String strc11 = c11.getContents();   
26.   
27.     
28.   
29. System.out.println(\"Cell(0, 0)\" + \" value : \" + strc00 + \"; type : \" + c00.getType());   
30.   
31. System.out.println(\"Cell(1, 0)\" + \" value : \" + strc10 + \"; type : \" + c10.getType());   
32.   
33. System.out.println(\"Cell(1, 1)\" + \" value : \" + strc11 + \"; type : \" + c11.getType());   
34.   
:::
:::

第四步：操作数据

如果仅仅是取得Cell的
值，我们可以方便地通过getContents()方法，它可以将任何类型的Cell值都作为一个字符串返回。如果有需要知道Cell内容的确切类型，API也提供了一系列的方法：

:::  
:::  
1.  String strc00 = null;   
2.    
3.  double strc10 = 0.00;   
4.    
5.  Date strc11 = null;   
6.    
7.  Cell c00 = rs.getCell(0, 0);   
8.    
9.  Cell c10 = rs.getCell(1, 0);   
10.   
11. Cell c11 = rs.getCell(1, 1);   
12.   
13. if(c00.getType() == CellType.LABEL)   
14.   
15. {   
16.   
17. LabelCell labelc00 = (LabelCell)c00;   
18.   
19. strc00 = labelc00.getString();   
20.   
21. }   
22.   
23. if(c10.getType() == CellType.NUMBER)   
24.   
25. {   
26.   
27. NmberCell numc10 = (NumberCell)c10;   
28.   
29. strc10 = numc10.getValue();   
30.   
31. }   
32.   
33. if(c11.getType() == CellType.DATE)   
34.   
35. {   
36.   
37. DateCell datec11 = (DateCell)c11;   
38.   
39. strc11 = datec11.getDate();   
40.   
41. }   
42.   
43.     
44.   
45. System.out.println(\"Cell(0, 0)\" + \" value : \" + strc00 + \"; type : \" + c00.getType());   
46.   
47. System.out.println(\"Cell(1, 0)\" + \" value : \" + strc10 + \"; type : \" + c10.getType());   
48.   
49. System.out.println(\"Cell(1, 1)\" + \" value : \" + strc11 + \"; type : \" + c11.getType());   
50.   
:::
:::

在得到

循环取出全部数据,并转化为相应格式：

:::  
:::  
1.  int rows = sheet.getRows();   
2.    
3.  for (int i = 1; i \< rows; i++) {   
4.    
5.      Cell cb1 = sheet.getCell(0, i);   
6.    
7.      Cell cb2 = sheet.getCell(1, i);   
8.    
9.      Cell num3 = sheet.getCell(2, i);   
10.   
11.     Cell num4 = sheet.getCell(3, i);   
12.   
13.     
14.   
15.     String user = \"\";   
16.   
17.     String rule = \"\";   
18.   
19.     int numNew = 0;   
20.   
21.     int numEdit = 0;   
22.   
23.     if (cb1.getType() == CellType.LABEL) {   
24.   
25.          LabelCell lc = (LabelCell) cb1;   
26.   
27.          user = lc.getString();   
28.   
29.     }   
30.   
31.     if (cb2.getType() == CellType.LABEL) {   
32.   
33.          LabelCell lc = (LabelCell) cb2;   
34.   
35.     rule = lc.getString();   
36.   
37.     }   
38.   
39.     if (num3.getType() == CellType.NUMBER_FORMULA) {   
40.   
41.          NumberFormulaCell nc = (NumberFormulaCell) num3;   
42.   
43.          try {   
44.   
45.              numNew = Double.valueOf(nc.getFormula()).intValue();   
46.   
47.          } catch (FormulaException e) {   
48.   
49.              e.printStackTrace();   
50.   
51.          }   
52.   
53.     }   
54.   
55.     if (num4.getType() == CellType.NUMBER_FORMULA) {   
56.   
57.          NumberFormulaCell nc = (NumberFormulaCell) num4;   
58.   
59.          try {   
60.   
61.              numEdit = Double.valueOf(nc.getFormula()).intValue();   
62.   
63.          } catch (FormulaException e) {   
64.   
65.              e.printStackTrace();   
66.   
67.          }   
68.   
69.     }   
70.   
71. }   
72.   
:::
:::

第五步：关闭对象，释放内存。

完成对Excel电子表格数据的处理后，一定要使用close()方法来关闭先前创建的对象，以释放读取数据表的过程中所占用的内存空间，在读取大量数据时显得尤为重要。

</div>

<div>

\

</div>

:::  
<div>

[Cell对象后，通过
getType()方法可以获得该单元格的类型，然后与API提供的基本类型相匹配，强制转换成相应的类型，最后调用相应的取值方法getXXX()，就可以得到确定类型的值。] 

</div>
:::
