---
layout: post
title: python 类和元类(metaclass)的理解和简单运用
category: 机器学习
keywords: python
---

针对https://segmentfault.com/a/1190000006814152这篇文章的理解以及在python3中的改造。

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

1.__class__这个方法用于查看对象属于是哪个生成的，这样理解在python中的所有东西都是对象，类对象也是对象。如果按照以前的思维来想的话就是类是元类的实例，而实例对象是类的实例。<br>
2.__bases__这个方法用于得到一个对象的父类是谁，特别注意一下__base__返回单个父类，__bases__以tuple形式返回所有父类。<br>

好了知道了这两个方法我来依次说一下每行什么意思。

1.使用type创建一个类赋值给pw type的接受的三个参数的意思分辨是(类的名称， 类是否有父类(), 类的属性字典{})<br>
2.这里初始化一个类的实例，然后尝试去获得父类的laugh_at属性值，然后得到结果hahahaha<br>
3.取一个pw的也就是我们常见类的类字典数据<br>
4.拿到pw的父类，结果是我们指定的 FlyToSky<br>
5.pw的实例pw()属于哪个类初始化的，可以看到是class Trick<br>
6.我们再看class trick是谁初始化的？ 就是元类type了<br>

## 2.什么是元类以及简单运用

到底什么是元类？通俗的就是说，元类就是创建类的类。。。这样听起来是不是超级抽象？来看看这个:
```python
Trick = MetaClass()
MyObject = Trick()
```
上面我们已经介绍了，搞一个Trick可以直接这样:
```python
Trick = type('Trick', (), {})
```
可以这样其实就是因为，Type实际上是一个元类，用他可以去创建类。什么是元类刚才说了，元类就是创建类的类。也可以说他就是一个类的创建工厂。

类上面的__metaclass__属性，相信愿意了解元类细节的盆友，都肯定见过这个东西，而且为之好奇。不然我不知道是什么支撑你看到这里的?使用了__metaclass__这个魔法方法就意味着就会用__metaclass__指定的元类来创建类了。
```python
class Trick(FlyToSky):
    pass
```
当我们在创建上面的类的时候，python做了如下的操作：Trick中有__metaclass__这个属性吗？如果有，那么Python会在内存中通过__metaclass__创建一个名字为Trick的类对象，也就是Trick这个东西。如果Python没有找到__metaclass__，它会继续在自己的父类FlyToSky中寻找__metaclass__属性，并且尝试以__metaclass__指定的方法创建一个Trick类对象。如果Python在任何一个父类中都找不到__metaclass__，它也不会就此放弃，而是去模块中搜寻是否有__metaclass__的指定。如果还是找不到，好吧那就是使用默认的type来创建Trick。

那么问题来了，我们要在__metaclass__中放置什么呢？答案是可以创建一个类的东西，type,或者任何用到type或子类化type的东西都行。

## 3.自定义元类

自定义类的的目的，我总结了一下就是拦截类的创建，然后修改一些特性，然后返回该类。是不是有点熟悉？没错，就是感觉是装饰器干的事情，只是装饰器是修饰一个函数，同样是一个东西进去，然后被额外加了一些东西，最后被返回。

其实除了上面谈到的制定一个__metaclass__,并不一定要是正式类，是一个函数也可以。要创建一个使所有模块级别都是用这个元类创建类的话，在模块级别设定__metaclass__就可以了。先写一个来试试看，我还是延用stackoverflow上面那个哥们的例子，将所有的属性都改为大写的。

来看这个例子：
```python
input:
def upper_attr(class_name, class_parents, class_attr):
    """
    返回一个对象,将属性都改为大写的形式
    :param class_name:  类的名称
    :param class_parents: 类的父类tuple
    :param class_attr: 类的参数
    :return: 返回类
    """
    # 生成了一个generator
    attrs = ((name, value) for name, value in class_attr.items() if not name.startswith('__'))
    uppercase_attrs = dict((name.upper(), value) for name, value in attrs)
    return type(class_name, class_parents, uppercase_attrs)

__metaclass__ = upper_attr

pw = upper_attr('Trick', (), {'bar': 0})
print(hasattr(pw, 'bar'))
print(hasattr(pw, 'BAR'))
print(pw.BAR)

output:
False
True
0
```
可以从上面看到，我实现了一个元类(metaclass)， 然后指定了模块使用这个元类来创建类，所以当我下面使用type进行类创建的时候，可以发现小写的bar参数被替换成了大写的BAR参数，并且在最后我调用了这个类属性并打印了它。

上面我们使用了函数做元类传递给类，下面我们使用一个正式类来作为元类传递给__metaclass__:
```python
class UpperAttrMetaclass(type):
    def __new__(cls, class_name, class_parents, class_attr):
        attrs = ((name, value) for name, value in class_attr.items() if not name.startswith('__'))
        uppercase_attrs = dict((name.upper(), value) for name, value in attrs)
        return type.__new__(cls, class_name, class_parents, uppercase_attrs)

class Trick(metaclass=UpperAttrMetaclass):
    bar = 12
    money = 'unlimited'

print(Trick.BAR)
print(Trick.MONEY)
```

## 4.元类的使用场景

ORM全称“Object Relational Mapping”，即对象-关系映射，就是把关系数据库的一行映射为一个对象，也就是一个类对应一个表，这样，写代码更简单，不用直接操作SQL语句。

要编写一个ORM框架，所有的类都只能动态定义，因为只有使用者才能根据表的结构定义出对应的类来。

让我们来尝试编写一个ORM框架。

编写底层模块的第一步，就是先把调用接口写出来。比如，使用者如果使用这个ORM框架，想定义一个User类来操作对应的数据库表User，我们期待他写出这样的代码：
```python
class User(Model):
    # 定义类的属性到列的映射：
    id = IntegerField('id')
    name = StringField('username')
    email = StringField('email')
    password = StringField('password')

# 创建一个实例：
u = User(id=12345, name='Michael', email='test@orm.org', password='my-pwd')
# 保存到数据库：
u.save()
```
其中，父类Model和属性类型StringField、IntegerField是由ORM框架提供的，剩下的魔术方法比如save()全部由metaclass自动完成。虽然metaclass的编写会比较复杂，但ORM的使用者用起来却异常简单。

现在，我们就按上面的接口来实现该ORM。

首先来定义Field类，它负责保存数据库表的字段名和字段类型：
```python
class Field(object):

    def __init__(self, name, column_type):
        self.name = name
        self.column_type = column_type

    def __str__(self):
        return '<%s:%s>' % (self.__class__.__name__, self.name)
```
在Field的基础上，进一步定义各种类型的Field，比如StringField，IntegerField等等：
```python
class StringField(Field):

    def __init__(self, name):
        super(StringField, self).__init__(name, 'varchar(100)')

class IntegerField(Field):

    def __init__(self, name):
        super(IntegerField, self).__init__(name, 'bigint')
```
下一步，就是编写最复杂的ModelMetaclass了：
```python
class ModelMetaclass(type):

    def __new__(cls, name, bases, attrs):
        if name=='Model':
            return type.__new__(cls, name, bases, attrs)
        print('Found model: %s' % name)
        mappings = dict()
        for k, v in attrs.items():
            if isinstance(v, Field):
                print('Found mapping: %s ==> %s' % (k, v))
                mappings[k] = v
        for k in mappings.keys():
            attrs.pop(k)
        attrs['__mappings__'] = mappings # 保存属性和列的映射关系
        attrs['__table__'] = name # 假设表名和类名一致
        return type.__new__(cls, name, bases, attrs)
```
以及基类Model：
```python
class Model(dict, metaclass=ModelMetaclass):

    def __init__(self, **kw):
        super(Model, self).__init__(**kw)

    def __getattr__(self, key):
        try:
            return self[key]
        except KeyError:
            raise AttributeError(r"'Model' object has no attribute '%s'" % key)

    def __setattr__(self, key, value):
        self[key] = value

    def save(self):
        fields = []
        params = []
        args = []
        for k, v in self.__mappings__.items():
            fields.append(v.name)
            params.append('?')
            args.append(getattr(self, k, None))
        sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(fields), ','.join(params))
        print('SQL: %s' % sql)
        print('ARGS: %s' % str(args))
```
当用户定义一个class User(Model)时，Python解释器首先在当前类User的定义中查找metaclass，如果没有找到，就继续在父类Model中查找metaclass，找到了，就使用Model中定义的metaclass的ModelMetaclass来创建User类，也就是说，metaclass可以隐式地继承到子类，但子类自己却感觉不到。

在ModelMetaclass中，一共做了几件事情：

1.排除掉对Model类的修改；<br>
2.在当前类（比如User）中查找定义的类的所有属性，如果找到一个Field属性，就把它保存到一个__mappings__的dict中，同时从类属性中删除该Field属性，否则，容易造成运行时错误（实例的属性会遮盖类的同名属性）；<br>
3.把表名保存到__table__中，这里简化为表名默认为类名。<br>

在Model类中，就可以定义各种操作数据库的方法，比如save()，delete()，find()，update()等等。

我们实现了save()方法，把一个实例保存到数据库中。因为有表名，属性到字段的映射和属性值的集合，就可以构造出INSERT语句。

编写代码试试：
```python
u = User(id=12345, name='Michael', email='test@orm.org', password='my-pwd')
u.save()
```
输出如下：
```python
Found model: User
Found mapping: email ==> <StringField:email>
Found mapping: password ==> <StringField:password>
Found mapping: id ==> <IntegerField:uid>
Found mapping: name ==> <StringField:username>
SQL: insert into User (password,email,username,id) values (?,?,?,?)
ARGS: ['my-pwd', 'test@orm.org', 'Michael', 12345]
```
可以看到，save()方法已经打印出了可执行的SQL语句，以及参数列表，只需要真正连接到数据库，执行该SQL语句，就可以完成真正的功能。

不到100行代码，我们就通过metaclass实现了一个精简的ORM框架。

## 5.小结

metaclass是Python中非常具有魔术性的对象，它可以改变类创建时的行为。这种强大的功能使用起来务必小心。