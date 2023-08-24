---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-06-02 17:15:09 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 语言基础\--反射 浅析
updated: "2019-06-17 15:55:35 +0000"
---

:::  
#### 前言  

反射是众多编程语言中的一个非常实用的功能，毫不意外go
也是对于反射提供了友好的支持，反射官方描述是一种能够自描述、自控制的应用。go
中的反射就是在运行时动态调用实例的方法和属性，并且在reflect包中对于反射做了集中的实现。常见反射场景比如说rpc调用，Java是基于反射实现的，go也是如此。

##### go反射基础  

go的反射是建立在类型基础上的，go中的类型是静态的（在创建变量的时候就已经明确了），可以粗暴的认为go的反射只跟接口类型相关，只有接口才能正常使用反射。\
一个interface都由一个pair：`(value,type)` 组成（一个interface变量包含两个指针，一个指向value、一个指向type），而反射就是在上述基础上用来检测存储在接口变量内部pair以构建运行时行为的一种机制，具体到代码来说是这样的：

```  
// 检查参数i的值，如果接口为空的话就返回0
func ValueOf(i interface{}) Value
// 检查参数i的类型，如果接口为空的话返回nil
func TypeOf(i interface{}) Type
```

go中就是依赖于上述两个函数完成的pair的检查和操作，有兴趣可以自己定义一个变量试验一下，这里举一个简单demo：

```  
package main
import (
    "reflect"
    "fmt"
)
func main() {
    var demo interface {}
    demo = "2333"
    value := reflect.ValueOf(demo)
    demoType := reflect.TypeOf(demo)
    fmt.Println(value)
    fmt.Println(demoType)
    fmt.Println(value.Interface().(string))
    // 下一行会panic，这也是使用interface转型的时候非常注意的一点，go对类型控制的非常严格。
    fmt.Println(value.Interface().(int)) 
}
```

结果:\
![](Go%20%E8%AF%AD%E8%A8%80%E5%9F%BA%E7%A1%80--%E5%8F%8D%E5%B0%84%20%E6%B5%85%E6%9E%90.resources/550DEB95-ACE0-477A-B26F-45FAB50A6B79.png) 
 \
像上面这种valueof、typeof都是为了后续对于运行时变量值修改或函数调用做准备的。

##### go设置变量  

我们可以通过`reflect.Value` \
看一下demo：

```  
var num float32 = 1.1
fmt.Println(num)
pointer := reflect.ValueOf(&num)// 参数是指针才可以
newValue := pointer.Elem()
newValue.SetFloat(1.2)
fmt.Println(num)
```

上面代码的执行过程是这样的：\
1、先传入变量的指针\
2、然后获取value的指针\
3、使用Elem方法获取原始值对应的反射对象（如果第一步传入的是一个普通变量，这一步会直接panic）\
4、最后设置值

##### go反射调用函数  

除了设置变量，反射更重要的一点是函数调用，在工程框架中随时都有扩展方法的需求，这就需要我们用反射的方式对于函数进行使用了。\
通常来说：\
1、通过reflect.ValueOf() 获取反射对象（reflect.Value）\
2、通过reflect.Value来根据方法名称或者来获取具体的方法对象：`reflect.Value.MethodByName` \
3、确定\[\]reflect.Value，这个是最终需要调用的方法的参数\
4、reflect.Call完成最终的函数调用\
下面来看一下demo：

```  
type Student struct {
    StuNo string
    StuName string
}
func (stu Student) Study(content string) {
    // do something
}

// 下面是如何调用
stuTmp := Student 
value := reflect.ValueOf(stuTmp)
methodValue := getValue.MethodByName("Study")
args := []reflect.Value{reflect.ValueOf("xiaoming_reflect"), reflect.ValueOf("2333_reflect")}
methodValue.Call(args) // 完成真正的调用
```

##### go反射性能  

平心而论，所有的反射性能其实都不够，Java、C#
无一幸免，其实go也是这样，反射性能是较低的，在灵活性及性能上总是要取一个折中的。\
事实上go反射性能低下与其api的设计有很大的相关性，value和type是分开的，我们处理完type之后，往往需要建立新的value的一套反射体系才能完整的使用，并且reflect.Value是一个不可复用的反射对象，每次反射都需要malloc这个reflect.Value，并且还涉及到GC。\
总体来说，慢的原因可以归结为：\
1、效率不是很高的GC策略\
2、反射实现过程中的大量for\
但是go仍然是一个年轻且亟待完善的潜力十足的语言，后续这些方面应该是会慢慢优化的，比如说你看看Java开始几个版本及现在的Java就会发现一个语言的发展过程除了易用性更大的还有性能，尤其是像Go这种十分追求性能的语言。\
关于go反射暂时就先介绍这么多
:::

 
