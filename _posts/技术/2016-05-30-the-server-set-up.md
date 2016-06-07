---
layout: post
title: 阿里云服务器简单搭建
category: 技术
keywords: server
---

## 系统环境

```
[root@iZ28nl5v3rjZ ~]# lsb_release -a
LSB Version:	:base-4.0-amd64:base-4.0-noarch:core-4.0-amd64:core-4.0-noarch
Distributor ID:	CentOS
Description:	CentOS release 6.5 (Final)
Release:	6.5
Codename:	Final
```

## 修改Centos的yum源

1.查看当前源

```
[root@iZ28nl5v3rjZ data]# cd /etc/yum.repos.d/
[root@iZ28nl5v3rjZ yum.repos.d]# ls
backup  CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-Media.repo  CentOS-Vault.repo  epel.repo
```

2.备份你的原镜像文件，以免出错后可以恢复

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

3.下载新的CentOS-Base.repo到/etc/yum.repos.d/

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
```

4.运行yum makecache生成缓存

```
yum clean all
yum makecache
```

## 安装Java环境

1.查看CentOS自带JDK是否已安装

```
yum list installed | grep java
```

2.卸载CentOS系统自带Java环境

卸载JDK相关文件输入：yum -y remove java-1.7.0-openjdk*。<br>
卸载tzdata-java输入：yum -y remove tzdata-java.noarch。<br>
当结果显示为Complete！即卸载完毕。<br>

3.查看yum库中的Java安装包

```
yum -y list java*
```

4.使用yum安装Java环境

```
yum -y install java-1.7.0-openjdk*
```
当结果显示为Complete！即安装完毕。

5.检查安装情况

```
[root@iZ28nl5v3rjZ yum.repos.d]# java -version
java version "1.7.0_101"
OpenJDK Runtime Environment (rhel-2.6.6.4.el6_8-x86_64 u101-b00)
OpenJDK 64-Bit Server VM (build 24.95-b01, mixed mode)
```

## 安装mysql

1.查看CentOS自带mysql是否已安装

```
yum list installed | grep mysql
```

2.卸载CentOS系统自带mysql数据库

```
yum -y remove mysql-libs.x86_64
```
若有多个依赖文件则依次卸载。<br>
当结果显示为Complete！即卸载完毕。

3.查看yum库上的mysql版本信息

```
yum list | grep mysql
或
yum -y list mysql*
```

4.使用yum安装mysql数据库

```
yum -y install mysql-server mysql mysql-devel
```
将mysql-server、mysql、mysql-devel都安装好，当结果显示为Complete！即安装完毕。

5.查看刚安装mysql数据库版本信息

```
rpm -qi mysql-server
```

6.设置字符集以及最大连接数

mysql配置文件/etc/my.cnf中，加入：

```
default-character-set=utf8
max_connections=1000
```

重启

```
service mysqld restart
```

检查最大连接数：

```
mysqladmin -uroot variables | grep max_connections
```

7.开机启动

```
chkconfig --add mysqld
chkconfig mysqld on
```

查看开机启动设置是否成功

```
chkconfig --list | grep mysql*
```

8.登录

创建root管理员

```
mysqladmin -u root password root
mysql -uroot -proot
```

忘记密码

```
service mysqld stop
mysqld_safe --user=root --skip-grant-tables
新开一个xshell
mysql -u root
use mysql
update user set password=password("root") where user="root";
flush privileges;
```

9.远程登录

```
use mysql;
GRANT ALL ON *.* TO root@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
```

10.Linux MySQL的几个重要目录

my.cnf地址

         /etc/my.cnf

数据库目录

         /var/lib/mysql/

配置文件

         /usr/share/mysql（mysql.server命令及配置文件）

相关命令

         /usr/bin（mysqladmin mysqldump等命令）

启动脚本

         /etc/rc.d/init.d/（启动脚本文件mysql的目录） 

## 安装redis

1.首先为yum添加epel源

```
yum install epel-release
```

2.安装redis

```
yum install redis
```

3.启动redis，并设定开机自动启动

```
service redis start
chkconfig redis on
```