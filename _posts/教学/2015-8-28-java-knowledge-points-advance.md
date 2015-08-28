---
layout: post
title: java知识点——进阶篇
category: 教学
keywords: java
---

## JVM的相关概念

- 生命周期：加载、连接、初始化，使用，卸载。
<img src="/assets/img/content1.png" style="width:80%">
- 对象基本上都是在jvm的堆区中创建，在创建对象之前，会触发类加载（加载、连接、初始化），其中链接又可分为校验（Verify），备（Prepare），解析（Resolve）三步。
- 当类初始化完成后，利用ClassLoader来装载，通过指定的className，找到二进制码，生成Class实例，放到JVM中。
- 当对象使用完之后会在合适的时候被jvm垃圾收集器回收。

