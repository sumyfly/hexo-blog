---
title: mac-apache2
date: 2017-07-13 10:32:00
tags:
  - mac
  - web
  - apache2
---

在Mac上使用apche2
==============

### 1.基本命令
Mac上自带apache2，它的安装目录是`/etc/apach2`， 文档目录在`/Library/WebServer/Documents/`

``` bash
#启动apche2 由于80端口需要管理者权限
sudo apachectl start
#关闭
sudo apachectl stop
#重启
sudo apachectl restart
```

<!-- more -->

### 2.设置用户配置
https://medium.com/@JohnFoderaro/how-to-set-up-apache-in-macos-sierra-10-12-bca5a5dfffba
