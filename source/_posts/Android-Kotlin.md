---
title: Android-Kotlin
date: 2018-07-12 11:14:34
tags:
  - Android
  - Kotlin
categories: Android
---

### 1.APT
#### 1.1 定义
APT英文全称：Android annotation process tool是一种处理注释的工具，它对源代码文件进行检测找出其中的Annotation，使用Annotation进行额外的处理。
Annotation处理器在处理Annotation时可以根据源文件中的Annotation生成额外的源文件和其它的文件（文件具体内容由Annotation处理器的编写者决定），APT还会编译生成源文件和原来的源文件，将它们一起生成class文件。简言之：APT可以把注解，在编译时生成代码。

### 2.2 注解框架
- ButterKnife 
BindView绑定View,不需要findViewById了。

- dagger
Binds等一些类注释工具

### 2.3 kapt
Kotlin的apt工具

- dagger
``` groovy
    compile "com.google.dagger:dagger:$dagger_version"
    kapt "com.google.dagger:dagger-compiler:$dagger_version"
```

- butterknife
``` groovy
    compile "com.jakewharton:butterknife:$butterknife_version"
    kapt "com.jakewharton:butterknife-compiler:$butterknife_version"
```

https://www.jianshu.com/p/94979c056b20
https://blog.csdn.net/Ru_Zhan/article/details/78232851?locationNum=8&fps=1

[butterknife vs dagger](https://blog.csdn.net/geekpark/article/details/38867873)

<!-- more -->