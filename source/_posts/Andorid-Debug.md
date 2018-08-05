---
title: Andorid-Debug
date: 2017-10-31 10:42:45
tags: 
  - Android
  - debug
categories: Android
---

### 1.gc overhead limit exceeded
react-native run-andorid 总是提示device not found, 但是adb devices 能发现模拟器。而且同时有gc overhead limit exceeded错误，并提醒gradle内存分配不够。我更新了genymotion，错误一样。后来按照提示在~/.gradle/gradle.properties中增加了
``` bash
org.gradle.jvmargs=-Xmx2048M
```
这样gradle就不报错了，执行react-native run-android成功安装到模拟器。
其实一开始按照提示来做，就不会有问题，只是我认为之前一直没有改gradle配置，也可以运行，并且./gradlew assembleRelease打包也没问题，只有debug有问题，所以认为是模拟器问题。最终还是gradle的问题，下次先按提示来改，这个提示还是正确的，并不是误报。
<!-- more -->

### 2.Gradle下载的依赖包位置
#### 2.1 下载包的位置
- Mac系统默认下载到：
/Users/(用户名)/.gradle/caches/modules-2/files-2.1
或
~/.gradle/caches/modules-2/files-2.1

- Windows系统默认下载到：
C:\Users\(用户名)\.gradle\caches\modules-2\files-2.1

- 或者在android studio中切换到project下，右键 -> Library Properties....

#### 2.2 com.android.support:appcompat-v7
如果我在build.gradle中没有设置`maven { url "https://maven.google.com"}`,那么搜索com.android.support:appcompat-v7时，E而是到Android SDK下:`/Library/Android/sdk/extras/android/m2repository/com/android/support/`，没有到~/.gradle/caches/modules-2/files-2.1中，导致一直报错找不到这个依赖。

> 如果设置了`maven { url "https://maven.google.com"}`,那么就不需要用Android SDK Manager下载suport包和buildTools，gralde会自动下载到自己的本地缓存中。