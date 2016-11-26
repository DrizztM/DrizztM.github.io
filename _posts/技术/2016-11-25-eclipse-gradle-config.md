---
layout: post
title: eclipse集成gradle
category: 技术
keywords: IDE
---

建议全程翻墙

## 下载gradle
需翻墙：<br>
http://services.gradle.org/distributions

## 设置环境变量
```
GRADLE_HOME
path添加：%GRADLE_HOME%\bin
修改下载的jar包保存位置：GRADLE_USER_HOME=%GRADLE_HOME%\.gradle
查看是否成功：cmd—>gradle -v
```

## eclipse下载插件
<img src="/assets/img/0069.jpg">


## 自己生成wrapper文件夹
进入gradle目录，执行gradle wrapper,会生成另一个gradle文件夹，下面有wrapper子文件夹，将子文件夹拷贝到.gradle目录下，并将gradle目录下的lib文件夹和bin文件夹拷贝到wrapper文件夹

## 配置eclipse的gradle插件
<img src="/assets/img/0070.jpg"><br>
<img src="/assets/img/0071.jpg">