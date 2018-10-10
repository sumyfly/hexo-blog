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
如果我在build.gradle中没有设置`maven { url "https://maven.google.com"}`,那么搜索com.android.support:appcompat-v7时，是到Android SDK下:`/Library/Android/sdk/extras/android/m2repository/com/android/support/`，没有到~/.gradle/caches/modules-2/files-2.1中，导致一直报错找不到这个依赖(jcenter没有这个包，所以没法下载，必须要google自己的maven库)。

> 如果设置了`maven { url "https://maven.google.com"}`,那么就不需要用Android SDK Manager下载suport包和buildTools，gralde会自动下载到自己的本地缓存中。

### 3. 实用命令
- 查找依赖: ./gradlew :app:dependencies --configuration compile
- clean: ./gradlew clean
- install debug: ./gradlew installDebug
- check: ./gradlew check
- dependencies: ./gradlew androidDependencies

### 4. Genymotion 2.12在mac 10.13.4上的安装错误
苹果系统更新了安全限制，virtual box安装不了。解决方案是安装时，**选择自定义安装路径**,这个时候去settings->security中允许安装来自Oracle,回到安装界面再选择自动安装。done.

做地图功能时，需要zoom in,zoom out，是按住鼠标右键，移动鼠标左键。
https://docs.genymotion.com/latest/Content/03_Virtual_Devices/Interacting_with_virtual_devices/Multi_touch_simulation.htm

Android Studio自带的模拟器，现在是速度快了，按时RN打印PixelRatio时，1920 * 1080打印了2.625,TODO:这个是什么原因，不是2，不是3，是2.625？

### 5. Android test
#### 5.1 查看依赖冲突
`./gradlew ./gradlew :app:dependencies --configuration _debugAndroidTestApk`,很有可能test apk用的库和普通的apk用的库版本不一致，出现版本冲突。修改app/build.gradle中的test部分。

``` groovy
    androidTestCompile ('com.android.support.test:runner:1.0.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    androidTestCompile ('com.android.support.test.espresso:espresso-core:3.0.2',{
        exclude group: 'com.android.support', module: 'support-annotations'
    })
```

#### 5.2 test的启动类
Android Stuido 3.2自带的测试apk启动类是`android.support.test.AndroidJUnitRunner`,如果是`com.android.support.test:runner:1.0.2`,那么就要指定新的启动类
``` groovy
    android {
      defaultConfig {
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
      }
    }
```
> 被@Text注解的方法不需要以'test'开头做前缀。

### 6 Androd Studio "no module"错误
导入工程后，File -> Sync Project With Gradle File。我发现了一个错误，testApplicationId不能与主ApplicationId一样。删掉这个就testApplicationId就可以了。
https://blog.csdn.net/cjm2484836553/article/details/69334941
