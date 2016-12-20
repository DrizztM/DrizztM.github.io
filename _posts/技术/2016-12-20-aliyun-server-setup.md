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

## tomcat安装

cd /usr/local/

mkdir tomcat

cd /home/setup

tar zxvf apache-tomcat-8.5.9.tar.gz

mv apache-tomcat-8.5.9 /usr/local/tomcat/

<font color="red">安装以后需要添加apr模式:在Tomcat中使用JNI的方式来读取文件以及进行网络传输。这个东西可以大大提升Tomcat对静态文件的处理性能，同时如果你使用了HTTPS方式 传输的话，也可以提升SSL的处理性能。</font> 

## openssl安装

cd /home/setup

tar zxvf openssl-1.1.0c.tar.gz

cd openssl-1.1.0c

./config --prefix=/usr/local/openssl -fPIC no-gost no-shared no-zlib

make

make install

<font color="red">以下更换系统自带的openssl，千万不要删除自带的openssl，会导致ssh无法连接！</font>

mv /usr/bin/openssl /usr/bin/openssl.OFF

mv /usr/include/openssl /usr/include/openssl.OFF

ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl

ln -s /usr/local/openssl/include/openssl /usr/include/openssl

echo "/usr/local/openssl/lib">>/etc/ld.so.conf

ldconfig -v

openssl version -a

## apr安装

cd /home/setup

tar zxvf apr-1.5.2.tar.gz

cd apr-1.5.2

./configure --prefix=/usr/local/apr

报错：rm: cannot remove `libtoolT': No such file or directory

修改脚本：configure

$RM "$cfgfile" 改为 $RM -f "$cfgfile" 

./configure --prefix=/usr/local/apr

make

make install

## apr-util安装

cd /home/setup

tar zxvf apr-util-1.5.4.tar.gz

cd apr-util-1.5.4

./configure --with-apr=/usr/local/apr

make

make install

## tomcat-native安装

cd /usr/local/tomcat/apache-tomcat-8.5.9/bin/

tar zxvf tomcat-native.tar.gz

cd tomcat-native-1.2.10-src/native/

./configure --prefix=/usr/local/tomcat-native --with-apr=/usr/local/apr --with-ssl=/usr/local/openssl

make

make install

修改tomcat脚本catalina.sh，最上面加上一行，不然会报找不到类库的错误：

CATALINA_OPTS="$CATALINA_OPTS -Djava.library.path=/usr/local/tomcat-native/lib"

