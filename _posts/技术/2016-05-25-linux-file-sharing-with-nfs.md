---
layout: post
title: linux设置nfs共享目录
category: 技术
keywords: server
---

## 使用场景

负载均衡多态服务器，上传文件后不同步问题！<br>
两台机器（可以是虚拟机）：<br>
10.175.202.119—->a机器<br>
10.171.239.25——->b机器<br>

## 查看是否安装NFS（portmap是用于RPC传输的）

```
rpm -q nfs-utils portmap   (portmap: 不再使用 一般由rpcbind代替)
(centos 6.4中portmap 已经被rpcbind取代)
```

## 安装nfs

```
yum install nfs-utils
```

## 配置文件

```
1.首先配置/etc/exports文件
    vi /etc/exports
2.添加内容
    /home/share 10.171.239.25(rw,async) 10.171.239.26(ro)   //多个内容
    共享目录    客户机1                   客户机2
或者
    /data/web/webphoto 10.171.239.25(rw,no_root_squash,async)
ro   只读
rw   读写
sync： 资料同步写入到内存与硬盘当中 
async:资料会先暂存于内存当中，而非直接写入硬盘 
all_squash 所有登录用户指定为nobody
no_all_squash 以当前登录的用户所设定的权限(默认设定)
anonuid  在使用all_squash时的选择，可以对登录的帐号指定为指定的用户ID帐号
anougid  在使用all_squash时的选择，可以对登录的帐号指定为指定的组ID帐号
root_squash root用户指定为nobody    no_root_squash：（允许远程用户以root帐号登录(比较不安全)）不讲root用户及所属用户组映射为匿名用户或用户组，默认root是被映射为匿名用户的nfsnobody，所有即使开了rw写权限，客户机也使无法写入的，这个不映射为匿名用户，还保留原来的用户权限就可以读写了，因为一般都是用root用户登录的。
注意：当客机是否有写权限时，还要看该目录对该用户有没有开放写入权限
```

## 启动服务

```
1.启动rpcbind服务 启动nfs服务  (且rpcbind一定要先于nfs启动)  
    service rpcbind start  或者 /etc.init.d/rpcbind start
    service nfs start      或者 /etc.init.d/nfs start
2. 停止服务
停止NFS服务器前，需要先停止NFS服务再停止portmap服务。如果系统中还有其它服务需要portmap时，则可以不用停止portmap服务。
    service rpcbind stop  或者 /etc.init.d/rpcbind stop
    service nfs stop      或者 /etc.init.d/nfs stop
3.自动启动NFS服务
    chkconfig --level 35 rpcbind on
    chkconfig --level 35 nfs on
4.中portmap查看所有输出的共享目录    
    showmount -e 10.175.202.119
    输出以下结果：
        Export list for 10.175.202.119:
        /data/web/webphoto 10.171.239.25
5.显示所有被挂载的所有输出目录    
    showmount -d 192.168.1.150 
说明成功启动（注意：在centos 6.x之后的版本showmount -e 后面一定要指定ip，否则会卡在那，也没输出，5.x的版本可以不指定）
```

## 挂载

```
下面是挂载nfs到指定机器的目录 
    1.ssh登陆到另一台机器b：
        ssh root@10.171.239.25
        mount -t nfs 10.175.202.119:/data/web/webphoto /data/web/webphoto
        -t:指定挂载设备的文件类型（nfs是网络文件系统）
        10.175.202.119：nfs服务器ip地址
        /data/web/webphoto ：nfs服务器的共享目录
        /data/web/webphoto：挂载在本地的目录
    2.启动时自动连接nfs服务器
        要先在启动时自动连接nfs服务器上的共享目录，要编辑/etc/fstab文件。在文件中加入
        10.175.202.119:/data/web/webphoto /data/web/webphoto nfs defaults 0 0
        10.175.202.119:/data/web/webphoto：nfs服务器的共享目录
        /data/web/webphoto：本机挂载目录
    在a机器上建立文件test.txt,然后发现b机器有了同样的文件，nfs服务端和客户端正常同步。
```