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
- splice 对数组中添加/删除项目，然后返回**被删除的项目**。这个会修改原数组。
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

### 3.null vs undefined
null, undefined都是primitive type.但是有个错误，typeof null === 'object'。

``` js
null == undefined // true
null === undefined // false
null ==== null
undefined === undefined
```

1. null表示一个属性定义了，但是有目的的设为null;undefined表示这个属性没有。
2. 一般初始化时设为null,然后判断时直接用=== null判断有没有设置值。
3. 函数参数要是没有实参，默认是undefined.
> When defining a variable that is meant to later hold an object, it is advisable to initialize the variable to **null** as opposed to anything else. That way, you can explicitly check for the value null to determine if the variable has been filled with an object reference at a later time

https://stackoverflow.com/questions/6604749/what-reason-is-there-to-use-null-instead-of-undefined-in-javascript

Summary
null is an assigned value. It means nothing.
undefined typically means a variable has been declared but not defined yet.
null and undefined are falsy values.
null and undefined are both primitives. However an error shows that typeof null = object.
null !== undefined but null == undefined.

https://codeburst.io/javascript-null-vs-undefined-20f955215a2

### 4. 三目运算赋初始值
我经常这么用` const a = b ? b : defaultValue`,这个其实不是很好的习惯。记住这里b的运算时及时布尔值，而可能我们需要的是undefined这种情况，像0,null,false这中我们给了值，但是是falsy的值，我们如果用了默认defaultValue,就会产生预料之外的情况，就可能有bug.
``` js
// !! 和 Boolean功能一样，但是React中用Boolean好一些，因为在jsx中用!!0会有bug.
// 下面都是primitive value
!!0 // false
!!null // false
!!undefined // false
!!'' // false

// []是个object,所以空数组也是true
!![] // true
!!{} // true

```