---
title: Android使用JNI
date: 2018-08-05 18:34:11
tags:
  - Android
  - JNI
categories: Android
---

### 1.JNI 引用**.so
#### 1.1 放到main下面的jniLibs
在app/src/main/jniLibs下按照硬件分文件夹放**.so(jniLibs好像大小写不分)

### 1.2 修改build.gradle
在项目目录下的app/libs下按照硬件分文件夹放**.so
``` groovy
android {
  sourceSets {
    main {
      jniLibs.srcDirs = ['libs']
    }
  }
}
```
https://blog.csdn.net/shengyingpo/article/details/51538444

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

### 2. Android Studio
#### 2.1智能提示
有时候第三方的jar包放到libs下时，不会智能提示，gradlew clean也没有用，这个时候就是之前设置有过问题，去File->Invalidate Caches/Restart -> Invalidate and Restart, 然后就修复了。
https://blog.csdn.net/Sailor_luo/article/details/80277695

https://blog.csdn.net/ToBeTheOnlyOne/article/details/78491024