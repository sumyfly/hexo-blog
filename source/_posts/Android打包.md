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
dex文件是组装smali后文件格式: dx工具会把class转为smali，dexdump查看dex中的smali。

Android Dalvik虚拟机
Java虚拟机运行的是Java字节码，Dalvik虚拟机运行的是Dalvik字节码
Dalvik虚拟机通过解释DEX来执行这些字节码

Dalvik可执行文件体积更小
dx工具负责将Java字节码转换位Dalvik字节码

Java虚拟机与Dalvik虚拟机架构不同
Java虚拟机基于栈架构，Dalvik虚拟机基于寄存器架构

``` sh
$ javac -source 1.6 -target 1.6 Hello.java
$ dx --dex --output=Hello.dex Hello.class
$ javap -c -classpath . Hello # 查看Java字节码
$ dexdump -d Hello.dex # 查看Dalvik字节码
```

dx和dexdump在/sdk/build_tools/android目录下
<!-- more -->

### 2. apk反编译
#### 2.1 apk/dex -> Jar -> Java
- apktool可以把apk中的AndroidManifest.xml和资源文件取出，dex被反编译成smali代码。apktool是对apk进行解包和重新打包时使用的更通用的工具。它实际上在引擎盖下使用smali/baksmali来组装/反汇编dex文件。它还将二进制资源和二进制xml文件解压回标准文本格式等。
- dex2JAR 可以把apk/dex反编译成Jar(baksmali -> smali -> class)
- jd-gui/jd-core可以把JAR反编译成Java

> jad不同于jd-core，jd-core是使用Java实现的，jad是使用C++实现的。jad是反编译class文件，反编译效果好于jd-core，但是不支持JAR，只针对class文件。

#### 2.2 apk/dex -> Java
jadx可以直接反编译apk/dex到Java文件和资源文件，jadx-GUI可以可视化操作。



### 3. JDE
可以反编译，可以debug，收费软件。
