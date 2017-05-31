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