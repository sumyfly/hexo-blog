---
title: mac
date: 2021-03-31 11:06:06
tags:
  - mac
---

### 1. 目录变成黄色
那是因为目录权限变为任何人都可以读写，去掉这个权限使用`chmod go-w $directory_name`
``` bash
ls | xargs chmod -R go-w
```
<!--more-->
