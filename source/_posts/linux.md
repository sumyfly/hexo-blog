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

### 3. pkg-config
pkg-config一个帮助编译时指定编译依赖路径的工具，它可以避免硬编码参数。
pkg-config必须有一个.pc结尾的配置文件，读取这个文件产生指定值。
pkg-config如果需要指定用户的目录，需要导出PKG_CONFIG_PATH环境变量。这个变量会在pkg-config的默认搜索路径前生效。查看pkg-config的默认搜索路径`pkg-config --variable pc_path pkg-config`.

``` bash
pkg-config --libs libsodium //查找sodium的lib位置。
```
