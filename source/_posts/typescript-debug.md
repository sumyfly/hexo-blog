---
title: typescript-debug
date: 2018-02-08 14:07:57
tags:
  - typescript
---

#### 1. 去掉转换后的exports
由于phantomjs发现不了exports，所以需要把转换后文件的第一行去掉。方法是在ts文件最上面加上`export = 0;`。
//issue: don't add Object.defineProperty(exports, "__esModule", { value: true }); 
//https://github.com/Microsoft/TypeScript/issues/14351

#### 2. noEmit
`"noEmit":true`这个配置就不会产生输出文件了。