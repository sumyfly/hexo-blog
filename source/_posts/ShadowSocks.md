---
title: ShadowSocks
date: 2019-02-11 18:40:33
tags:
- proxy
- shadowsocks
---

### 1. ShadowSocks
ShadowSocks是Socks5代理。之前我在Bandwagon上有个OpenVZ的ShadowSocks代理，后来到期了，买了一个KVM的虚拟机，手工装了ShadowSocks.
网上有一键安装ShadowSocks的脚本，但是不支持CentorOS,只支持Debian和Ubuntu。我想用BBR加速，自带的镜像只有CenterOS有BBR，所以就手动安装。难点是Python的版本问题，CenterOS的默认自带Python时2.6版本，而ShadowSocks是要2.7版本。而如果升级了python，yum就使用不了，方案就是升级python，软连接python，然后yum还是使用python2.6，这样就不冲突。

#### 1.1 CenterOS升级python为2.7
https://www.cnblogs.com/haoorhuai/p/9773628.html

安装python使用的是源码编译
``` bash
yum -y update
yum groupinstall 'Development Tools'
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel expat-devel

wget https://www.python.org/ftp/python/2.7.15/Python-2.7.15.tgz
tar -zxvf Python-2.7.15.tgz 
cd Python-2.7.15
./configure --prefix=/usr/local
make all
make altinstall
make clean
```

不用使用easy_install安装python,使用官方自己带脚本。
``` bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python get-pip.py --user
```

安装结束后使用pip2命令，不要使用pip。
``` bash
pip2 install shadowsocks
```

#### 1.2 安装ShadowSocks
https://www.linuxidc.com/Linux/2015-05/117290.htm

``` bash
pip2 install shadowsocks
touch /etc/shadowsocks.json 
vi /etc/shadowsocks.json 
```
{ 
"server":"138.128.208.158", 
"server_port":443, 
"local_address": "127.0.0.1", 
"local_port":1080, 
"password":"MyPass", 
"timeout":600, 
"method":"rc4-md5"
}
备注：加密方式官方默认使用aes-256-cfb，推荐使用rc4-md5，因为 RC4比AES速度快好几倍。
各字段说明：

    server:服务器IP
    server_port:服务器端口
    local_port:本地端端口
    password:用来加密的密码
    timeout:超时时间（秒）
    method:加密方法，可选择 “bf-cfb”, “aes-256-cfb”, “des-cfb”, “rc4″等

使用配置文件在后台运行shadowsocks服务
[root@localhost /]# ssserver -c /etc/shadowsocks.json -d start
备注：若无配置文件，在后台可以使用一下命令运行：
[root@localhost /]# ssserver -p 443 -k MyPass -m rc4-md5 -d start
停止服务
[root@localhost /]# ssserver -c /etc/shadowsocks.json -d stop

### 2. BBR
Google的TCP BBR拥塞控制算法解析，只能内核4.9以上支持。
``` bash
#   查看内核版本，含有 4.9 就表示 OK 了
uname -r
```
https://www.cnblogs.com/Eason1024/p/8177665.html
https://blog.csdn.net/dog250/article/details/52830576
