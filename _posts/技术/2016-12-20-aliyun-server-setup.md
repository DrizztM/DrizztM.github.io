---
layout: post
title: 阿里云服务器搭建过程
category: 技术
keywords: server
---

## JDK安装

cd /usr/local

mkdir java

cd /home/setup

tar zxvf jdk-8u111-linux-x64.tar.gz

mv jdk-8u111-linux-x64.tar.gz /usr/local/java/

vi /etc/profile

最下面添加：

```
export JAVA_HOME=/usr/local/java/jdk1.8.0_111
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH
```

source /etc/profile
