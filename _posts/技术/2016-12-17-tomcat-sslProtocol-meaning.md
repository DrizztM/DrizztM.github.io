---
layout: post
title: tomcat中sslProtocol含义
category: 技术
keywords: server
---

## 各种配置含义
1. setProtocol="TLS" will enable SSLv3 and TLSv1
2. setProtocol="TLSv1.2" will enable SSLv3, TLSv1, TLSv1.1 and TLS v1.2
3. setProtocol="TLSv1.1" will enable SSLv3, TLSv1, and TLSv1.1
4. setProtocol="TLSv1" will enable SSLv3 and TLSv1
5. setProtocol="SSL" will enable SSLv3 and TLSv1
6. setProtocol="SSLv3" will enable SSLv3 and TLSv1
7. setProtocol="SSLv2" won't work

## SSLv3协议漏洞解决办法
```
vi tomcat/conf/server.xml

setProtocol="TLSv1.2" 
sslEnabledProtocols="TLSv1.2"
```
