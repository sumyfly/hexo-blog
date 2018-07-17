---
title: JavaScript
date: 2018-07-17 16:27:17
tags:
---
### 1.循环
``` js
// 1. for 普通循环
for(let i= 0; i < length; i++){

}
// 缺点，不能break,continue

// 2. forEach
Array.forEach
// 缺点： 只对数组

// 3. for of, 最新添加的功能，推荐使用
for(let o of arr){

}

// 4. for in, 这个是用来遍历属性的，不推荐遍历数组。这个会把原型的属性也遍历。
// TODO: check? 应该还是可遍历属性。
for (let p in objectA){

}

> JS其实没有数组，都是对象。
```
所以，应该优先使用for of,其次for 普通的循环。
<!-- more -->