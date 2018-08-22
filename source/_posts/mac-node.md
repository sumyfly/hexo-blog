---
title: mac-node
date: 2018-01-12 15:58:34
tags:
  - mac
  - node
categories: mac
---

### 1. nvm
nvm的目录是`~/.nvm`

``` bash
nvm alias default 8.11.3 #使用node 8.11.3作为默认版本
```

### 1.1 特定node的目录
比如node 6.9.5的目录是
.nvm/versions/node/v6.9.5/lib/node_modules

### 2. yarn
npm模块的依赖，现在用`yarn`,它的缓存在~/.npm。
> 注意在安装nvm的情况下，不要再安装node了`brew install yarn --without-node

<!-- more -->
