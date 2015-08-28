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

## 线程安全

-Spring
 - Spring默认的注入是单例的，所以Spring存在线程安全问题。
 - 解决方案就是在spring配置文件创建注入Bean的时候，将原本的单例改成原型。
 - 旧版本Spring配置方式： Singleton=”true”【单例】→Singleton=”false”【原型】。
 - 新版本Spring配置方式：Scope=”singleton”【单例】→Scope=”prototype”【原型】。
 
- Struts
 - Struts1是单例模式，所以Struts1是线程不安全的。
 - Struts2是多例【即原型】，所以Struts2是线程安全的。
 - Struts2是多例，线程安全的，但如果我们将Struts2交给Spring管理的时候，Struts2就会默认使用Spring的线程即单例。这样就会产生线程安全为例，解决方案：在Spring配置Struts2的Bean时，将单例改成原型。

- Stringbuffer、stringbuilder
 - StringBuffer是线程安全的，它是单线程的，但因为StringBuffer类内部使用了synchronized，所以StringBuffer是线程安全的。
 - StringBuilder是线程不安全的。【它在多线程环境下是不安全的，虽然它和StringBuffer很多相似地方，但它没有synchronized】,我们应该尽量避免在多线程环境下使用StringBuilder，但不得不说它的效率的确是最快的。

- HashMap、HashTable
 - Map是一个以键值对存储的接口。Map下有两个具体的实现，分别是HashMap和HashTable。
 - HashMap是线程非安全的，HashTable是线程安全的，所以HashMap的效率高于HashTable。
 - HashMap允许键或值为空，而HashTable不允许键或值为空。
 - HashMap性能优于Hashtable。

- ArrayList、Vector
 - List接口实现类有ArrayList,LinkedList,Vector。ArrayList和Vector是基于数组实现的,所以查询的时候速度快，而在进行增加和删除的时候速度较慢LinkedList是基于链式存储结构，所以在进行查询的时候速度较慢但在进行增加和删除的时候速度较快。又因为Vector是线程安全的，所以他和ArrayList相比而言，查询效率要低。
 - ArrayList是线程非安全的，因为没有同步方法。
 - LinkedList,也是线程不安全的，解决方法参照ArrayList。
 - 如何将ArrayList变成线程安全的：
  - 1.使用synchronized关键字，这个大家应该都很熟悉了；
  - 2.List list = Collections.synchronizedList(new ArrayList());

