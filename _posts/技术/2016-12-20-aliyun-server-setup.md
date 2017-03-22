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

./config \--prefix=/usr/local/openssl -fPIC no-gost no-shared no-zlib

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

./configure \--prefix=/usr/local/apr

报错：rm: cannot remove `libtoolT': No such file or directory

修改脚本：configure

$RM "$cfgfile" 改为 $RM -f "$cfgfile" 

./configure \--prefix=/usr/local/apr

make

make install

## apr-util安装

cd /home/setup

tar zxvf apr-util-1.5.4.tar.gz

cd apr-util-1.5.4

./configure \--with-apr=/usr/local/apr

make

make install

## tomcat-native安装

cd /usr/local/tomcat/apache-tomcat-8.5.9/bin/

tar zxvf tomcat-native.tar.gz

cd tomcat-native-1.2.10-src/native/

./configure \--prefix=/usr/local/tomcat-native \--with-apr=/usr/local/apr \--with-ssl=/usr/local/openssl

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
               maxThreads="1000"
               minSpareThreads="500"
               enableLookups="false"
               URIEncoding="utf-8"
               acceptCount="500"
               connectionTimeout="20000"
               redirectPort="8443" />
```

## 设置nfs共享目录

10.25.196.23—->a机器<br>
10.25.156.112——->b机器

以下命令全部在10.25.196.23上操作。

查看nfs是否安装：<br>
rpm -q nfs-utils

yum install nfs-utils

vi /etc/exports

/home/images 10.25.156.112(rw,no_root_squash,async)

启动服务

```
1.启动rpcbind服务 启动nfs服务  (且rpcbind一定要先于nfs启动)  
    service rpcbind start
    service nfs start
2. 停止服务
停止NFS服务器前，需要先停止NFS服务再停止rpcbind服务。
    service rpcbind stop
    service nfs stop
3.自动启动NFS服务
    chkconfig --level 3 rpcbind on
    chkconfig --level 3 nfs on
4.查看所有输出的共享目录    
    showmount -e 10.25.196.23
    输出以下结果：
	Export list for 10.25.196.23:
	/home/images 10.25.156.112
```

挂载

```
1.ssh登陆到另一台机器b：
	ssh root@10.25.156.112
	mount -t nfs 10.25.196.23:/home/images /home/images
	-t:指定挂载设备的文件类型（nfs是网络文件系统）
	10.25.196.23：nfs服务器ip地址
	/home/images：nfs服务器的共享目录
	/home/images：挂载在本地的目录
2.启动时自动连接nfs服务器
	vi /etc/fstab
	10.25.196.23:/home/images /home/images nfs defaults 0 0
	在a机器上建立文件test.txt,然后发现b机器有了同样的文件，nfs服务端和客户端正常同步。
```

## redis安装

cd /home/setup/

tar zxvf redis-3.2.6.tar.gz

cd redis-3.2.6

make

make install

cp redis-server /usr/local/bin/

cp redis-cli  /usr/local/bin

mv /home/setup/redis-3.2.6 /usr/local/redis

cd /usr/local/redis

vi redis.conf

```
tcp-backlog 1000 //改大一点
bind 127.0.0.1 //绑定ip
daemonize yes  //改为后台运行
requirepass foobared  //打开这个选项，设置密码
maxmemory 10gb //最大内存最好不要超过空闲内存的3/5，超过32GB会自动进入64位世界，指针长度*2，20%的空间会被指针消耗，性能略有影响
```

vi /etc/init.d/redis

```
#!/bin/sh
#chkconfig: 345 86 14
#description: Startup and shutdown script for Redis
 
PROGDIR=/usr/local/redis/src
PROGNAME=redis-server
DAEMON=$PROGDIR/$PROGNAME
CONFIG=/usr/local/redis/redis.conf
PIDFILE=/var/run/redis_6379.pid
DESC="redis daemon"
SCRIPTNAME=/etc/rc.d/init.d/redis
 
start()
{
         if test -x $DAEMON
         then
        echo -e "Starting $DESC: $PROGNAME"
                   if $DAEMON $CONFIG
                   then
                            echo -e "OK"
                   else
                            echo -e "failed"
                   fi
         else
                   echo -e "Couldn't find Redis Server ($DAEMON)"
         fi
}
 
stop()
{
         if test -e $PIDFILE
         then
                   echo -e "Stopping $DESC: $PROGNAME"
                   if kill `cat $PIDFILE`
                   then
                            echo -e "OK"
                   else
                            echo -e "failed"
                   fi
         else
                   echo -e "No Redis Server ($DAEMON) running"
         fi
}
 
restart()
{
    echo -e "Restarting $DESC: $PROGNAME"
    stop
         start
}
 
list()
{
         ps aux | grep $PROGNAME
}
 
case $1 in
         start)
                   start
        ;;
         stop)
        stop
        ;;
         restart)
        restart
        ;;
         list)
        list
        ;;
 
         *)
        echo "Usage: $SCRIPTNAME {start|stop|restart|list}" >&2
        exit 1
        ;;
esac
exit 0
```

chmod +x /etc/init.d/redis

service redis start

service redis stop
  
chkconfig redis on
