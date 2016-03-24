---
layout: post
title: freemarker相关
category: 技术
keywords: freemarker
---

## 保留关键字

true：布尔值"true"<br>
false：布尔值"false"<br>
gt：比较运算符"大于"<br>
gte：比较运算符"大于或等于"<br>
lt：比较运算符"小于"<br>
lte：比较运算符"小于或等于"<br>
as：由少数指令使用<br>
in：由少数指令使用<br>
using：由少数指令使用<br>
如果你的变量名包含一些符号时，比如 foo-bar，你编写${foo-bar},FreeMarker 可能会认为是从 foo 中减去 bar,这时可以使用${.vars["foo-bar"]}<br>

## 特殊变量

特殊变量是由 FreeMarker 引擎自己定义的变量。要访问它们，你可以使用.variable\_name 语法。比如，你不能仅仅写 version，而必须写.version。支持的特殊变量有：<br>
data\_model：你可以使用来直接访问数据模型的哈希表。也就是，你使用global 指令定义在这里不可见的的变量。<br>
vars：表达式.vars.foo 返回和表达式 foo 相同的变量。出于某些原因你不得不使用方括号语法时这是有用的，因为它只对哈希表子变量有用，所以你需要一个人工的父哈希表。<br>
version：返回 FreeMarker 版本号的字符串形式。<br>
now ： 返 回 当 前 的 时 间 日 期 。  示 例 ： ${.now}。<br>
globals：你可以使用来访问全局可访问的变量的哈希表：数据模型和由global 指令创建的变量。注意用 assign 或 macro 创建的变量不是全局的。因此当你使用 globals 时你不能隐藏变量。<br>
locales：你可以访问本地化变量的哈希表。<br>
language：返回当前本地设置的语言部分的值。比如.locale 是 en\_US，那么.lang 是 en。<br>
locale：返回当前本地设置的值。这是一个字符串，比如 en\_US。 <br>
main：你可以用来访问主命名空间的哈希表。 <br>
namespace：你可以用来访问当前命名空间的哈希表。<br>
current\_node ： 当前节点，处理xml过程中用到。<br>
output\_encoding（从 FreeMarker 2.3.1 版本开始可用）：返回当前输出字符集的名称。<br>
current\_template\_name：当前模板的名称（从 FreeMarker 2.3.23 版本开始可用）。<br>
main\_template_name：顶级模板的名称 (从 FreeMarker 2.3.23 版本开始可用)。<br>
url\_escaping\_charset（从 FreeMarker 2.3.1 版本开始可用）： URL 转义的字符集的名称。<br>
error（从 FreeMarker 2.3.1 版本开始可用）：这个变量在 recover 指令体中可以访问，它存储了我们恢复错的错误信息。<br>

## 非空判断

检测不存在的值：使用name?? 或者(user.name)??<br>
处理不存在的值：name!"unknown" 或者(user.name)!"unknown" 或者 name! 或者 (user.name)!<br>
检测空值：使用name?has_content<br>
${user!"Anonymous"}相当于<#if user??>${user}<#else>Anonymous</#if><br>
${user!}则什么都不输出相当于<#if user??>${user}</#if><br>

## 循环变量内建函数

counter	从1开始的索引<br>
has\_next	是否还有下一项<br>
index		从0开始的索引<br>
is\_even\_item	是否是奇数项<br>
is\_first	是否是第一项<br>
is\_last	是否是最后一项与has\_next相反<br>
is\_odd\_item	是否是偶数项	<br>
item\_parity	返回字符串值 "odd" 或 "even"<br>
item\_parity\_cap	返回字符串值 "Odd" 或 "Even"<br>
item\_cycle		可以指定何值来代替 "odd" 和 "even"，允许多余两个值来循环<br>

## 常用指令

### include指令

<#include filename><br>
<#include filename options> <br>
说明:options包括parse 和encoding <br>
    parse ：为true时，被包含的文件作用FTL模板处理,否则作为普通文本处理，默认为true。<br>
    encoding ：编码<br>
<#include "\*/footer.ftl"><br>
       \*代表本目录或任一上级目录。<br>
       如有"/foo/bar/template.ftl"，则上例按如下顺序查找/foo/bar/footer.ftl 、      /foo/footer.ftl、 /footer.ftl 。如有<#include "\*/commons/footer.ftl"> ，会   按/foo/bar/commons/footer.ftl、/foo/commons/footer.ftl 、/commons/footer.ftl顺序查找。<br>

### import指令

<#import path as hash> <br>
例：<br>
mylib.ftl:<br>

```
<#macro copyright date><br>
 <p>Copyright (C) ${date} Julia Smith. All rights reserved.</p><br>
</#macro><br>
```

在另一个文件中：<br>
<#import "/libs/mylib.ftl" as my> <br>
<@my.copyright date="1999-2002"/> <br>


### noparse指令

```
<#noparse>
 <#list animals as being>
 <tr><td>${being.name}<td>${being.price} Euros
 </#list>
</#noparse>
```

输出

```
<#list animals as being>
 <tr><td>${being.name}<td>${being.price} Euros
 </#list>
```

### assign指令

```
<#assign name=value>
<#assign name1=value1 name2=value2 ... nameN=valueN> 
<#assign name=value in namespacehash>
<#assign name> capture this </#assign>
<#assign name in namespacehash> capture this</#assign>
```

在当前或指定命名空间内定义或替换已存在的顶级变量。如：some\_hash.subvar不行，而 some\_hash则可以。 Assign指令中包含的内容不会被输出，而是被存入指定的变量中。<br>
<#assign var="你好"><br>
${var}<br>
输出：你好。

### global指令

```
<#global name=value>
<#global name1=value1 name2=value2 ... nameN=valueN> 
<#global name> capture this </#global>
```

在所有命名空间内定义或替换已存在的顶级变量。规则与assign一致。

### local 指令

```
<#local name=value> 
<#local name1=value1 name2=value2 ... nameN=valueN> 
<#local name> capture this</#local>
```

规则与assign一致，只在宏和方法的内部定义才会有作用。

## springmvc 配置参数

### locale

作用<br>
设置默认地区，主要影响数字、日期输出格式，request中没有指定地区时模板查找值<br>
例如：eh\_US，zh\_CN<br>

### classic\_compatible

作用<br>
兼容旧版freemarker习惯，不建议使用，会导致一些问题，比如include必须用绝对路径值<br>
"true", "false" 或者 "yes", "no" 或者 "t", "f" 或者  "y", "n"<br>

### template\_exception\_handler

作用<br>
	模板异常处理器<br>
值<br>
rethrow，debug，html_debug，ignore<br>
也可以是类名<br>
默认是debug<br>

### arithmetic\_engine

作用<br>
数学运算引擎<br>
值<br>
bigdecimal,conservative<br>
默认值bigdecimal<br>

### object\_wrapper

作用<br>
对象包装器<br>
值<br>
default, default_2_3_0，simple，beans，jython<br>

### number\_format

作用<br>
	设置默认数字输出格式<br>
值<br>
	number，currency，percent，computer<br>
也可以 DecimalFormat 类型(比如"0.##")<br>
具体使用参考 <br>
http://docs.oracle.com/javase/8/docs/api/java/text/DecimalFormat.html?is-external=true

### boolean\_format

作用<br>
	设置默认布尔值输出格式<br>
值<br>
	true时输出的值和false时输出的值使用英文逗号分隔<br>
	比如true,false或者 是,否<br>
	
### date\_format,time\_format,datetime\_format

作用<br>
	设置默认时间、日期输出格式<br>
值<br>
	short, medium, long, full<br>
	也可以是表达式 比如yyyy-MM-dd hh:mm:ss a或者yyyy-MM-dd HH:mm:ss<br>
	
	
### time\_zone

作用<br>
	设置时区<br>
值<br>
	GMT+8:00，Asia/Shanghai<br>
默认为系统时区和设置的locale无关<br>
 
### sql\_date\_and\_time\_time\_zone

作用<br>
	设置java.sql.Date的时区<br>
值<br>
	GMT+8:00，Asia/Shanghai<br>
	
### output\_encoding

作用<br>
	设置FreeMarker输出的字符集<br>
值<br>
	utf-8，gb2312<br>
	
### url\_escaping\_charset

作用<br>
	设置URL转义字符集<br>
值<br>
	UTF-8，GB2312,ISO-8859-1<br>
	默认为output_encoding中的值<br>
	
### auto\_flush

作用<br>
	设置模板处理后自动刷新<br>
值<br>
	true,false<br>
	默认值true<br>

### new\_builtin\_class\_resolver

作用<br>
	设置?new内建函数使用范围?new()<br>
值<br>
	unrestricted，safer，allows\_nothing<br>
	如果值包含冒号则使用OptInTemplateClassResolver.此时可使用allowed\_classes:和trusted\_templates:<br>
	默认是safer<br>
	如果值包含点则当作类名使用<br>
	
### show\_error\_tips

作用<br>
	控制是否显示错误提示<br>
值<br>
	true,false<br>
	默认值true<br>
	
### api\_builtin\_enabled

作用<br>
	控制?api内建函数是否可用<br>
值<br>
	true,false<br>
	默认值false <br>
	
### auto\_import

作用<br>
	在所有模板中自动引入模板<br>
值<br>
规范：模板文件 as 命名空间<br>
多个引入用逗号隔开<br>
/lib/form.ftl as f, /lib/widget as w, "/lib/odd name.ftl" as odd<br>

### auto\_include

作用<br>
	在所有模板中自动包含模板<br>
值<br>
规范：模板文件<br>
多个引入用逗号隔开<br>
/include/common.ftl, "/include/evil name.ftl"<br>

### default\_encoding

作用<br>
	设置默认编码，如果不设置，可能会导致切换到不同环境时产生乱码问题<br>
值<br>
	UTF-8,ISO-8859-1,GB2312<br>
	默认值为操作系统的编码<br>

### localized\_lookup

作用<br>
	控制模板本地化查找是否可用，比如locale设置为zh\_CN加载模板foo.ftl时，freemarker自动查找foo\_zh\_CN.ftl,foo\_zh.ftl,foo.ftl并返回找到的第一个<br>
值<br>
	true,false<br>
	默认值为true<br>
	
### whitespace\_stripping

作用<br>
	控制是否删除多余空格<br>
值<br>
	true,false<br>
	默认值为true<br>
	
### template\_update\_delay

作用<br>
	设置模板缓存时间，没有设置单位时，单位是秒<br>
值<br>
	正整数，或者带单位的正整数（单位可以是s,ms,m,h;分别表示秒，毫秒，分钟，小时）<br>
	默认值为5秒<br>

### tag\_syntax

作用<br>
	设置标签风格<#tag> 或者[#tag]<br>
值<br>
	auto\_detect，angle\_bracket，square\_bracket<br>
默认值为angle\_bracket<br>

### template\_loader

作用<br>
	设置自定义的模板加载器<br>
值<br>
	default,类名<br>
	默认值 default<br>

## 宏定义

格式：<br>
<#macro name param1 param2 ... paramN><br>
 ...<br>
</#macro><br>
name：宏变量的名称。 <br>
param1，param2 等： 局部变量的名称，存储参数的值，在= 号后面的默认值（表达式）可选。默认值也可以是另外一个参数，比如：<#macro section title label=title>。<br>

例子：<br>
<#macro test><br>
 Test text<br>
</#macro><br>

调用方式：<br>
<@test/><br>

输出：<br>
Test text<br>

<#macro test foo bar baaz><br>
 Test text, and the params: ${foo}, ${bar}, ${baaz}<br>
</#macro><br>

调用方式：<br>
<@test "a" "b" 5*5-2/><br>
<@test bar="b" foo="a" baaz=5*5-2/><br>

输出都是：<br>
Test text, and the params: a, b, 23

nested 指令执行自定义指令开始和结束标签中间的模板片段。<br>
<#macro do\_twice><br>
 1. <#nested><br>
 2. <#nested><br>
</#macro><br>

调用方式：<br>
<@do\_twice>something</@do\_twice>

输出：<br>
 1. something<br>
 2. something


