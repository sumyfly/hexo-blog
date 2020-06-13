---
title: bash
date: 2019-02-13 15:49:47
tags:
  - Linux
  - bash
---

### 1. tr
echo $PATH | tr ':' '\n'

### 2. xargs
``` sh
find . -type f | xargs -I{} md5sum {} # {} 是用来代替管道之前标准输出内容的。
```
加 -I 参数 需要事先指定替换字符