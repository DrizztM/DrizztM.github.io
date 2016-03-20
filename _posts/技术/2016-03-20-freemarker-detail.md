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
如果你的变量名包含一些符号时，比如 foo-bar，你编写${foo-bar},FreeMarker 可能会认为是从 foo 中减去 bar,这时可以使用${. vars["foo-bar"]}<br>

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
1.include指令<br>
<#include filename><br>
<#include filename options> <br>
说明:options包括parse 和encoding <br>
    parse ：为true时，被包含的文件作用FTL模板处理,否则作为普通文本处理，默认为true。<br>
    encoding ：编码<br>
<#include "*/footer.ftl"><br>
       *代表本目录或任一上级目录。<br>
       如有"/foo/bar/template.ftl"，则上例按如下顺序查找/foo/bar/footer.ftl 、      /foo/footer.ftl、 /footer.ftl 。如有<#include "*/commons/footer.ftl"> ，会   按/foo/bar/commons/footer.ftl、/foo/commons/footer.ftl 、/commons/footer.ftl顺序查找。<br>

2.import指令<br>
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


3.noparse指令

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
