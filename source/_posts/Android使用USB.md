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
![USB2.0](/images/usb/Usb2.0.png)

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
#### 3.1 CDC - Communication Device Class 
在USB标准协议中，有一类专用于通讯设备（主要包括电信通信设备和中速网络通信设备）的CDC协议，USB的CDC类是 USB通信设备类（Communication Device Class Specification ）的简称。可以通过 USB CDC 协议来将 USB 接口虚拟为其他通讯接口如串口，以太网接口，ISDN 接口等等。根据 CDC 协议所针对通信设备的不同，CDC 协议又被分成以下不同的模型：
- USB 传统电话业务（POTS ）模型， 
- USB ISDN 模型
- USB网络模型。

CDC类可以实现虚拟串口通讯协议，而且由于大部分的操作系统（Windows,Linux)都带有支持CDC类的设备驱动程序，可以自动识别CDC类的设备，这样不仅免去了写专用设备驱动的负担，同时简化了设备驱动的安装。

https://wenku.baidu.com/view/3e2b630002020740be1e9b0f.html
https://blog.csdn.net/king_jie0210/article/details/76713938
https://blog.csdn.net/weijory/article/details/75570597

#### 3.1 相关USB转COM芯片
- CP21XX
- Ch34x
- Ftdi
- Prolific
