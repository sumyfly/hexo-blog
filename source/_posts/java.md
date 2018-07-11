---
title: java
date: 2018-07-11 12:21:13
tags:
---

### vert.x启动慢，线程被阻塞
原因是sierra解析主机名时间过长，在/etc/hosts设置主机名解析就好了。

https://thoeni.io/post/macos-sierra-java/

<!-- more -->