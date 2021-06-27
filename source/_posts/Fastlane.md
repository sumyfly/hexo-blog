---
title: Fastlane
date: 2021-06-27 22:12:10
tags:
---

### 1. CI绕过2fa,2sv

 2-factor authentication (2FA) :可以选一个可信设备，也可以选手机号。现在的AppleId如果涉及到开发，都是必须要启用2fa的。fastlane的工作机制是基于session，而session是通过cookie来标识的，所以如果遇到session过期，session不支持，那么首先考虑的就是新建一个session，而新建session的方法是通过刷新cookie。

legacy 2-step verification (2SV)：选一个手机号。

[fastlane CI](https://docs.fastlane.tools/best-practices/continuous-integration/#integrations)上面说有三种方法：

#### 1.1 API Key

这个是推荐方法，是从Apple Developer Center导出一个文件，作为Apple Store Connect API。缺点是不支持某些操作，比如下载dSYM文件，期待以后的版本能支持。
App Store Connect API是基于Http的连接，使用JWT认证。

#### 1.2  Two-step or two-factor authentication
```shell
fastlane spaceauth -u user@email.com
```

生成session后，可以把这个字符串保存到`FASTLANE_SESSION`中。

> 如果遇到Keychain没有权限，那么可以运行`security unlock-keychain $keychain_path`来暂时解锁keychain,解锁过后可以运行`security lock-keychain $keychain_path`来加锁，或者默认300秒后自动恢复加锁。

#### 1.3 Application-specific passwords

这个是Apple支持第三方登录后的一种方法，我们可以把CI build的机器当做一个第三方App，然后再Apple上生成一个specific password，这个specific password可以保持在环境变量`FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD`中，这个specific password可以生成一个session,这个同样需要刷新cookie。这个认证方法只适合上传.ipa文件，其它的都不适合，如果需要使用其它的功能需要使用另外的认证方法。

> 需要注意的是，这个方法需要把AppileID的登录密码也改为specific password一样，这个登录信息也保存在KeyChain中。

总结下：

- fastlane的Apple认证依赖的是session，而session依赖的是cookie(为了保持向前兼容性)，保存位置在（$HOME/spaceship/$APPLE_ID/cookie)。上面的1.2，1.3是用session的方式，都是使用了cookie。而1.1是用的JWT，不使用cookie. 

- 用户名密码是保存在Keychain中，它是以'deliver'开头，后面接一个邮箱名，所以它只能被Fastlane使用。证书也是保存在Keychain中。

Chrome保存的用户名密码不在Keychain中，Safari保存的用户名密码在Keychain中。

所以说，API Key用的是JWT，不需要登录信息和session。而1.2,1.3的方式都需要登录（登录账号信息在Keychain中），1.2在session无效期间需要2FA。1.1和1.3不需要2FA。

<!-- more -->

### 2. spaceship刷新session

先用`fastlane spaceship`,然后输入appleId，如果有2FA,输入短信验证码。之后在目录`~/.fastlane/spaceship/${appleId}/`下面有`cookie`文件，这个文件的有效期是一个月，所以到了时间要手工地刷新。刷新的方式就是重新运行`fastlane spaceship`。

### 3. download_dsyms

需要mkdir downloads

``` sh
fastlane run download_dsyms username:qianshenglong@hotmail.com app_identifier:com.storehub.beep team_id:X78BF7H5HG version:1.7.1 build_number:5 output_directory:downloads
```

如果碰到需要2FA输入验证码，输入一次，但是通常情况下CI下是无法输入的，所以一定会失败，这种情况下需要去运行`fastlane spaceship`，刷新下token。



### 4. Certificate

创建开发或发布证书，首先登录账号，然后新建证书。

XCode->Perferences->Accounts->Select One Team->Manage Certificaates->Left Button + Plus Button->Apple Development or Distribution 