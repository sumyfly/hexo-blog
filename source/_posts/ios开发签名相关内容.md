---
title: iOS开发签名相关内容
date: 2017-10-18 09:26:55
tags:
  - iOS
  - 签名
---

### 1. XCode的工程相关名词解释

#### 1.1 workspace
这个是放所有文件的地方，里面有多个project

#### 1.2 project
管理一个项目的地方，里面有多个scheme

#### 1.3 scheme
管理一个target,生成一个产品（APP)，勾选Share之后才能共享给他人使用。

#### 1.4 target
一个管理编译设置和文件引用的配置（.plist文件)，继承project的设置，可以根据需要改写

#### 1.5 provisioning profiles
该文件将App ID、开发者证书、设备绑定到一块儿，在开发者中心配置好后可以添加到Xcode上，也可以直接在Xcode上连接开发者中心生成，真机调试时需要在PP文件中添加真机的UDID。

#### 1.6 开发环境 生产环境 
默认xcode带有debug和release Configurations(Project->Info),最大区别就是宏Build Settings->Preprocessor Macros DEBUG=1 为DEBUG，即开发环境,否则是生产环境。其它configuration都是duplicate自这两个，还是看这个宏。

<!-- more -->
参考：
http://www.wenghengcong.com/2016/07/iOS%E8%AF%81%E4%B9%A6%E4%B8%80%E8%A7%88/
https://www.jianshu.com/p/83b6e781eb51

### 2. 签名相关

#### 2.1 个人开发者和企业开发者
申请证书是绑定设备的，只能那台申请的电脑有证书，其它人使用只能用p12。
个人开发者或公司开发者可以有2个开发者证书，三个发布者证书。最大app上架数100。`99$一年。
企业开发者证书数量不知道，不可以上架app store，但是可以发布in-house.299$一年。`

#### 2.2 开发证书，生产证书
下面以个人或公司账号为例。开发证书只能开发，生产证书可以开发也可以发布。同一个发布证书可以发布不同APP，只要它们具有不同的bundle id。

常用的证书类型：
  * iOS App Development。开发、真机调试用
  * Apple Push Notification service SSL (Sandbox)。开发阶段使用苹果的推送服务
  * App Store and Ad Hoc。上架和AdHoc方式发布时用
  * Apple Push Notification service SSL (Production)。上架后使用苹果推送服务
  * In-House。企业版发布，需$299才能拥有，还需邓氏编码

#### 2.3 推送
推送需要Apple Push Notification service SSL (Sandbox)或者Apple Push Notification service SSL (Production)证书。这个证书绑定了一个bundle id,我不知道最多可以申请几个APNS证书，APNS证书和签名证书没关系，相互独立。
iOS只是认bundle id，所以签名打包后，只要bundle id没变，APNS 证书可以更换，而不需要重新签名打包。
极光推送需要p12文件，这个可以由上述的开发环境推送证书或者生产环境推送证书导出，对应相应的环境。但是用生产环境推送证书导出比较方便，可以用于开发也可以用于生产。TestFlight 用的是ad-hoc,打包签名是Distribute证书，环境是生产，最大设备数量100。

#### 2.4 .mobileprovison
.mobileprovision : 对应app 的配置文件。Xcode 其实最终也是需要 这个文件 来进行 对应的app 打包。这个是苹果认证中心签名，个人无法签名，里面存储了可以安装的设备ID,这些设备需要事先添加到苹果MC（Member Center）的Devices里面。对于开发时候的真机调试，原理差不多。都是通过mobileprovision的条目4来做到的。而苹果对于调试和测试用机的数量限制为100台！

#### 2.5 .entitlements
一个target对应一个.entitlements,这个是推送权限相关的.推送必须有这个文件，不然App Store审核不通过。
``` xml
<dict>
	<key>aps-environment</key>
	<string>development</string>
</dict>
```
这里的aps-enviroment，网上说发布时不需要改成production，会自动更改, 参考http://www.jianshu.com/p/3bb79bdaf87a。

### 3.自动签名
自动签名使用team，就不用去走手动签名的过程，只要打包之前，去Perferences->Accounts->Download Manual Profiles.这样就可以了。
Archive之后，Export的四个选项，App Store, Ad Hoc都需要dispatch 证书，Ad Hoc的环境是和App Store的环境一模一样，是上App Store前的准备，TestFlight可以直接移动过去提交App Store. Enterpise是企业证书，发布In-House时使用。Development只需要Developer证书。

### 4.手动签名
#### 4.1导入证书
先创建一个CSR，
去苹果管理中心，提交CSR，创建开发者证书
下载开发者证书，双击导入的KeyChain
#### 4.2下载profile
去苹果管理中心，创建一个application id
去profile管理页面里生成一个profile(刚创建的application id)
下载这个profile，双击安装到xcode
在Build Settings->Singing中的Provision Profile中选择你的profile, Code Signing Identity也选择profile中的证书

> Profile中有三个东西: 证书，设备，application id(bundle id), 这样设备就可以下载这个ipa运行了。这个文件打包后在.ipa->.app->embedded.mobileprovision。
