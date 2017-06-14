---
layout: post
title: python 类和元类(metaclass)的理解和简单运用（转）
category: 技术
keywords: python
---

## 1.python中的类
首先在python中，所有东西都是对象。这句话非常重要，要理解元类要重新来理解一下python中的类。
```python
class Trick(object):
    pass
```
当python在执行带class语句的时候，会初始化一个类对象放在内存里面。例如这里会初始化一个Trick对象。这个对象(类)自身拥有创建对象(通常我们说的实例，但是在python中还是对象)的能力。<br>

为了方便后续理解，我们可以先尝试一下在新式类中最古老厉害的关键字type。
```python
input:
class Trick(object):
    pass

print(type('123'))
print(type(123))
print(type(Trick()))

output:
<type 'str'>
<type 'int'>
<class '__main__.Trick'>
```
可以看到能得到我们平时使用的 str, int, 以及我们初始化的一个实例对象Trick()。

但是下面的方法你可能没有见过，type同样可以用来动态创建一个类。

type(类名, 父类的元组（针对继承的情况，可以为空），包含属性的字典（名称和值）)。

这个怎么用呢，我要用这个方法创建一个类,让我们看下下面的代码：
```python
input:
print(type('trick', (), {}))

output:
<class '__main__.trick'>
同样我们可以实例化这个类对象

input:
print(type('trick', (), {})())

output:
<__main__.trick object at 0x01033A30>
```
可以看到，这里就是一个trick的实例对象了。

同样的这个方法还可以初始化创建类的父类，同时也可以初始化类属性:
```python
input:
class FlyToSky(object):
    pass

pw = type('Trick', (FlyToSky, ), {'laugh_at': 'hahahaha'})
print(pw().laugh_at)
print(pw.__dict__)
print(pw.__bases__)
print(pw().__class__)
print(pw().__class__.__class__)


output:
hahahaha
{'__module__': '__main__', 'laugh_at': 'hahahaha', '__doc__': None}
(<class '__main__.FlyToSky'>,)
<class '__main__.Trick'>
<type 'type'>
```
下面我将依次理一下上面的内容，在此之前我必须先介绍两个魔法方法：

1.__class__这个方法用于查看对象属于是哪个生成的，这样理解在python中的所有东西都是对象，类对象也是对象。如果按照以前的思维来想的话就是类是元类的实例，而实例对象是类的实例。

2.__bases__这个方法用于得到一个对象的父类是谁，特别注意一下__base__返回单个父类，__bases__以tuple形式返回所有父类。

好了知道了这两个方法我来依次说一下每行什么意思。

1.使用type创建一个类赋值给pw type的接受的三个参数的意思分辨是(类的名称， 类是否有父类(), 类的属性字典{})

2.这里初始化一个类的实例，然后尝试去获得父类的laugh_at属性值，然后得到结果hahahaha

3.取一个pw的也就是我们常见类的类字典数据

4.拿到pw的父类，结果是我们指定的 FlyToSky

5.pw的实例pw()属于哪个类初始化的，可以看到是class Trick

6.我们再看class trick是谁初始化的？ 就是元类type了