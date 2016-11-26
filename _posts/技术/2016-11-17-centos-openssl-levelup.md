---
layout: post
title: CentOS升级openssl为最新版
category: 技术
keywords: linux
---

```
wget http://www.openssl.org/source/openssl-1.1.0c.tar.gz 
tar zxvf openssl-1.1.0c.tar.gz
cd openssl-1.1.0c
./config --prefix=/usr/local/openssl
make && make install
mv /usr/bin/openssl /usr/bin/openssl.OFF
mv /usr/include/openssl /usr/include/openssl.OFF
ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
ln -s /usr/local/openssl/include/openssl /usr/include/openssl
echo "/usr/local/openssl/lib">>/etc/ld.so.conf
ldconfig -v
openssl version -a
```
