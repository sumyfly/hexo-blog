---
title: React-Native-Test
date: 2019-02-19 16:46:47
tags:
  - react-native
  - debug
categories: React-Native
---

### 1. jest mock

#### 1.1 mock node_module 中的 moduel

手动 mock，放到**mocks**目录下面

<!-- more -->

#### 1.2 mock 源码中的 module

##### 1.2.1 可以用 require

但是 mock 的结果按需要加上 default 这个 key，也要修改源码

##### 1.2.2 依旧 import

默认 jest 会加在 babel-jest 这个 transform.但是 RN 0.56 版本没有使用 babel-jest。所以 mock 不必提升到 import 之前，这个功能本来是 babel-jest 中的 plugin "jest-hoist"带来的，所以现在要手工在.babelrc 中加上这个插件。

> 在加入这jest-hoist之前，node_modules中的模块只能用文件mock（创建__mocks__文件夹），加入jest-hoist后可以使用函数mock(jest.mock('xxx'))

.babelrc 文件如下

```
{
  "presets": [
    "module:metro-react-native-babel-preset"
  ],
  "plugins": [
    "jest-hoist"
  ]
}
```

package.json 中 jest 相关配置

```
  "jest": {
    "preset": "react-native",
    "moduleFileExtensions": [
      "ts",
      "tsx",
      "js"
    ],
    "transform": {
      "^.+\\.(js)$": "<rootDir>/node_modules/react-native/jest/preprocessor.js",
      "^.+\\.(ts|tsx)$": "ts-jest"
    },
    "testPathIgnorePatterns": [
      "\\.snap$",
      "<rootDir>/node_modules/"
    ],
    "cacheDirectory": ".jest/cache",
    "testMatch": [
      "<rootDir>/__tests__/(**/*|*.)(spec|test).js?(x)"
    ],
    "snapshotSerializers": [
      "enzyme-to-json/serializer"
    ],
    "setupFiles": [
      "<rootDir>/jest/setup.js"
    ]
  }
```

setup.js, 不想要这个文件，也可以安装 enzyme 的官方文档下载一个 moduel 做 setup.

```js
import { configure } from "enzyme";
import Adapter from "enzyme-adapter-react-16";

configure({ adapter: new Adapter() });
```

### 2. shallow vs render

render、mount、shallow 的区别
render 采用的是第三方库 Cheerio 的渲染，渲染结果是普通的 html 结构，对于 snapshot 使用 render 比较合适。
shallow 和 mount 对组件的渲染结果不是 html 的 dom 树，而是 react 树，如果你 chrome 装了 react devtool 插件，他的渲染结果就是 react devtool tab 下查看的组件结构，而 render 函数的结果是 element tab 下查看的结果。
这些只是渲染结果上的差别，更大的差别是 shallow 和 mount 的结果是个被封装的 ReactWrapper，可以进行多种操作，譬如 find()、parents()、children()等选择器进行元素查找；state()、props()进行数据查找，setState()、setprops()操作数据；simulate()模拟事件触发。
shallow 只渲染当前组件，只能能对当前组件做断言；mount 会渲染当前组件以及所有子组件，对所有子组件也可以做上述操作。一般交互测试都会关心到子组件，我使用的都是 mount。但是 mount 耗时更长，内存啥的也都占用的更多，如果没必要操作和断言子组件，可以使用 shallow。

链接：https://www.jianshu.com/p/d4e447430c3c

### 3. 测试文件的模块名字

我遇到一个问题，如果某个文件名定义为 transaction, 源文件中使用它`import {funA} from '../utils/transaction'`,如果刚好我有个局部变量，定义为\_transaction, 那么这个局部变量改写了模块的变量，会导致这个模块的函数 funA 是 undefiend.这个问题在源码中不会有问题，但是在测试文件中有问题，我觉得是 transform 这个 import 的时候导致的。

### 4. Jest: `it` and `test` must return either a Promise or undefined.

这个是测试sagas时的函数没有用 async 关键字，我不知道generator也需要这个关键字？

```js
it("test", async () => {
  return testSata();
});
```

### 5. AutoMock
automock是jest容易搞混的概念，之前的旧版本，jest默认都是automock，也就是所有模块都是mock的。现在的版本配置，automock默认是false。就是因为automock容易搞混，对新手不友好。
无论是用文件mock，还是函数mock，都不是automock，都是manual mock。
