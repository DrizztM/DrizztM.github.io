---
layout: post
title: fail2ban配置说明
category: 技术
keywords: 安全
---

fail2ban搞定了，单开一贴说明。

## 安装

```
yum install fail2ban
```

## 配置

安装完成后配置文件在目录/etc/fail2ban/中：

```
/etc/fail2ban/fail2ban.conf  #fail2ban的配置文件
/etc/fail2ban/jail.conf #阻挡设定文件
/etc/fail2ban/filter.d/ #具体过滤规则文件目录 
/etc/fail2ban/action.d/ #具体过滤规则检测到后采取相对应措施的目录 
```

## 监控nginx日志

### 首先修改jail.conf：

```
[nginx]    ;规则名字
enabled = true ;是否启用
port = 8000 ;监控端口
filter = nginx    ;需要过滤匹配规则
logpath = /var/log/nginx/tcp_access_log; 日志路径
findtime =60    ;检测周期 单位秒 以下一样
bantime =300    ;iptable封禁IP时间
maxretry =10    ;最大尝试次数
action = iptables[name=nginx, port="8000", protocal=tcp] ;发现暴力破解采取iptalbes封禁IP的措施
```

注意：action = iptables[name=nginx, port="8000", protocal=tcp]中的port端口号一定要加""。

### 然后创建 /etc/fail2ban/filter.d/nginx.conf文件，并添加以下内容：

```
[Definition]
failregex = .*] <HOST> 0.0.0.0:8000 .*$	;需要匹配日志发现攻击行为的正则,<HOST>为fail2ban内置变量匹配IP，不可修改
ignoreregex =	;需要忽略的正则
```

### 修改日志文件地址，在/etc/fail2ban/fail2ban.conf中：

在vi中输入：“/logtarget”来查找，按“n”查找下一个。

```
logtarget = /etc/fail2ban/fail2ban.log
```

### 启动服务

```
service fail2ban start
```