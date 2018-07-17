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
// 可以有brek, continue
// 缺点: 这个最慢

// 2. forEach
Array.forEach
// 缺点: 只对数组有效,不能break,continue

// 3. for of, 最新添加的功能，推荐使用
for(let o of arr){

}
// 效率比较高

// 4. for in, 这个是用来遍历属性的，不推荐遍历数组。这个会把原型的属性也遍历。
// TODO: check? 应该还是可遍历属性。
for (let p in objectA){

}

> JS其实没有数组，都是对象。
```
所以，应该优先使用for of,其次for 普通的循环。
<!-- more -->

### 2. slice splice
- slice 从数组返回选定的元素,比如浅拷贝slice(0),返回的也是一个数组。
>arrayObject.slice(start,end)
- splice 对数组中添加/删除项目，然后返回被删除的项目。这个会修改原数组。
>arrayObject.splice(index,howmany,item1,.....,itemX)
> 这两个都是返回被选定的from start to end，或者length长度的数组。不是返回修改后的数组,也不是参数。

### 2. Array.push
js 的push 注意返回值
```js
let arr = [1, 2]

const b = arr.splice(1,1,3) // 错误, arr.splice(1,1,3)返回的start=1,end
// 应该是
// arr.splice(1,1,3)
// b = arr

const c = b.push(4) // 这里push返回的不是数组，是传的参数，这个不是链式调用
//正确的调用
// b.push(4)
// c = b
```

Immutable.List和原生的数组是有区别，前者可以链式调用。而且splice还**不修改**原List.
```js
const list = List.of('a', 'b', 'c', 'd')
const r = list.splice(1, 1, 'f').splice(2, 1, 'g')
console.warn('result', r.toJS(), list.toJS())
// result [ 'a', 'f', 'g', 'd' ] [ 'a', 'b', 'c', 'd' ]
```