---
layout: post
title: JVM性能调优监控工具jps、jstack、jmap、jhat、jstat、hprof使用详解（转）
category: 技术
keywords: java
---

## jps(Java Virtual Machine Process Status Tool)
jps主要用来输出JVM中运行的进程状态信息。语法格式如下：
```
jps [options] [hostid]
```
如果不指定hostid就默认为当前主机或服务器。<br>
命令行参数选项说明如下：
```
-q 不输出类名、Jar名和传入main方法的参数
-m 输出传入main方法的参数
-l 输出main类或Jar的全限名
-v 输出传入JVM的参数
```
比如下面：
```
root@ubuntu:/# jps -m -l
2458 org.artifactory.standalone.main.Main /usr/local/artifactory-2.2.5/etc/jetty.xml
29920 com.sun.tools.hat.Main -port 9998 /tmp/dump.dat
3149 org.apache.catalina.startup.Bootstrap start
30972 sun.tools.jps.Jps -m -l
8247 org.apache.catalina.startup.Bootstrap start
25687 com.sun.tools.hat.Main -port 9999 dump.dat
21711 mrf-center.jar
```

## jstack
jstack主要用来查看某个Java进程内的线程堆栈信息。语法格式如下：
```
jstack [option] pid
jstack [option] executable core
jstack [option] [server-id@]remote-hostname-or-ip
```
命令行参数选项说明如下：
```
-l long listings，会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况
-m mixed mode，不仅会输出Java堆栈信息，还会输出C/C++堆栈信息（比如Native方法）
```
jstack可以定位到线程堆栈，根据堆栈信息我们可以定位到具体代码，所以它在JVM性能调优中使用得非常多。下面我们来一个实例找出某个Java进程中最耗费CPU的Java线程并定位堆栈信息，用到的命令有ps、top、printf、jstack、grep。<br>
第一步先找出Java进程ID，我部署在服务器上的Java应用名称为mrf-center：
```
root@ubuntu:/# ps -ef | grep mrf-center | grep -v grep
root     21711     1  1 14:47 pts/3    00:02:10 java -jar mrf-center.jar
```
得到进程ID为21711，第二步找出该进程内最耗费CPU的线程，可以使用ps -Lfp pid或者ps -mp pid -o THREAD, tid, time或者top -Hp pid，我这里用第三个，输出如下：
<img src="/assets/img/0083.png"><br>
TIME列就是各个Java线程耗费的CPU时间，CPU时间最长的是线程ID为21742的线程，用
```
printf "%x\n" 21742
```
得到21742的十六进制值为54ee，下面会用到。