---
layout: post
title: AES对称加密算法简介
category: 技术
keywords: 密码
---

## 概述

&#160; &#160; &#160; &#160;密码学中的高级加密标准（Advanced Encryption Standard，AES），又称Rijndael加密法，是美国联邦政府采用的一种区块加密标准。

&#160; &#160; &#160; &#160;这个标准用来替代原先的DES（Data Encryption Standard），已经被多方分析且广为全世界所使用。经过五年的甄选流程，高级加密标准由美国国家标准与技术研究院 （NIST）于2001年11月26日发布于FIPS PUB 197，并在2002年5月26日成为有效的标准。2006年，高级加密标准已然成为<font color="blue">对称密钥</font>加密中最流行的算法之一。

## 几个重要名词解释

1、AES128和AES256：AES128和AES256主要区别是密钥长度不同（分别是128bits,256bits)、加密处理轮数不同（分别是10轮，14轮），后者强度高于前者。

2、明文块：把明文按照密钥长度分为若干块。

3、Padding：AES支持支持几种充：NoPadding，PKCS5Padding，ISO10126Padding，PaddingMode.Zeros，PaddingMode.PKCS7。一般情况下，PKCS5Padding和PKCS7Padding是一样的。

4、PKCS7：就是数据少几个就填充几个几。

<img src="/assets/img/0050.jpg">

5、Zeros：缺多少就补多少个0。

6、ECB模式：对每一块进行加密后组合。

<img src="/assets/img/0051.jpg">

7、CBC：对每一块先使用向量进行异或，然后再加密，加密后的数据作为下一块的向量继续对下一块进行异或。

<img src="/assets/img/0052.jpg">
