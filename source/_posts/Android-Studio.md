---
title: Android-Studio
date: 2018-12-17 12:15:37
tags:
  - Android Studio
  - Mac
---
### 1.快捷键
#### 1.1 打开关闭side tool window
Ctrl + Cmd + F12

#### 1.2 toggle terminal
Alt + F12
<!-- more -->

### 2.设置
生成serialVersionUID
https://blog.csdn.net/anyanyan07/article/details/78721247

### 3.新建module
在项目中，打开File->New->New Module,这个方法创建的module在当前目录下面。如果想单独一个module文件夹，可以创建之后，把它移到项目的同级目录。然后修改settings.gradle,在其中指定路径。
``` groovy
include ':app', ':mylibrary', ':testlib'

include ':utils'
project(':utils').projectDir = new File(rootProject.projectDir,'../Utils')
```
application和library的区别
 - library中，没有settings.gradle.
 - 也没有app/src目录，直接就是src目录，
 - src下面也没有build.gradle.一共只有一个build.gradle.
 - build.gradle没有applicationId
 - AndroidManifest.xml中没有Application.


### Android Studio 格式化layout的xml的时，修改了顺序。
执行如下操作:

Settings -> Editor -> Code Style -> XML-> Set From -> Predefined Style > Android.

https://stackoverflow.com/questions/57685749/android-studio-3-5-formatting-ctrlaltl-the-code-in-xml-file-is-rearranging