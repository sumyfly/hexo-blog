---
title: ubuntu
date: 2018-04-09 10:30:04
tags:
 - ubuntu
---

### 1.mysql-server升级
我用系统升级的提示框升级了，看到了升级了mysql-server到5.7,然后apt-get autoremove时，发现mysql-server的配置错了，在`/etc/mysql/`下有一个`debian.cnf`的配置文件，这个是mysql的维护用户，可以用来做profile的。按照里面的配置去mysql的数据库更新下密码就正常了。

> 注意mysql的密码插件的强度提示，可以手工降低后再修改密码。
``` bash
# 首先登陆mysql命令行
mysql -u root -p
# 输入密码登陆后执行以下两个命令
set global validate_password_policy=0;  
# 修改密码sys-debian-maint(maint就是maintenance缩写)
GRANT ALL PRIVILEGES on *.* TO `debian-sys-maint`@`localhost` IDENTIFIED BY 'your password' WITH GRANT OPTION;
```

- https://segmentfault.com/a/1190000000412194
- https://www.jianshu.com/p/1ec04ecbb690
- [StackOverflow](https://serverfault.com/questions/9948/what-is-the-debian-sys-maint-mysql-user-and-more?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)

之后还是手工升级保险一点。
<!-- more -->

### 2. Ubuntu16.04升级内核
原因是系统自动升级4.4内核时失败了，我还得重新设置EasyBCD启动（Widows7+Ubuntu）。我想安装Docker，看到Overlay2需要kernel4+版本支持，所以升级4.6.2。内核不是最新就最好，而是稳定最好。
升级内核一般按照CPU是64位还是32位下载相应.deb文件，有2个headers,一个image，headers是因为可以从源码编译安装软件时需要。下载好之后安装重启即可。
```bash
uname -ar # 查看当前kernel
```