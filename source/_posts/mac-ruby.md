---
title: mac-ruby
date: 2018-08-22 15:54:59
tags:
  - ruby
  - mac
categories: mac
---

### 1.rvm
管理不同的ruby版本。
``` bash
rvm list #列出不同的ruby版本
rvm use 2.4.1 --default #使用ruby 2.4.1做为默认版本
```
### 2.gem
管理ruby的库文件。
``` bash
gem install cocoapods #安装cocoapods到全局环境
gem environment # gem的环境，看gem的安装目录
gem list --local #看gem中库的版本，如果看到一个库有2个不同版本，说明在2个路径都可以查到，比如/usr/local/bin和$HOME/.rvm/gems/ruby-2.4.1/bin/bundler
```

https://blog.csdn.net/u014621915/article/details/62221705

<!-- more -->

### 3.bundler
管理gem的依赖关系，使用Gemfile和Gemfile.lock。
先在Gemfile中写好需要安装的gem的版本比如
``` ruby
source 'https://rubygems.org'
gem 'cocoapods', '~> 1.5', '>= 1.5.3'
```
然后执行`bundler install`
``` bash
bundler install # 从Gemfile.lock中安装gem
```

### 3.cocoapods
这个是管理iOS的第三方依赖包的，管理文件是Podfile。
``` bash
pod install # 安装第三方库，安装之后使用.xcworkspace打开项目。不要使用.xcodeproj
```