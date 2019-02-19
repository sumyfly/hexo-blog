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


### shallow vs render
shallow只会渲染当前控件，不会渲染children.render会渲染children.TODO: check?