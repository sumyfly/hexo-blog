---
title: js原型
date: 2018-04-12 14:26:17
tags:
  - javascript
---
### 1.构造函数
``` js
function Foo(a){
  if(!(this instanceof Foo)){
    //非当前实例，原因是没用new去调用
    return new Foo(a)
  }
  this.name = a
}
var foo = new Foo('foo') //这里加不加new一样，但是推荐加，不能保证每个人写的构造函数都会check instance
```
我记得构造函数首字母要大写，但是发现现在不大写也可以，最好大写吧。
new 的作用：
- 绑定了this
- 返回一个对象
- 增加一个`__proto__`的属性指向构造函数的`protoType`


### 2.原型
#### 2.1 继承
js中的继承是原型链，就是`GrandFather grandFather<-Father father<-Son son`.它们之间靠__proto__联系在一起。就是
``` js
son.__proto__ === Son.ProtoType
son.__proto__.__proto__ === Father.protoType
son.__proto__.__proto__ .__proto__ === GrandFather.protoType
``` 
由于js查找属性时，按照这个原型链去查，一直能查到祖父。

#### 2.2 protoType `__proto__`
- 相同点：
protoType和__proto__对应的是同一个原型，**它是一个Object**。每个实例的__proto__都是构造函数用自己的protoType赋值的。
- 不同点：
不同点__proto__是实例调用的，protoType是构造函数调用的（注意：我没说是类，js本质上没类，只有对象和函数，每个实例都是构造函数构造出来的）。

``` js
var p ={ 
  isHuman: false,
  name: 'foo',
}
function Glass(){
  this.glass = 'glass'
}
var a = {}
// 不要直接给一个对象赋值__proto__,这个会导致性能问题，不是赋值的时候有问题，而是查找的时候有性能问题
a.__proto__ = p 
// 1. 可以用原型来做
var b = new Glass()
b.__proto__.__proto__ = p // or Glass.protoType.__proto__ = p
// 2. 用Obejct.create()
var c = Object.create(p) // 这个没有影响任何构造函数
```

#### 2.3 实例的属性
js的属性分为自己的(`Object.getOwnerPropety()`)和继承的(`Object.getPrototypeOf()`)
循环遍历属性有几个特殊的地方：
- 如果使用for
for会打印所有属性，包括自己的和继承的。
``` js
// 使用上面的b这个例子
for(let v in b){
  console(v) // isHuman, name, glass 都是可以打印出来的
}
```
- 使用Object.keys
Object.keys只会打印自己的属性
``` js
Object.keys(b) //只会打印 isHuman, name
```

> 我很少写原生的js了，大部分是用TS，写多了TS，这些都忘记了。