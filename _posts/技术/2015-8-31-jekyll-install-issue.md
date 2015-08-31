---
layout: post
title: jekyll安装的几个问题
category: 技术
keywords: jekyll
---

## 安装过程
参照这个链接——<a href="http://blog.csdn.net/kong5090041/article/details/38408211" target="_blank">Windows上安装Jekyll</a>

## gem install jekyll 网络错误

由于国内网络原因（你懂的），导致 rubygems.org 存放在 Amazon S3 上面的资源文件间歇性连接失败。所以你会与遇到 gem install rack 或 bundle install 的时候半天没有响应，具体可以用 gem install rails -V 来查看执行过程。

```
$ gem sources --remove https://rubygems.org/
$ gem sources -a https://ruby.taobao.org/
$ gem sources -l
*** CURRENT SOURCES ***

https://ruby.taobao.org
# 请确保只有 ruby.taobao.org
$ gem install jekyll
```

## hitimes-require-error-when-running-jekyll-serve-on-windows-8-1

这个问题由于hitimes和ruby版本不对应

```
gem uni hitimes

**Remove ALL versions**

gem ins hitimes -v 1.2.1 --platform ruby
```

## 再试试吧

```
cd myblog
$ jekyll serve
# => Now browse to http://localhost:4000
```

