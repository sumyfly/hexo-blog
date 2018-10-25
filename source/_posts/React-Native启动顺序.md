---
title: React-Native启动顺序
date: 2018-10-24 17:33:15
tags: 
  - react-native 
  - 原理
categories: React-Native
---

### 1. React Naitve启动顺(Android)
1. ReactActivity->onCreate()
2. MainApplication->mReactNativeHost.getPackages()
3. ScannerPackage->Scanner.initScannerModule
<!-- More -->
所以在ReactActivity->onCreate()中取不到ScannerModule.getInstance(),是异步加载Packages.

https://www.jianshu.com/p/af4cb096785b
ReactActivity--->
  ReactActivityDelegate--->
         createRootView->
          getReactNativeHost->
          createReactInstanceManager->ReactInstanceManagerImpl
                  recreateReactContextInBackground->
                     1.构造ReactContext->
                     2.加载CoreModulesPackage->
                     3.加载用户自定义Packages->
                     4.构造CatalystInstanceImpl->
                     5.runJSBundle
          setContentView(mReactRootView)