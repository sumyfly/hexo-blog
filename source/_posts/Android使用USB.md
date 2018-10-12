---
title: Android使用USB
date: 2018-10-11 13:47:10
tags:
  - Android
  - USB
categories: Android
---
### 1. USB2.0标准
USB2.0标准的子类。
![USB2.0](/images/usb/usb2.0.png)

<!-- more -->

### 2. USB 驱动
#### 2.1 Linux 分为内核驱动，libusb无驱驱动。
内核驱动里面包含USB标准的一部分驱动，如HID。
无驱驱动意思是直接读取数据，处理数据，发送数据。
第三方驱动也是以模块的方式安装到模块(安装到内核)。

#### 2.2 Windows 有内核驱动，第三方驱动。
第三方驱动需要安装驱动软件，应该是安装到内核。
Windows的无驱驱动，跟Linux的概念不太一样，它的驱动是Windows操作系统自带的，如键盘，无驱摄像头。

### 3. USB 转 COM
CDC: USB通信设备类（Communication Device Class)的简写。它是USB标准的一个子类。
CDC类可以实现虚拟串口通讯协议，而且由于大部分的操作系统（Windows,Linux)都带有支持CDC类的设备驱动程序，可以自动识别CDC类的设备，这样不仅免去了写专用设备驱动的负担，同时
https://wenku.baidu.com/view/3e2b630002020740be1e9b0f.html
https://blog.csdn.net/king_jie0210/article/details/76713938
