---
author: zouzq7@163.com
content-class: yinxiang.markdown
created: "2019-05-22 12:42:06 +0000"
exporter-version: Evernote Mac 9.6.4 (470194)
source: desktop.mac
title: Go 语言基础\--strings&strconv 浅析
updated: "2019-06-17 15:55:20 +0000"
---

:::  
string 是日常开发中最常用的一个结构，string
相关工具主要有`strings & strconv` ，本篇就来看看这个两个工具。

#### strings  

strings和Java中的StringUtils比较类似，直接看一下API吧，主要包含字符串查找、Trim、Split等

> 字符串查找
>
> > 子串substr在s中，返回true:\
> > `func Contains(s, substr string) bool  \
> > chars中任何一个Unicode字符在s中，返回true:\
> > `func ContainsAny(s, chars string) bool  \
> > Unicode字符r在s中，返回true:\
> > `func ContainsRune(s string, r rune) bool  

> 位置相关
>
> > 返回子串sep在字符串s中第一次出现的索引值，不在的话返回-1:\
> > `func Index(s, sep string) int  \
> > 返回字符c在s中第一次出现的位置:\
> > `func IndexByte(s string, c byte) int  \
> > Unicode 代码点 r 在 s 中第一次出现的位置:\
> > `func IndexRune(s string, r rune) int  \
> > chars中任何一个Unicode代码点在s中首次出现的位置，不存在返回-1:\
> > `func IndexAny(s, chars string) int  \
> > 查找字符 c 在 s 中第一次出现的位置，其中 c 满足 f(c) 返回 true:\
> > `func IndexFunc(s string, f func(rune) bool) int  \
> > 查找最后一次出现的位置:\
> > `func LastIndex(s, sep string) int  \
> > `func LastIndexByte(s string, c byte) int  \
> > `func LastIndexAny(s, chars string) int  \
> > `func LastIndexFunc(s string, f func(rune) bool) int  

> 字串匹配
>
> > 子串在s字符串中出现的次数\
> > `func Count(s, sep string) int  \
> > 重复count次str:\
> > `func Repeat(str string, count int)string  

> 大小写
>
> > 转为小写:\
> > `strings.ToLower(str string)string` \
> > 转为大写:\
> > `strings.ToUpper(str string)string` 

> Trim
>
> > 去掉字符串首尾空白字符:\
> > `strings.TrimSpace(str string)` \
> > 去掉字符串首尾空白字符\
> > `strings.Trim(str string, cut string) string` \
> > 去掉字符串首空白字符:\
> > `strings.TrimLeft(str string, cut string) string` \
> > 去掉字符串首空白字符\
> > `strings.TrimRight(str string, cut string) string` 

> 字符串切割
>
> > 返回str空格分隔的所有子串的slice:\
> > `strings.Field(str string) []string  \
> > 返回str split分隔的所有子串的slice:\
> > `strings.Split(str string, split string) []string  

> 字符串生成
>
> > 用sep把s1中的所有元素链接起来\
> > `strings.Join(s1 []string, sep string) string` \
> > 把一个整数i转成字符串:\
> > `strings.Itoa(i int) string` \
> > 把一个字符串转成整数:\
> > `strings.Atoi(str string)(int, error)` 

#### strconv  

strconv 主要提供了数据类型转换的API。主要是string
与其他类型的转换，这里主要介绍一下常用的API：

> 字符串 & int 互转
>
> > string转int：`func Atoi(str string) int \
> > int转string：`func Itoa(value int) string  

> Parse类
>
> > parse
> > 函数通常包含待转型的变量，base（进制），baseSize（多少位，比如说8位、16位、32位、64位）\
> > string转bool
> > 类型：`ParseBool()` \
> > string转float：`ParseFloat()` \
> > string转int：`ParseInt()` \
> > string转无符号int：`ParseUint()` 

> Format类
>
> > format是将其他类型转化为string类型。\
> > bool转string：`FormatBool()` \
> > float转string：`FormatFloat()` \
> > int转string：`FormatInt()` \
> > uint转string：`FormatUint()` 

> Append 类
>
> > append 函数主要是将对应的类型转化为切片并append到目标切片中。\
> > 看其中一个实现就懂了：

```  
func AppendBool(dst []byte, b bool) []byte {
    if b {
        return append(dst, "true"...)
    }
    return append(dst, "false"...)
}
```

append
一个bool转string后的结果：`AppendBool()` \
append
一个float转string后的结果：`AppendFloat()` \
append
一个int转string后的结果：`AppendInt()` \
append
一个uint转string后的结果：`AppendUint()` \
本篇要介绍的关于strings、strconv的API暂时就这么多。
:::

string%20%E6%98%AF%E6%97%A5%E5%B8%B8%E5%BC%80%E5%8F%91%E4%B8%AD%E6%9C%80%E5%B8%B8%E7%94%A8%E7%9A%84%E4%B8%80%E4%B8%AA%E7%BB%93%E6%9E%84%EF%BC%8Cstring%20%E7%9B%B8%E5%85%B3%E5%B7%A5%E5%85%B7%E4%B8%BB%E8%A6%81%E6%9C%89%60%60%60strings%20%26%20strconv%60%60%60%EF%BC%8C%E6%9C%AC%E7%AF%87%E5%B0%B1%E6%9D%A5%E7%9C%8B%E7%9C%8B%E8%BF%99%E4%B8%AA%E4%B8%A4%E4%B8%AA%E5%B7%A5%E5%85%B7%E3%80%82%0A%23%23%23%23%20strings%0Astrings%E5%92%8CJava%E4%B8%AD%E7%9A%84StringUtils%E6%AF%94%E8%BE%83%E7%B1%BB%E4%BC%BC%EF%BC%8C%E7%9B%B4%E6%8E%A5%E7%9C%8B%E4%B8%80%E4%B8%8BAPI%E5%90%A7%EF%BC%8C%E4%B8%BB%E8%A6%81%E5%8C%85%E5%90%AB%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%9F%A5%E6%89%BE%E3%80%81Trim%E3%80%81Split%E7%AD%89%0A%0A%3E%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%9F%A5%E6%89%BE%0A%3E%3E%E5%AD%90%E4%B8%B2substr%E5%9C%A8s%E4%B8%AD%EF%BC%8C%E8%BF%94%E5%9B%9Etrue%3A%0A%60%60%60func%20Contains(s%2C%20substr%20string)%20bool%20%7B%7D%60%60%60%0Achars%E4%B8%AD%E4%BB%BB%E4%BD%95%E4%B8%80%E4%B8%AAUnicode%E5%AD%97%E7%AC%A6%E5%9C%A8s%E4%B8%AD%EF%BC%8C%E8%BF%94%E5%9B%9Etrue%3A%0A%60%60%60func%20ContainsAny(s%2C%20chars%20string)%20bool%20%7B%7D%60%60%60%0AUnicode%E5%AD%97%E7%AC%A6r%E5%9C%A8s%E4%B8%AD%EF%BC%8C%E8%BF%94%E5%9B%9Etrue%3A%0A%60%60%60func%20ContainsRune(s%20string%2C%20r%20rune)%20bool%20%7B%7D%60%60%60%0A%0A%3E%E4%BD%8D%E7%BD%AE%E7%9B%B8%E5%85%B3%0A%3E%3E%E8%BF%94%E5%9B%9E%E5%AD%90%E4%B8%B2sep%E5%9C%A8%E5%AD%97%E7%AC%A6%E4%B8%B2s%E4%B8%AD%E7%AC%AC%E4%B8%80%E6%AC%A1%E5%87%BA%E7%8E%B0%E7%9A%84%E7%B4%A2%E5%BC%95%E5%80%BC%EF%BC%8C%E4%B8%8D%E5%9C%A8%E7%9A%84%E8%AF%9D%E8%BF%94%E5%9B%9E-1%3A%0A%60%60%60func%20Index(s%2C%20sep%20string)%20int%20%7B%7D%60%60%60%0A%E8%BF%94%E5%9B%9E%E5%AD%97%E7%AC%A6c%E5%9C%A8s%E4%B8%AD%E7%AC%AC%E4%B8%80%E6%AC%A1%E5%87%BA%E7%8E%B0%E7%9A%84%E4%BD%8D%E7%BD%AE%3A%0A%60%60%60func%20IndexByte(s%20string%2C%20c%20byte)%20int%20%7B%7D%20%60%60%60%0AUnicode%20%E4%BB%A3%E7%A0%81%E7%82%B9%20r%20%E5%9C%A8%20s%20%E4%B8%AD%E7%AC%AC%E4%B8%80%E6%AC%A1%E5%87%BA%E7%8E%B0%E7%9A%84%E4%BD%8D%E7%BD%AE%3A%0A%60%60%60func%20IndexRune(s%20string%2C%20r%20rune)%20int%20%7B%7D%60%60%60%0Achars%E4%B8%AD%E4%BB%BB%E4%BD%95%E4%B8%80%E4%B8%AAUnicode%E4%BB%A3%E7%A0%81%E7%82%B9%E5%9C%A8s%E4%B8%AD%E9%A6%96%E6%AC%A1%E5%87%BA%E7%8E%B0%E7%9A%84%E4%BD%8D%E7%BD%AE%EF%BC%8C%E4%B8%8D%E5%AD%98%E5%9C%A8%E8%BF%94%E5%9B%9E-1%3A%0A%60%60%60func%20IndexAny(s%2C%20chars%20string)%20int%20%7B%7D%60%60%60%0A%E6%9F%A5%E6%89%BE%E5%AD%97%E7%AC%A6%20c%20%E5%9C%A8%20s%20%E4%B8%AD%E7%AC%AC%E4%B8%80%E6%AC%A1%E5%87%BA%E7%8E%B0%E7%9A%84%E4%BD%8D%E7%BD%AE%EF%BC%8C%E5%85%B6%E4%B8%AD%20c%20%E6%BB%A1%E8%B6%B3%20f(c)%20%E8%BF%94%E5%9B%9E%20true%3A%0A%60%60%60func%20IndexFunc(s%20string%2C%20f%20func(rune)%20bool)%20int%20%7B%7D%60%60%60%0A%E6%9F%A5%E6%89%BE%E6%9C%80%E5%90%8E%E4%B8%80%E6%AC%A1%E5%87%BA%E7%8E%B0%E7%9A%84%E4%BD%8D%E7%BD%AE%3A%0A%60%60%60func%20LastIndex(s%2C%20sep%20string)%20int%20%7B%7D%60%60%60%0A%60%60%60func%20LastIndexByte(s%20string%2C%20c%20byte)%20int%20%7B%7D%60%60%60%0A%60%60%60func%20LastIndexAny(s%2C%20chars%20string)%20int%20%7B%7D%60%60%60%0A%60%60%60func%20LastIndexFunc(s%20string%2C%20f%20func(rune)%20bool)%20int%20%7B%7D%60%60%60%0A%0A%3E%E5%AD%97%E4%B8%B2%E5%8C%B9%E9%85%8D%0A%3E%3E%E5%AD%90%E4%B8%B2%E5%9C%A8s%E5%AD%97%E7%AC%A6%E4%B8%B2%E4%B8%AD%E5%87%BA%E7%8E%B0%E7%9A%84%E6%AC%A1%E6%95%B0%0A%60%60%60func%20Count(s%2C%20sep%20string)%20int%20%7B%7D%60%60%60%0A%E9%87%8D%E5%A4%8Dcount%E6%AC%A1str%3A%0A%60%60%60func%20Repeat(str%20string%2C%20count%20int)string%20%7B%7D%60%60%60%0A%0A%3E%E5%A4%A7%E5%B0%8F%E5%86%99%0A%3E%3E%E8%BD%AC%E4%B8%BA%E5%B0%8F%E5%86%99%3A%0A%60%60%60strings.ToLower(str%20string)string%60%60%60%0A%E8%BD%AC%E4%B8%BA%E5%A4%A7%E5%86%99%3A%0A%60%60%60strings.ToUpper(str%20string)string%60%60%60%0A%0A%3ETrim%0A%3E%3E%E5%8E%BB%E6%8E%89%E5%AD%97%E7%AC%A6%E4%B8%B2%E9%A6%96%E5%B0%BE%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6%3A%0A%60%60%60strings.TrimSpace(str%20string)%60%60%60%0A%E5%8E%BB%E6%8E%89%E5%AD%97%E7%AC%A6%E4%B8%B2%E9%A6%96%E5%B0%BE%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6%0A%60%60%60strings.Trim(str%20string%2C%20cut%20string)%20string%60%60%60%0A%E5%8E%BB%E6%8E%89%E5%AD%97%E7%AC%A6%E4%B8%B2%E9%A6%96%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6%3A%0A%60%60%60strings.TrimLeft(str%20string%2C%20cut%20string)%20string%60%60%60%0A%E5%8E%BB%E6%8E%89%E5%AD%97%E7%AC%A6%E4%B8%B2%E9%A6%96%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6%0A%60%60%60strings.TrimRight(str%20string%2C%20cut%20string)%20string%60%60%60%0A%0A%3E%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%88%87%E5%89%B2%0A%3E%3E%E8%BF%94%E5%9B%9Estr%E7%A9%BA%E6%A0%BC%E5%88%86%E9%9A%94%E7%9A%84%E6%89%80%E6%9C%89%E5%AD%90%E4%B8%B2%E7%9A%84slice%3A%0A%60%60%60strings.Field(str%20string)%20%5B%5Dstring%20%7B%7D%60%60%60%0A%E8%BF%94%E5%9B%9Estr%20split%E5%88%86%E9%9A%94%E7%9A%84%E6%89%80%E6%9C%89%E5%AD%90%E4%B8%B2%E7%9A%84slice%3A%0A%60%60%60strings.Split(str%20string%2C%20split%20string)%20%5B%5Dstring%20%7B%7D%60%60%60%0A%0A%3E%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%94%9F%E6%88%90%0A%3E%3E%E7%94%A8sep%E6%8A%8As1%E4%B8%AD%E7%9A%84%E6%89%80%E6%9C%89%E5%85%83%E7%B4%A0%E9%93%BE%E6%8E%A5%E8%B5%B7%E6%9D%A5%0A%60%60%60strings.Join(s1%20%5B%5Dstring%2C%20sep%20string)%20string%60%60%60%0A%E6%8A%8A%E4%B8%80%E4%B8%AA%E6%95%B4%E6%95%B0i%E8%BD%AC%E6%88%90%E5%AD%97%E7%AC%A6%E4%B8%B2%3A%0A%60%60%60strings.Itoa(i%20int)%20string%60%60%60%0A%E6%8A%8A%E4%B8%80%E4%B8%AA%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%88%90%E6%95%B4%E6%95%B0%3A%0A%60%60%60strings.Atoi(str%20string)(int%2C%20error)%20%60%60%60%0A%23%23%23%23%20strconv%0Astrconv%20%E4%B8%BB%E8%A6%81%E6%8F%90%E4%BE%9B%E4%BA%86%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2%E7%9A%84API%E3%80%82%E4%B8%BB%E8%A6%81%E6%98%AFstring%20%E4%B8%8E%E5%85%B6%E4%BB%96%E7%B1%BB%E5%9E%8B%E7%9A%84%E8%BD%AC%E6%8D%A2%EF%BC%8C%E8%BF%99%E9%87%8C%E4%B8%BB%E8%A6%81%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B%E5%B8%B8%E7%94%A8%E7%9A%84API%EF%BC%9A%0A%3E%E5%AD%97%E7%AC%A6%E4%B8%B2%20%26%20int%20%E4%BA%92%E8%BD%AC%0A%3E%3E%20string%E8%BD%ACint%EF%BC%9A%60%60%60func%20Atoi(str%20string)%20int%7B%7D%60%60%60%0A%3E%3E%20int%E8%BD%ACstring%EF%BC%9A%60%60%60func%20Itoa(value%20int)%20string%20%7B%7D%60%60%60%0A%0A%3EParse%E7%B1%BB%0A%3E%3Eparse%20%E5%87%BD%E6%95%B0%E9%80%9A%E5%B8%B8%E5%8C%85%E5%90%AB%E5%BE%85%E8%BD%AC%E5%9E%8B%E7%9A%84%E5%8F%98%E9%87%8F%EF%BC%8Cbase%EF%BC%88%E8%BF%9B%E5%88%B6%EF%BC%89%EF%BC%8CbaseSize%EF%BC%88%E5%A4%9A%E5%B0%91%E4%BD%8D%EF%BC%8C%E6%AF%94%E5%A6%82%E8%AF%B48%E4%BD%8D%E3%80%8116%E4%BD%8D%E3%80%8132%E4%BD%8D%E3%80%8164%E4%BD%8D%EF%BC%89%0Astring%E8%BD%ACbool%20%E7%B1%BB%E5%9E%8B%EF%BC%9A%60%60%60ParseBool()%60%60%60%0Astring%E8%BD%ACfloat%EF%BC%9A%60%60%60ParseFloat()%60%60%60%0Astring%E8%BD%ACint%EF%BC%9A%60%60%60ParseInt()%60%60%60%0Astring%E8%BD%AC%E6%97%A0%E7%AC%A6%E5%8F%B7int%EF%BC%9A%60%60%60ParseUint()%60%60%60%0A%0A%3EFormat%E7%B1%BB%0A%3E%3Eformat%E6%98%AF%E5%B0%86%E5%85%B6%E4%BB%96%E7%B1%BB%E5%9E%8B%E8%BD%AC%E5%8C%96%E4%B8%BAstring%E7%B1%BB%E5%9E%8B%E3%80%82%0Abool%E8%BD%ACstring%EF%BC%9A%60%60%60FormatBool()%60%60%60%0Afloat%E8%BD%ACstring%EF%BC%9A%60%60%60FormatFloat()%60%60%60%0Aint%E8%BD%ACstring%EF%BC%9A%60%60%60FormatInt()%60%60%60%0Auint%E8%BD%ACstring%EF%BC%9A%60%60%60FormatUint()%60%60%60%0A%0A%3EAppend%20%E7%B1%BB%0A%3E%3Eappend%20%E5%87%BD%E6%95%B0%E4%B8%BB%E8%A6%81%E6%98%AF%E5%B0%86%E5%AF%B9%E5%BA%94%E7%9A%84%E7%B1%BB%E5%9E%8B%E8%BD%AC%E5%8C%96%E4%B8%BA%E5%88%87%E7%89%87%E5%B9%B6append%E5%88%B0%E7%9B%AE%E6%A0%87%E5%88%87%E7%89%87%E4%B8%AD%E3%80%82%0A%E7%9C%8B%E5%85%B6%E4%B8%AD%E4%B8%80%E4%B8%AA%E5%AE%9E%E7%8E%B0%E5%B0%B1%E6%87%82%E4%BA%86%EF%BC%9A%0A%60%60%60go%0Afunc%20AppendBool(dst%20%5B%5Dbyte%2C%20b%20bool)%20%5B%5Dbyte%20%7B%0A%09if%20b%20%7B%0A%09%09return%20append(dst%2C%20%22true%22\...)%0A%09%7D%0A%09return%20append(dst%2C%20%22false%22\...)%0A%7D%0A%60%60%60%0Aappend%20%E4%B8%80%E4%B8%AAbool%E8%BD%ACstring%E5%90%8E%E7%9A%84%E7%BB%93%E6%9E%9C%EF%BC%9A%60%60%60AppendBool()%60%60%60%0Aappend%20%E4%B8%80%E4%B8%AAfloat%E8%BD%ACstring%E5%90%8E%E7%9A%84%E7%BB%93%E6%9E%9C%EF%BC%9A%60%60%60AppendFloat()%60%60%60%0Aappend%20%E4%B8%80%E4%B8%AAint%E8%BD%ACstring%E5%90%8E%E7%9A%84%E7%BB%93%E6%9E%9C%EF%BC%9A%60%60%60AppendInt()%60%60%60%0Aappend%20%E4%B8%80%E4%B8%AAuint%E8%BD%ACstring%E5%90%8E%E7%9A%84%E7%BB%93%E6%9E%9C%EF%BC%9A%60%60%60AppendUint()%60%60%60%0A%E6%9C%AC%E7%AF%87%E8%A6%81%E4%BB%8B%E7%BB%8D%E7%9A%84%E5%85%B3%E4%BA%8Estrings%E3%80%81strconv%E7%9A%84API%E6%9A%82%E6%97%B6%E5%B0%B1%E8%BF%99%E4%B9%88%E5%A4%9A%E3%80%82
