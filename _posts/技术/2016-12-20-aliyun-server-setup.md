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

<font color="red">以下为更换系统自带的openssl，千万不要删除自带的openssl，会导致ssh无法连接！</font>

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

CATALINA\_OPTS="$CATALINA\_OPTS -Djava.library.path=/usr/local/tomcat-native/lib"

<font color="red">以上配置完毕后tomcat成功开启apr模式。</font>

## tomcat调优

1.删除webapps下所有的应用。

2.删除conf/tomcat\_users.xml，以及server.xml中关于tomcat\_users.xml的引用，旨在增强安全性。（去掉pathname="conf/tomcat-users.xml"）

3.配置conf/logging.properties，删除所有的日志配置项，将日志输出级别调整到INFO（改成WARNING后看不到启动信息了）。样例如下：

```
handlers = java.util.logging.ConsoleHandler
.handlers = java.util.logging.ConsoleHandler
java.util.logging.ConsoleHandler.level = INFO
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
```

4.bin目录下增加setenv.sh,进行jdk调优，8核8G服务器的配置如下：

JAVA\_OPTS="-server -Xms4G -Xmx4G -Xss256k -XX:+UseParallelOldGC -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/home/dump -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -Xloggc:/home/dump/heap_trace.txt -XX:NewSize=1G -XX:MaxNewSize=1G"

5.Tomcat 8启动很慢，且日志上无任何错误，在日志中查看到如下信息
```
INFO: Creation of SecureRandom instance for session ID generation using [SHA1PRNG] took [57,733] milliseconds.
```

打开$JAVA\_PATH/jre/lib/security/java.security这个文件，找到下面的内容：

securerandom.source=file:/dev/urandom

替换成

securerandom.source=file:/dev/./urandom

6.并发数调整

修改server.xml文件
```
<Connector port="8080" protocol="org.apache.coyote.http11.Http11AprProtocol"
               maxThreads="2000"
               minSpareThreads="1000"
               enableLookups="false"
               URIEncoding="utf-8"
               acceptCount="1000"
               connectionTimeout="20000"
               redirectPort="8443" />
```