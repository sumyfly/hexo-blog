---
title: ios开发签名相关内容
date: 2017-10-18 09:26:55
tags:
  - ios
  - 签名
---

###1. XCode的工程相关名词解释

####1.1 workspace
这个是放所有文件的地方，里面有多个project

####1.2 project
管理一个项目的地方，里面有多个scheme

####1.3 scheme
管理一个target,生成一个产品（APP)

####1.4 target
一个管理编译设置和文件引用的配置（.plist文件)，继承project的设置，可以根据需要改写

<!-- more -->

###2. 签名相关

####2.1 个人开发者和企业开发者
申请证书是绑定设备的，只能那台申请的电脑有证书，其它人使用只能用p12。
个人开发者或公司开发者可以有2个开发者证书，三个发布者证书。最大app上架数100。99$一年。
企业开发者证书数量不知道，不可以上架app store，但是可以发布in-house.299$一年

####2.2 开发证书，生产证书
下面以个人或公司账号为例。开发证书只能开发，生产证书可以开发也可以发布。同一个发布证书可以发布不同APP，只要它们具有不同的bundle id。但是只有一个bundle id可以接受App Push Notification Service。也就是最大可以有三个APP具有推送功能。

常用的证书类型：
  * iOS App Development。开发、真机调试用
  * Apple Push Notification service SSL (Sandbox)。开发阶段使用苹果的推送服务
  * App Store and Ad Hoc。上架和AdHoc方式发布时用
  * Apple Push Notification service SSL (Production)。上架后使用苹果推送服务
  * In-House。企业版发布，需$299才能拥有，还需邓氏编码

####2.3 推送
极光推送需要p12文件，这个可以有开发证书或者生产证书导出，对应相应的环境。但是用生产证书导出比较方便，因为用TestFight时，是用的生产证书。开发证书只能对应100个设备。

####2.4 .mobileprovison
.mobileprovision : 对应app 的配置文件。Xcode 其实最终也是需要 这个文件 来进行 对应的app 打包。这个是苹果认证中心签名，个人无法签名，里面存储了可以安装的设备ID,这些设备需要事先添加到苹果MC（Member Center）的Devices里面。对于开发时候的真机调试，原理差不多。都是通过mobileprovision的条目4来做到的。而苹果对于调试和测试用机的数量限制为100台！

####2.5 .entitlements
一个target对应一个.entitlements,这个是推送权限相关的.推送必须有这个文件，不然App Store审核不通过。
``` xml
<dict>
	<key>aps-environment</key>
	<string>development</string>
</dict>
```
这里的aps-enviroment，网上说发布时不需要改成production，会自动更改, 参考http://www.jianshu.com/p/3bb79bdaf87a。