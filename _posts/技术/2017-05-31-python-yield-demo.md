---
layout: post
title: python中的yield的一个例子
category: 技术
keywords: python
---

```python
import time  
def tail(f):  
    f.seek(0,2)#移动到文件EOF，参考：[seek](http://docs.python.org/2/library/stdtypes.html?highlight=file#file.seek)  
    while True:  
        line = f.readline()  #读取文件中新的文本行
        if not line:
            time.sleep(0.1)  
            continue  
        yield line
  
def grep(lines,searchtext):  
    for line in lines:  
        if searchtext in line:  
            yield line
flog = tail(open('warn.log'))
pylines = grep(flog,'python')
for line in pylines:
    print(line,)
```

这段代码实现了tail -f warn.log | grep python的功能，具体执行顺序如下：<br>
1. flog = tail(open('warn.log'))
2. tail方法中的yield line
3. pylines = grep(flog,'python')
4. grep方法中的for line in lines:回到2
5. grep方法中的yield line
6. for line in pylines，回到5
7. grep方法中的for line in lines:回到2
<br>
总结一下：yield当时不会执行，仅仅返回一个生成器对象，当对这个对象调用next()(python3中是__next__())或者进行for循环时，才开始执行。