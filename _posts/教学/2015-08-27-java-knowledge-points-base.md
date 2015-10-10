---
layout: post
title: java知识点——基础篇
category: 教学
keywords: java
---

## Get和Post的区别

- get是从服务器上获取数据，post是向服务器传送数据。
- get传送的数据量较小，不能大于2KB。post传送的数据量较大，一般被默认为不受限制。
- get安全性非常低，post安全性较高。但是执行效率却比post方法好。
- 在进行文件上传时只能使用post而不能是get。
- 需要用户加入收藏的地址要用get。

## Forword(请求转发)与Redirect(重定向)

- 从数据共享上
 - forword是一个请求的延续，可以共享request的数据。
 - redirect开启一个新的请求，不可以共享request的数据。

- 从地址栏
 - forword转发地址栏不发生变化。
 - redirect转发地址栏发生变化。

## StringBuffer StringBuilder String 区别

- String:字符串常量,不可变,使用字符串拼接时是不同的2个空间。
- StringBuffer:字符串变量,可变,线程安全,字符串拼接直接在字符串后追加。
- StringBuilder:字符串变量,可变,非线程安全,字符串拼接直接在字符串后追加。
- StringBuilder执行效率高于StringBuffer高于String。
- String是一个常量，是不可变的，所以对于每一次+=赋值都会创建一个新的对象，StringBuffer和StringBuilder都是可变的，当进行字符串拼接时采用append方法，在原来的基础上进行追加，所以性能比String要高，又因为StringBuffer是线程安全的而StringBuilder是线程非安的，所以StringBuilder的效率高于StringBuffer。
- 对于大数据量的字符串的拼接，采用StringBuffer,StringBuilder。

## Hashtable与HashMap的区别
- Map是一个以键值对存储的接口。Map下有两个具体的实现，分别是HashMap和HashTable。
- HashMap是线程非安全的，HashTable是线程安全的，所以HashMap的效率高于HashTable。
- HashMap允许键或值为空，而HashTable不允许键或值为空。

## List,Set,Collection,Collections
- List和Set都是接口，他们都继承于接口Collection,List是一个有序的可重复的集合，而Set的无序的不可重复的集合。Collection是集合的顶层接口，Collections是一个封装了众多关于集合操作的静态方法的工具类,因为构造方法是私有的，所以不能实例化。
- List接口实现类有ArrayList,LinkedList,Vector。ArrayList和Vector是基于数组实现的,所以查询的时候速度快，而在进行增加和删除的时候速度较，LinkedList是基于链式存储结构，所以在进行查询的时候速度较慢但在进行增加和删除的时候速度较快。又因为Vector是线程安全的，所以他和ArrayList相比而言，查询效率要低。
