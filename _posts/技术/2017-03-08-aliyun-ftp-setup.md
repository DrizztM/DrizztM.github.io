---
layout: post
title: 阿里云搭建FTP服务器
category: 技术
keywords: server
---

## 安装
```
yum check-update  检查可更新的所有软件包
yum install vsftpd -y	安装ftp服务
```

## 配置
```
vi /etc/vsftpd/vsftpd.conf
anonymous_enable=YES  禁止匿名访问，降YES改成NO
#ascii_upload_enable 允许使用ascii码上传
#ascii_download_enable 允许使用ascii码下载
去掉前面的“#”号
```

## 启动
```
service vsftpd start
useradd -p /alidata/www/wwwroot -s /sbin/nologin pwftp  添加用户
passwd pwftp  修改该用户密码
chkconfig vsftpd on   开机自动启动
```