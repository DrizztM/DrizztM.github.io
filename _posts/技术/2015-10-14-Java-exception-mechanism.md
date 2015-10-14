---
layout: post
title: Java异常机制
category: 技术
keywords: java
---

最近，在产品中出现了奇怪的问题，起因是手机处理器架构不同的情况下，如果应用调用了.so文件，一定要注意在相应的处理器架构文件夹下放置同样架构的.so文件，不然会报无法被捕获的ERROR。

在这里正好对Java异常机制进行一个回顾。

## Java异常机制

Java把异常当做对象来处理，并定义一个基类java.lang.Throwable作为所有异常的超类。Java中的异常分为两大类：错误Error和异常Exception，Java异常体系结构如下图所示：

<img src="/assets/img/0049.jpg">

## Throwable

Throwable类是所有异常或错误的超类，它有两个子类：Error和Exception，分别表示错误和异常。其中异常Exception分为运行时异常(RuntimeException)和非运行时异常，也称之为不检查异常(Unchecked Exception)和检查异常(Checked Exception)。

## Error

一般是指java虚拟机相关的问题，如系统崩溃、虚拟机出错误、动态链接失败等，这种错误无法恢复或不可能捕获，将导致应用程序中断，通常应用程序无法处理这些错误，因此应用程序不应该捕获Error对象，也无须在其throws子句中声明该方法抛出任何Error或其子类。 

## 运行时异常和非运行时异常

### 运行时异常

运行时异常都是RuntimeException类及其子类异常，如NullPointerException、IndexOutOfBoundsException等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。

当出现RuntimeException的时候，我们可以不处理。当出现这样的异常时，总是由虚拟机接管。比如：我们从来没有人去处理过NullPointerException异常，它就是运行时异常，并且这种异常还是最常见的异常之一。

出现运行时异常后，系统会把异常一直往上层抛，一直遇到处理代码。如果没有处理块，到最上层，如果是多线程就由Thread.run()抛出，如果是单线程就被main()抛出。抛出之后，如果是线程，这个线程也就退出了。如果是主程序抛出的异常，那么这整个程序也就退出了。运行时异常是Exception的子类，也有一般异常的特点，是可以被Catch块处理的。只不过往往我们不对他处理罢了。也就是说，你如果不对运行时异常进行处理，那么出现运行时异常之后，要么是线程中止，要么是主程序终止。 

如果不想终止，则必须扑捉所有的运行时异常，决不让这个处理线程退出。队列里面出现异常数据了，正常的处理应该是把异常数据舍弃，然后记录日志。不应该由于异常数据而影响下面对正常数据的处理。

### 非运行时异常

非运行时异常是RuntimeException以外的异常，类型上都属于Exception类及其子类。如 IOException、SQLException 等以及用户自定义的Exception异常。对于这种异常，JAVA编译器强制要求我们必需对出现的这些异常进行catch并处理，否则程序就不能编译通过。所以，面对这种异常不管我们是否愿意，只能自己去写一大堆catch块去处理可能的异常。 

## 常见的RuntimeException

ArrayStoreException         试图将错误类型的对象存储到一个对象数组时抛出的异常

ClassCastException          试图将对象强制转换为不是实例的子类时，抛出该异常

IllegalArgumentException    抛出的异常表明向方法传递了一个不合法或不正确的参数

IndexOutOfBoundsException   指示某排序索引（例如对数组、字符串或向量的排序）超出范围时抛出

NoSuchElementException      表明枚举中没有更多的元素

NullPointerException        当应用程序试图在需要对象的地方使用 null 时，抛出该异常
