---
layout: post
title: tomcat中sslProtocol含义
category: 技术
keywords: server
---
```
vi tomcat/conf/server.xml

setProtocol="TLSv1.2" 
sslEnabledProtocols="TLSv1.2"
```
## 各种配置含义
1. setProtocol="TLS" will enable SSLv3 and TLSv1
2. setProtocol="TLSv1.2" will enable SSLv3, TLSv1, TLSv1.1 and TLS v1.2
3. setProtocol="TLSv1.1" will enable SSLv3, TLSv1, and TLSv1.1
4. setProtocol="TLSv1" will enable SSLv3 and TLSv1
5. setProtocol="SSL" will enable SSLv3 and TLSv1
6. setProtocol="SSLv3" will enable SSLv3 and TLSv1
7. setProtocol="SSLv2" won't work

## SSLv3协议漏洞解决办法
SSLv3被曝出存在协议漏洞：“通过此漏洞可以窃取客户端与server端使用SSLv3加密通信的明文内容，危害严重”，目前官方暂无升级修复补丁和攻击利用方式公布。
<br>
<br>
关闭SSLv3
