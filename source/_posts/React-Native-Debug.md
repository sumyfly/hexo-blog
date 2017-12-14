---
title: React-Native-Debug
date: 2017-07-08 22:28:36
tags: 
  - react-native 
  - debug
categories: React-Native
---

### 1. Image overflow
Image是可以overflow:display,这个在android和ios都有效果。而View在android上不可以overflow:display的。

<!-- more -->
### 2. flex layout

#### 2.1 yoga
RN使用的layout引擎是yoga，https://github.com/facebook/yoga， 跨平台的，实现flexbox。
RN默认是display:flex，block和inner-block都是有效的，in-line无效。

#### 2.2 BFC
BFC blcok Formatting Context，这个文档流是相互隔离的盒子，所以margin不会合并而是并加，
这个可以都能通过overflow:hidden来设置，其实设置方式有很多，满足以下条件的任何一条都是BFC
  * float不为none
  * position 的值不为 static 或者 relative
  * display 的值为 table-cell , table-caption , inline-block , flex , 或者 inline-flex 中的其中一个
  * overflow 的值不为 visible

RN默认IOS overflow:visiable, Android是overflow:hidden，但是它们都是display:flex, 所以都是用的BFC，相互隔离的盒子。
https://segmentfault.com/a/1190000009139500

#### 2.3 RN CSS继承
注意：CSS都是强制继承的，但是RN不是。React Native still has the concept of style inheritance, but limited to text subtrees.RN的继承只有Text。


#### 2.4 RN flex
flex在网页CSS中是flex-grow,flex-shrink,flex-basis的简写，默认值是0 1 auto，后两个属性可选；RN中flex是布局用

宽高度应该是确定的，或者是可以推断的，如子节点宽高度，父容器宽高度。如果父节点、子节点皆不能推断，那么显示就会不确定，有问题。

父节点->子节点
1)子节点如果没有设置(alignItems,justifyContent），那么宽度为其父节点宽度,高度由内容决定。
2)子节点如果设置了(alignItems,justifyContent），那么宽高度为其内容宽高度。
3)子节点设置了宽高度，那么以此宽高度为准。

父节点flex:1
1)如果父节点flex:1不能推断大小，那么子节点flex:1也没有效果

如果父节点是position:absolute,那么子元素忽视flex:1

#### 2.5 style覆盖
flex：1会覆盖width,height设置。

marginTop，mrginBootom 会覆盖marginVertical；marginLeft, marginRight会覆盖marginHorizontal;
paddingTop, paddingBottom会覆盖paddingVertical;paddingLeft, paddingRight会覆盖paddingHorizontal。

### 3. postion:absolute
position:absolute不在flow中，忽视flex:1布局。其位置,大小由left,right,top,bottom设置，大小也可以由内容大小确定。

### 4. Text
如果元素为Text，其内容过长，并不会换行，而是一直显示下去。如果设置了宽度，或者推断出宽度，那么就会换行。

### 5. borderRadius
有时候borderRadius设置了，但是只有下半部份有效果，那么设置一下overflow:hidden

### 6. setState
setState是异步的，在contructor里面初始化this.state={...},然后紧接着设置setState,那么可能初始化值覆盖了后面的setState，所以setState这里并不可靠。放到didMount里面去设置。

### 7. ListView 中的onEndReached
不能设置contentContainerStyle,不然onEndReached不能再安卓上正确触发(初始化后的第一个onEndReached不能fired)。

### 8.子组件和父组件通讯问题
如果使用ref,那么还可以用state来保证同步状态。如果用this.xx的私有变量，不能保证同步状态。因为显示的时候，可能在diff后不去更换控件，而私有变量就算变化了，也不能取到。
继续讨论： 我觉得使用ref还是可能会出问题，我应该传props去子控件，那样会保证状态是父组件可以预料的。

### 9.ref调用共有方法
如果使用ref调用子控件的共有方法，那么可以不用bind(this)到子控件。在render函数中调用也不需要bind(this)，但是回调函数需要bind(this)。

### 10.继承和组合
实际项目中，使用了普通类作为Modal，里面有函数来渲染一些组件，它继承自普通父类，而不是Component。像这样：
```javascript
//应该使用Componet， extends Conponet
class Modal{

}

class SModal extends Modal{
  validated(){
    //.....
    return true // or false
  }
  renderComponent(){
    reutnr (<ValidatedComponent>
    //....
    </ValidatedCoponent>
    )
  }
}

class Page extends Component<Props,State>{
  render(){
    Modal modal = new SModal()
    return(
      <View>
      {modal.renderComponent()}
      </View>
    )
  }
}
```

这样导致的结果是，在容器redner中初始化后，其组件的state与从外部获取不匹配，是因为diff时发现组件props没变，所以没有用刚刚初始化状态的组件替代。所以要加上props传到组件中，并且加上key，这样还不如用Componet的组件保存数据和状态。
教训：
1.如果没有渲染，使用普通类型，如果涉及到渲染UI，使用Compoent
2.优先使用props从父组件往下传递，包括回调函数，而不是this.ref.xx直接调用公开方法
共享功能时组合和继承选择？
1.is A 继承， has A 组合。
2.主动调用方法用继承（依赖构造函数传属性），回调方法用组合（用props传入更加方便）。

### 11.requestAnimationFrame
使用requestAnimationFrame要注意，在componentWillReceiveProps中,不要使用requestAnimationFrame中setState，因为此时可能导致这个setState在render之后，导致多了一次render，所以在componentWillReceiveProps中，不使用requestAnimationFrame中setState。
``` javascript
 componentWillReceiveProps(nextProps) {
    if (!nextProps.refreshing && this.props.rowSource != nextProps.rowSource) {
        // requestAnimationFrame(() => { //不要加上这个，否则导致额外的render
          this.setState({ showActivityIndicator: true })
        // })
    }
  }
```

### 12. .gitignore
.gitignore 没有起效，是因为我使用了 import '../../smile-shared/xx' 应该是使用 'smile-shared/xxx',这个是引用的问题，tsc运行时，output结果错误。

### 13. import require外部文件
web在src文件夹里面的文件，不可以import那些在src外的其它文件，但是可以用require。比如在src文件夹外面的libs文件夹。


### 14. Text 布局

### 14.1 Text 宽度
Text如果不设置宽度，默认是一直延伸的，不会换行。默认占满容器宽度。可以设置固定宽度，flex:1，或给它容器设置可计算宽度。

#### 14.2 Text中嵌套View,要设置width,height
在Text中的嵌套View,Image都要设置width,height。可能是因为Text的样式会继承。

#### 14.3 Text在View中布局
Text如果不设置宽度，会导致在flexDirection:row中，计算错误，会穿透本容器，到上一层容器(换行位置不对)。

``` js
<View style={{flexDirection: 'row'}}>
  <Text style={{marginRight: 3}}>Title</Text>
  <Text >This is a long contetn.</Text>
</View>
```

### 15. yield
 调用generator时，没有yield会造成generator没有预期行为，比如yield call没有阻塞调用
``` js
import { takeLatest, delay } from 'redux-saga'
import { put, call, fork, take, race, select } from 'redux-saga/effects'

function* mySaga(){
  yield call(delay, 1000) 
}
``` 

### 16. RCTNativeAppEventEmitter vs DeviceEventManagerModule.RCTDeviceEventEmitter
RN中这两个Emitter应该没什么区别，我没有接受到消息，是因为我拼写中大小写有错误，浪费时间在寻找这两个Emitter区别。
``` java
  private static final String RCTInmobiEventName = "Inmobi_Resp";
```

### 46. moment format 'M-D'
在排序时，没有考虑跨年的问题，只是按'M-D'排序，应该按'YYYY-M-D'排序。
