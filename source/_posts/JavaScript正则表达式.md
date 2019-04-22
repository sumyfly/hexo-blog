---
title: JavaScript正则表达式
date: 2019-04-22 16:25:47
tags:
- JavaScript
- 正则表达式
---

### 1. 正则表达式的修饰符
#### 1.1 /g
/g修饰符代表全局匹配, 查找所有匹配而非在找到第一个匹配后停止。
注意string.match和RegExp.exec的区别，前者的正则表达式不需要加上/g，而后者需要加上/g。
> exec with a global regular expression is meant to be used in a loop, as it will still retrieve all matched subexpressions. So:
``` js
var re = /[^\/]+/g;
var match;

while (match = re.exec('/a/b/c/d')) {
    // match is now the next match, in array form.
}
// No more matches.
```
String.match does this for you and discards the captured groups.

<!-- more -->

https://javascript.info/regexp-groups
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions
https://stackoverflow.com/questions/9214754/what-is-the-difference-between-regexp-s-exec-function-and-string-s-match-fun