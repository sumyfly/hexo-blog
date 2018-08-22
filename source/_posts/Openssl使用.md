---
title: Openssl使用
date: 2018-08-22 15:54:39
tags:
---

### 1.查看private key
openssl pkcs8  -in my_private_key -passin mypassworld
openssl pkcs12  -info -in my_private_key -passin mypassworld
> 注意不同的加密格式对应不同命令

<!-- more -->