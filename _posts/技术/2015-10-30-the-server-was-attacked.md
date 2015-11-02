---
layout: post
title: 最近，服务器被攻击了
category: 技术
keywords: 安全
---

最近nginx服务器经常被黑客攻击，nginx利用nginx\_tcp\_proxy\_module模块对tcp请求进行代理服务，配置文件如下：

```
worker_processes  4;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
	use epoll;
    worker_connections  1024;
}

tcp {
    #tcp_nopush     on;

    #keepalive_timeout  0;

    #gzip  on;

	timeout 1d; 

	#不停得发送心跳等待服务器返回状态（长连接）
    proxy_read_timeout 10d; 
    proxy_send_timeout 10d; 
    proxy_connect_timeout 30; 
	
	upstream ciaAuth {
        server ip1:port weight=5;
        server ip2:port weight=5;
        
    }

	server {
        listen       8000;
        server_name  authProxy;
        tcp_nodelay on;
        proxy_pass ciaAuth;
    }

}
```

这样会生成tcp请求的访问日志，利用如下命令可以将用户的ip地址按照访问次数排序，并导入到黑名单配置文件中。

```
awk '{print $4}' tcp_access.log |sort | uniq -c | sort -n -k 1 -r | head -n 100 | awk '{if($1>1000)print "deny "$2";"}'> blockip.conf
```

解释一下：

awk '{print $4}' tcp_access.log<br>
输出访问日志中第四列（即ip列）

sort<br>
排序

uniq -c<br>
去重并计数

sort -n -k 1 -r<br>
-n	依照数值的大小排序<br>
-k1	选择第一列进行排序（即数量列）<br>
-r	倒序

head -n 100<br>
取前100条数据

awk '{if($1>1000)print "deny "$2";"}'<br>
如果第一列数量大于1000，则输入deny 第二列(ip);

\> blockip.conf<br>
将打印结果保存到blockip.conf中。

<font color="blue">还可用如下命令： netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -n -r | head -n 10</font>


## nginx报错

```
2015/11/01 20:04:19 [error] 14533#0: *1855045 upstream servers are busy or encounter error! while connecting to upstream, client: 117.136.0.0, server: 0.0.0.0:88
```

修改：worker\_connections  1024;<br>
调整为：worker\_connections  5000;

## 阿里云的云盾

千万不要让阿里云去做清洗，会导致一部分ip无法访问。

## 其他方案（待研究）

fail2ban

直接使用iptables