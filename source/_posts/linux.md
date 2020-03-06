---
title: linux
date: 2019-03-14 23:28:11
tags:
 - linux
---

### 1. ^M不可见字符
vim中输入CTRL+V CTRL+M, 而不是上箭号+M。
https://blog.csdn.net/kkwant/article/details/81501241
<!-- more -->

### 2. 启动本地TCP服务器

#### 2.1 nc
Server listens on port 8000:

$ nc -l 8000
Client connects to port 8000:

$ nc localhost 8000 

#### 2.2 socat

socat TCP-LISTEN:6142,fork stdout



