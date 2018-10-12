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

#### 3.2 相关USB转COM芯片
- CP21XX
- Ch34x
- Ftdi
- Prolific

### 4. Android usb通信
#### 4.1 串口
Android与串口设备通信的方案有四种：

- 第一种是直接用SDK的BluetoothSocket类来进行蓝牙通信，外部设备再用蓝牙转串口进行控制。这种方式有较高延时，蓝牙模块需要供电，低带宽。
- 第二种是使用USB转RS232方式（使用内核驱动和使用Android驱动两种方式），这种方式不需要硬件改动，不需要另外的供电，延时很小且有较高带宽。但是Android设备需要硬件上支持USB Host接口（一般Android平板支持，Android手机一般是没有的），另外可能需要root以改变/dev/ttyUSB0文件权限来加载一个内核模块。开发需要使用android_serialport_api。
- 第三种是最容易的方案，直接使用串口进行连接，但是这种方式兼容性不好，只有少数设备支持，而且串口不支持流控制(由Android提供的USB Host API决定的TODO: 串口在/dev/tty下，并不能遍历到/dev/bus/usb或/dev/usb，这里存疑。)。使用时也用android_serialport_api。
- 第四种是将Android作为USB从机，外部设备作为USB主机与之通信，这种方式几乎与所有Android设备兼容（一般都有USB从口），无需root，低延迟，高带宽。

#### 4.1 USB直连
- HID，输入设备。
- CDC直接通讯

https://blog.csdn.net/sunjing_/article/details/51889801