---
title: React-Native-Test
date: 2019-02-19 16:46:47
tags: 
  - react-native 
  - debug
categories: React-Native
---
### 1. jest mock
#### 1.1 mock node_module中的moduel
手动mock，放到__mocks__目录下面

<!-- more -->

#### 1.2 mock源码中的module
##### 1.2.1 可以用require
但是mock的结果按需要加上default这个key，也要修改源码

##### 1.2.2 依旧import
默认jest会加在babel-jest这个transform.但是RN 0.56版本没有使用babel-jest。所以mock不必提升到import之前，这个功能本来是babel-jest中的plugin "jest-hoist"带来的，所以现在要手工在.babelrc中加上这个插件。

.babelrc文件如下
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

package.json中jest相关配置
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

setup.js, 不想要这个文件，也可以安装enzyme的官方文档下载一个moduel做setup.
``` js
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });

```


### 2. shallow vs render
render、mount、shallow的区别
render采用的是第三方库Cheerio的渲染，渲染结果是普通的html结构，对于snapshot使用render比较合适。
shallow和mount对组件的渲染结果不是html的dom树，而是react树，如果你chrome装了react devtool插件，他的渲染结果就是react devtool tab下查看的组件结构，而render函数的结果是element tab下查看的结果。
这些只是渲染结果上的差别，更大的差别是shallow和mount的结果是个被封装的ReactWrapper，可以进行多种操作，譬如find()、parents()、children()等选择器进行元素查找；state()、props()进行数据查找，setState()、setprops()操作数据；simulate()模拟事件触发。
shallow只渲染当前组件，只能能对当前组件做断言；mount会渲染当前组件以及所有子组件，对所有子组件也可以做上述操作。一般交互测试都会关心到子组件，我使用的都是mount。但是mount耗时更长，内存啥的也都占用的更多，如果没必要操作和断言子组件，可以使用shallow。

链接：https://www.jianshu.com/p/d4e447430c3c

### 3. 测试文件的模块名字
我遇到一个问题，如果某个文件名定义为transaction, 源文件中使用它`import {funA} from '../utils/transaction'`,如果刚好我有个局部变量，定义为_transaction, 那么这个局部变量改写了模块的变量，会导致这个模块的函数funA是undefiend.这个问题在源码中不会有问题，但是在测试文件中有问题，我觉得是transform这个import的时候导致的。
