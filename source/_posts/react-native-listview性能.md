---
title: react-native-preformace
date: 2017-07-27 17:14:00
tags:
    - react-native
    - 性能
---
React-Native ListView性能
===================================

### 优化方案
1.使用react-native-sglistview、react-native-gifted-listview
2.设置removeClippedSubviews 为true，子类需要添加样式overflow:'hidden'
3.设置拖动速率 decelerationRate
4.dataSource里的数组个数做限制，做分页处理，10~15一页
5.在sholudComponentUpdate处理，确保不要重复刷新
6.上拉下拉刷新操作，加载过程中隐藏listview，显示加载界面

<!-- more -->

### 优化建议
1. 异步逐层渲染。

React Native 虽然一直标榜媲美Native的体验，但实际使用下来，其渲染性还是非常低效，基于ScrollView和ListView两大容器，在渲染上，相当于web端的table布局，需要等整个大table渲染完成才显示页面，也就是说，当容器内有大量的子元素，其白屏时间会非常长。

如何让React Native做到像web端边渲染边加载？可以采用异步渲染的方式，使用requestAnimationFrame 或 setTimeout 定时将单个组件push进ScrollView容器。

基于这个原理，写了个逐层渲染的组件： react-progressive[https://github.com/hugohua/react-progressive]

2. 实现shouldComponentUpdate方法

如上第一点，逐层渲染提升打开时间，但是也会导致component重复渲染，也就是执行了大量无用的diff算法。虽然React里引以为傲的diff算法非常高效，但是执行数量达到一定程度后，也会带来非常大的影响。那么可使用 shouldComponentUpdate 来控制component的渲染次数。

如何做？

如果确定该组件渲染完后无需再次更新，即这个组件是一个静态组件，那么可以直接return false。
shouldComponentUpdate(){
    return false
如果组件比较复杂，自己对RN的更新机制不太熟，可以直接Minxi一下React提供的 PureRenderMixin 组件
mixins: [React.addons.PureRenderMixin]
手动实现或使用第三方组件库，比如 Immutable-js
说白了，就是要确定组件内的不可变数据，让其不再执行diff及render。

3. 使用setNativeProps方法

setNativeProps 方法可以理解为web的直接修改dom。使用该方法修改 View 、 Text 等 RN自带的组件 ，则不会触发组件的 componentWillReceiveProps 、 shouldComponentUpdate 、 componentWillUpdate 等组件生命周期中的方法。

建议频繁更新的操作，如slider、tabs切换等拖曳操作时，使用 setNativeProps 来更新属性，会获得意想不到的性能体验。

代码片段：

me.refs.tabView.setNativeProps({
                style : {
                    height : 0,
                    opacity : 0
                }
            });
性能分析工具： React.addons.Perf

4. 不要使用阴影效果

React Native 里面的 shadow 相关的样式，是非常耗性能的css属性。这在web上，以前android 2.0年代，也是一样耗性能的css属性之一。如果需要使用阴影效果，建议使用图片来代替反而性能更好一些。

5. 最小化DOM

React Native里虚拟dom结构越复杂，则越低效。感觉RN的渲染性能，和以前android2.x时代没多大区别，如果层级结构大于5级，则要考虑下优化了。这没啥技巧，纯靠经验及硬实力。

6.组件粒度化

如何更好的划分组件粒度，这需要合理的对组件进行更细粒度的划分，区分出静态组件及动态组件。

7.bind和()=>{}
最好不要使用lambda函数，因为它每次都是产生一个新的函数，可能引起控件不必要的渲染。所以用一个函数wrap那些按需求变化的功能函数，保持这个wrapper不变。
``` javascript
constructor(props){
    //...
    this.wrapperFunc = this.wrapperFunc.bind(this)
}

wrapperFunc(){
    if(this.state.xx = 0){
        this.fun1()
    }else{
        this.fun2(p1,p2)
    }
}

<Componet fun={this.wrapperFunc}>
```