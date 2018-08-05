---
title: Android使用JNI
date: 2018-08-05 18:34:11
tags:
- JNI
categories: Android
---

### 1.JNI 引用**.so
#### 1.1 放到main下面的JNILibs
按照硬件分文件夹放.so

### 1.2 修改build.gradle

``` groovy
android {
  sourceSets {
    main {
      jniLibs.srcDirs = ['libs']
    }
  }
}
```

### 1.3 注意ndk的编译选项
> 如果没有提供硬件平台就不需要选择，不然APP会到相应目录下寻找.so文件。
``` groovy
android {
    defaultConfig {
        // 指定支持的so架构
        ndk {
            abiFilters 'armeabi' //, 'x86', 'x86_64', 没有就不用选。
        }
    }
}
```

https://blog.csdn.net/ToBeTheOnlyOne/article/details/78491024