---
layout: post
title: tomcat中sslProtocol含义
category: 技术
keywords: server
---

## 各种配置含义

## SSLv3协议漏洞解决办法
SSLv3被曝出存在协议漏洞：“通过此漏洞可以窃取客户端与server端使用SSLv3加密通信的明文内容，危害严重”，目前官方暂无升级修复补丁和攻击利用方式公布。

关闭SSLv3
```
vi tomcat/conf/server.xml

setProtocol="TLSv1.2" 
sslEnabledProtocols="TLSv1.2"
```
