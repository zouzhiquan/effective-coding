---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-21 11:13:04 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 语言基础\--函数&作用域
updated: "2019-06-17 15:54:41 +0000"
---

:::  
#### 访问标示  

go变量通常会出现在函数内部（局部变量，只出现在函数体内，只能够在函数内部使用），函数外变量（全局变量，可以通过包访问），函数参数（形式参数）\
go的访问形式并没有一个特殊的标识符来标记，而是通过首写字母的大小来控制的。大写意味着是一个可导出的变量（可以理解为公有访问），消协意味着是一个包内私有变量（不导出的），结构体内定一个变量同理。

```  
func privateFunc() {
    // 这是一个私有的函数
}
func PublicFunc() {
    // 这是一个公有函数
}
var privateVar int // 这是一个私有变量
var PublicVar int // 这是一个公有变量
type PublicStruct struct {
    var privateVar int // 这是一个结构体内私有变量
    var PublicVar int // 这是一个结构体内公有变量
    privateFunc(a int) // 这是一个结构体私有的函数
    PublicFunc() string// 这是一个结构体公有的函数
}
```

#### 函数组成  

通常来说函数有这么几个部分组成，函数标示、函数名、出参、入参、函数体、函数名。\
`func` :
函数标示\
`getNameById` :
函数名\
`id int64` :
函数入参\
`string` :
函数出参\
` :
函数体

```  
func getNameById(id int64) string {···}
```

##### 值传递&引用传递&指针传递  

值传递：调用函数时将实际的参数copy一份到函数中\
引用传递：调用函数时将实际参数的地址传递到函数中\
指针传递：将实际变量的指针传递到函数中

值传递&引用传递这些都是各种语言中经常讨论的一个话题，比如说Java
中就是完全按值传递，基础类型时copy一份传递到函数中，在函数内部对这个值进行更新毫无作用，传递一个对象的时候copy一份引用的值传递到函数内部，这也就是为什么
对这个引用值进行操作毫无作用，但是对于这个引用值指向的对象进行更新就有效。

```  
public static void main(string []args) {
    Student student = new Student();
    int tempInt = 2;
    update(student, tempInt);
}
public static void update(Student stu, int value) {
    stu.name = "23333"; // 更新有效
    stu = new Student(); // 更新无效
    value = 1; // 更新无效
}
```

go中和Java是类似的，完全遵循按值传递。\
对于基础变量，就是传递了一个值的拷贝\
对于map或者数组，将实际变量的地址的拷贝进行传递\
对于结构体，传递的是结构体的一份拷贝，注意一点内部的变量也是进行copy的。\
看一下demo就很清晰了：

```  
type Student struct {
    Name string
    Age int
    stuInfo map[string]string
}
var value = 10
var stu = Student }
var strs = []string  

func update(student Student, value int, strs []string)  {
    value = 1 // 更新无效
    student.Age = 1 // 更新无效
    student.stuInfo["address"] = "asdf" // 更新有效
    student = Student{} // 更新无效
    strs[1] = "0" // 更新有效
    strs = []string{} // 更新无效
}
```

但是go 相对于Java
非常灵活的是go增加了指针，我们就可以方便使用的指针进行操作了

```  
type Student struct {
    Name string
    Age int
    stuInfo map[string]string
}
var stu = Student }

func update(student *Student) {
    student.Age = 1 // 更新有效
    student.stuInfo["address"] = "asdf" // 更新有效
    student = &Student{} // 传递了一个指针值的拷贝，函数外原指针不会受到影响
}
```

go
的指针不像是C指针那么危险，会正常有空指针，但是不会发生野指针，这也算是go
指针的一个优势点吧。
:::

 
