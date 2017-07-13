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
