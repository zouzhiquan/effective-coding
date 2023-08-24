---
altitude: 156.3638153076172
author: zouzq7@163.com
created: "2016-10-19 10:15:15 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
latitude: 36.19464292524135
longitude: 117.1118744255002
source: desktop.mac
title: java反射机制
updated: "2016-10-19 10:51:52 +0000"
---

<div>

概述：检测和修改它本身的状态或行为的一种能力；既对于任意一个类，能动态的获取参数调用对象及相应的方法。但是会破坏类的完整性。

</div>

<div>

\

</div>

<div>

功能：

</div>

-   获取类的Class对象
-   获取类的Fields（申明字段：public等访问属性）
-   获取类的Method
-   获取Constructor
-   新建类的实例
-   调用private的属性、方法

<div>

\

</div>

<div>

常用方法：

</div>

<div>

getName（）／／返回该类的简要概述

</div>

<div>

newInstance（）／／根据Class对象产生相应的实例

</div>

<div>

getClassLoader（）／／获取该类的加载器

</div>

<div>

getComponentType（）／／得到数组元素所对应对象的Class对象

</div>

<div>

getSuperClass（）／／获取字累对应的Class对象

</div>

<div>

isArray（）／／判断是否为数组对象

</div>

<div>

getDeclaredMethods（）／／获取所有的方法

</div>

<div>

getReturnType（）／／获取返回类型

</div>

<div>

getParameterTypes（）／／获取方法的形参类型

</div>

<div>

getDeclaredMethod（"方法名"，参数类.Class，......）／／获取特定的方法

</div>

<div>

getSuperclass（）／／获取某类的父类

</div>

<div>

getInterfaces（）／／获取某类实现的接口

</div>

<div>

getDeclaredConstructors（）／／获取所有的构造方法

</div>

<div>

getDeclaredConstructor（参数类.Class，......）／／获取特定的构造方法

</div>

<div>

\

</div>

<div>

\

</div>

<div>

范例：

</div>

1.  [[import] 
2.  [[import] 
3.  [[import] 
4.  [[import] 
5.  [    ] 
6.  [    ] 
7.  [[/\*\*  ] 
8.  [[ \* \<a href=\"<http://lib.csdn.net/base/java>\" class=\'replace_word\' title=\"Java 知识库\" target=\'\_blank\' style=\'color:#df3434; font-weight:bold;\'\>Java \</a\>Reflection Cookbook  ] 
9.  [[ \*  ] 
10. [[ \* @author Michael Lee  ] 
11. [[ \* @since 2006-8-23  ] 
12. [[ \* @version 0.1a  ] 
13. [[ \*/] 
14. [    ] 
15. [[public] 
16. [    [/\*\*  ] 
17. [[     \* 得到某个对象的公共属性  ] 
18. [[     \*  ] 
19. [[     \* @param owner, fieldName  ] 
20. [[     \* @return 该属性对象  ] 
21. [[     \* @throws Exception  ] 
22. [[     \*  ] 
23. [[     \*/] 
24. [    [public] 
25. [        Class ownerClass = owner.getClass();     ] 
26. [    ] 
27. [        Field field = ownerClass.getField(fieldName);     ] 
28. [    ] 
29. [        Object property = field.get(owner);     ] 
30. [    ] 
31. [        [return] 
32. [    }     ] 
33. [    ] 
34. [    [/\*\*  ] 
35. [[     \* 得到某类的静态公共属性  ] 
36. [[     \*  ] 
37. [[     \* @param className   类名  ] 
38. [[     \* @param fieldName   属性名  ] 
39. [[     \* @return 该属性对象  ] 
40. [[     \* @throws Exception  ] 
41. [[     \*/] 
42. [    [public] 
43. [            [throws] 
44. [        Class ownerClass = Class.forName(className);     ] 
45. [    ] 
46. [        Field field = ownerClass.getField(fieldName);     ] 
47. [    ] 
48. [        Object property = field.get(ownerClass);     ] 
49. [    ] 
50. [        [return] 
51. [    }     ] 
52. [    ] 
53. [    ] 
54. [    [/\*\*  ] 
55. [[     \* 执行某对象方法  ] 
56. [[     \*  ] 
57. [[     \* @param owner  ] 
58. [[     \*            对象  ] 
59. [[     \* @param methodName  ] 
60. [[     \*            方法名  ] 
61. [[     \* @param args  ] 
62. [[     \*            参数  ] 
63. [[     \* @return 方法返回值  ] 
64. [[     \* @throws Exception  ] 
65. [[     \*/] 
66. [    [public] 
67. [            [throws] 
68. [    ] 
69. [        Class ownerClass = owner.getClass();     ] 
70. [    ] 
71. [        Class\[\] argsClass = [new] 
72. [    ] 
73. [        [for] 
74. [            argsClass\[i\] = args\[i\].getClass();     ] 
75. [        }     ] 
76. [    ] 
77. [        Method method = ownerClass.getMethod(methodName, argsClass);     ] 
78. [    ] 
79. [        [return] 
80. [    }     ] 
81. [    ] 
82. [    ] 
83. [      [/\*\*  ] 
84. [[     \* 执行某类的静态方法  ] 
85. [[     \*  ] 
86. [[     \* @param className  ] 
87. [[     \*            类名  ] 
88. [[     \* @param methodName  ] 
89. [[     \*            方法名  ] 
90. [[     \* @param args  ] 
91. [[     \*            参数数组  ] 
92. [[     \* @return 执行方法返回的结果  ] 
93. [[     \* @throws Exception  ] 
94. [[     \*/] 
95. [    [public] 
96. [            Object\[\] args) [throws] 
97. [        Class ownerClass = Class.forName(className);     ] 
98. [    ] 
99. [        Class\[\] argsClass = [new] 
100. [    ] 
101. [        [for] 
102. [            argsClass\[i\] = args\[i\].getClass();     ] 
103. [        }     ] 
104. [    ] 
105. [        Method method = ownerClass.getMethod(methodName, argsClass);     ] 
106. [    ] 
107. [        [return] 
108. [    }     ] 
109. [    ] 
110. [    ] 
111. [    ] 
112. [    [/\*\*  ] 
113. [[     \* 新建实例  ] 
114. [[     \*  ] 
115. [[     \* @param className  ] 
116. [[     \*            类名  ] 
117. [[     \* @param args  ] 
118. [[     \*            构造函数的参数  ] 
119. [[     \* @return 新建的实例  ] 
120. [[     \* @throws Exception  ] 
121. [[     \*/] 
122. [    [public] 
123. [        Class newoneClass = Class.forName(className);     ] 
124. [    ] 
125. [        Class\[\] argsClass = [new] 
126. [    ] 
127. [        [for] 
128. [            argsClass\[i\] = args\[i\].getClass();     ] 
129. [        }     ] 
130. [    ] 
131. [        Constructor cons = newoneClass.getConstructor(argsClass);     ] 
132. [    ] 
133. [        [return] 
134. [    ] 
135. [    }     ] 
136. [    ] 
137. [    ] 
138. [         ] 
139. [    [/\*\*  ] 
140. [[     \* 是不是某个类的实例  ] 
141. [[     \* @param obj 实例  ] 
142. [[     \* @param cls 类  ] 
143. [[     \* @return 如果 obj 是此类的实例，则返回 true  ] 
144. [[     \*/] 
145. [    [public] 
146. [        [return] 
147. [    }     ] 
148. [         ] 
149. [    [/\*\*  ] 
150. [[     \* 得到数组中的某个元素  ] 
151. [[     \* @param array 数组  ] 
152. [[     \* @param index 索引  ] 
153. [[     \* @return 返回指定数组对象中索引组件的值  ] 
154. [[     \*/] 
155. [    [public] 
156. [        [return] 
157. [    }     ] 
158. [}    ] 

<div>

\

</div>

<div>

\

</div>
