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
<br>
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
可以看到能得到我们平时使用的 str, int, 以及我们初始化的一个实例对象Trick()

但是下面的方法你可能没有见过，type同样可以用来动态创建一个类

type(类名, 父类的元组（针对继承的情况，可以为空），包含属性的字典（名称和值）)

这个怎么用呢，我要用这个方法创建一个类,让我们看下下面的代码