---
title: HOC
date: 2017-07-18 17:24:51
tags:
  - react
  - hoc
---

### 1.定义
A higher-order component is a function that takes a component and returns a new component.

<!-- more -->

!["HOC vs Mixin"]("http://img0.tuicool.com/N7r6FnB.png!web")

高阶组件属于函数式编程(functional programming)思想，对于被包裹的组件时不会感知到高阶组件的存在，而高阶组件返回的组件会在原来的组件之上具有功能增强的效果。而Mixin这种混入的模式，会给组件不断增加新的方法和属性，组件本身不仅可以感知，甚至需要做相关的处理(例如命名冲突、状态维护)，一旦混入的模块变多时，整个组件就变的难以维护，也就是为什么如此多的React库都采用高阶组件的方式进行开发。

### 2.加载 卸载顺序
有2中hoc方式

#### 2.1 嵌套
``` javascript
const PopupContainer = (Wrapper) =>
  class WrapperComponent extends Component {
    componentDidMount() {
      console.log('HOC did mount')
    }

    componentWillUnmount() {
      console.log('HOC will unmount')
    }

    render() {
      return <Wrapper {...this.props} />;
    }
  } 
```
加载： inner didmount --> wrapper didmount
卸载:  wrap will unmount --> inner will unmount

#### 2.2继承
``` javascript
const PopupContainer = (Wrapper) =>
  class WrapperComponent extends Wrapper {
    static propTypes = Object.assign({}, Component.propTypes, {
      foo: React.PropTypes.string,
    });

    componentDidMount() {
      super.componentDidMount && super.componentDidMount();
      console.log('HOC did mount')
    }

    componentWillUnmount() {
      super.componentWillUnmount && super.componentWillUnmount();
      console.log('HOC will unmount')
    }
  }
```

加载： inner didmount --> wrapper didmount
卸载:  inner will unmount --> wrapper will unmount



