---
title: React-Native集成module
date: 2018-08-25 08:56:06
tags:
- react-native
- debug
categories: React-Native
---

### 1.集成react-native-maps
集成谷歌地图，分两步，客户端集成与Google Api设置。难点是Google Service的版本统一，iOS集成，Google Api设置。

- Android的构建工具是gradle，所以集成还算方便，最多改下buildToolsVersion, targetVersion, Google Service使用一个全局变量来统一版本。
- iOS可以手动集成，也可以用cocoaPods，难点在于同时使用这两个。react-native-maps我是用pod，创建PodFile后运行`pod install`,使用.xcworkspace打开，pod所有的依赖都打包到一起。
- Google Map APi设置，需要分Android和iOS两个，开发环境也要分成2个，所以一共要4个。

### 1.集成react-native-google-signin
一开始我看最新版本是需要Google Service15，我想兼容性好用Google Service的11.8.0版本。所以一开始用了它的0.11版本。
- Android上改了module的buildToolsVersion，把compileOnly,implementation改成compile.Java文件中@Override
Android需要下载Google-Service,这个是Firebase导出的，事实上我们用不到Firebase。Firebase-core是analytics的别名，它的大部分功能是用来统计数据的，比如ads,crashlytics,google analtytics。Firbase-auth是可以支持更多的登录，跟Google-signin不一样。
- iOS集成，我之前用cocoaPods安装了react-native-maps,一开始我手动集成react-native-google-signin，发现Arguments too long,按照FaceBook的说法是Header Search Paths里面的pod路径和手工路径重复了，可是我没有重复的;使用pod集成，找不到各种文件。
- Google Signin Api跟Map Api差不多，唯一不同的是需要Android的packageName和SHA1指纹，iOS需要bunldeId。

如果再次引入其他包，可以主项目引入，然后加上Header Search Paths.iOS需要**头文件和lib一起工作**，如果主项目用到了头文件或者Framework用到了头文件，那么头文件路径需要设置（Framework可以到.framework文件夹，自定义头文件的需要到头文件文件夹路径）。

解决方案: 使用了最新的1.0.0-rc3，然后iOS就可以了，Android把Google Service的版本降低了。下次集成先集成iOS，Android相对容易些。

<!-- more -->
