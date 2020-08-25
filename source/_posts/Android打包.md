---
title: Android打包
date: 2020-08-25 15:11:47
tags:
  - Android
---

### 1. dalvik

smali/baksmali is an assembler/disassembler for the dex format used by dalvik, Android's Java VM implementation.
smali 芬兰语中的编译
baksmali 芬兰语中的反编译
dex 文件是组装 smali 后文件格式

### 2. apk 反编译

#### 2.1 apk/dex -> Jar -> Java

- apktool 可以把 apk 中的 AndroidManifest.xml 和资源文件取出。apktool 是对 apk 进行解包和重新打包时使用的更通用的工具。它实际上在引擎盖下使用 smali/baksmali 来组装/反汇编 dex 文件。它还将二进制资源和二进制 xml 文件解压回标准文本格式等。
- dex2JAR 可以把 apk/dex 反编译成 Jar(baksmali -> smali -> classcode)
- jd-gui/jd-core 可以把 JAR 反编译成 Java

> jad 不同于 jd-core，jd-core 是使用 Java 实现的，jad 是使用 C++实现的。jad 是反编译 classcode 文件，反编译效果好于 jd-core，但是不支持 JAR，只针对 class 文件。

#### 2.2 apk/dex -> Java

jadx 可以直接反编译 apk/dex 到 Java 文件和资源文件，jadx-GUI 可以可视化操作。

<!-- more -->

### 3. JDE

可以反编译，可以 debug，收费软件。
