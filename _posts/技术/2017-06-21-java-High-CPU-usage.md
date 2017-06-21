---
layout: post
title: java高CPU占用的排查
category: 技术
keywords: java
---

1. 通过<font color="blue">ps auxw --sort=-%cpu</font>命令查看各个进程CPU使用率。
2. 通过<font color="blue">ps -mp pid -o THREAD,tid,time</font>命令查看线程列表。
3. 在线程列表中找到CPU占用高并且持续时间长的TID。
4. 将需要的线程ID转换为16进制格式<font color="blue">printf "%x\n" tid</font>。
5. 通过<font color="blue">jstack pid |grep tid -A 30</font>命令打印堆栈信息，找到出问题部分代码。