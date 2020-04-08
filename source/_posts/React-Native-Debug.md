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

##### 2.4.1父节点->子节点
1)子节点如果没有设置(alignItems,justifyContent），那么宽度为其父节点宽度,高度由内容决定。
2)子节点如果设置了(alignItems,justifyContent），那么宽高度为其内容宽高度。
3)子节点设置了宽高度，那么以此宽高度为准。

##### 2.4.2 父节点flex:1
1)如果父节点flex:1不能推断大小，那么子节点flex:1也没有效果

##### 2.4.3 父节点absolute,子节点flex:1
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
实际项目中，使用了普通类作为Model，里面有函数来渲染一些组件，它继承自普通父类，而不是Component。像这样：
```javascript
//应该使用Componet， extends Conponet
class Model{

}

class SubModel extends Model{
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
    Model model = new SubModel()
    return(
      <View>
      {model.renderComponent()}
      </View>
    )
  }
}
```

这样导致的结果是，在容器redner中初始化后，其组件的state与从外部获取不匹配，是因为diff时发现组件props没变，所以没有用刚刚初始化状态的组件替代。所以要加上props传到组件中，并且加上key，这样还不如用Componet的组件保存数据和状态。（如果是Componet那么还有另外一个方法是用componentWillReceiveProps中去判断某个props改变了，然后setState.）
教训：
1.如果没有渲染，使用普通类型，如果涉及到渲染UI，使用Compoent
2.优先使用props从父组件往下传递，包括回调函数，而不是this.ref.xx直接调用公开方法
共享功能时组合和继承选择？
1.is A 继承， has A 组合。
2.主动调用方法用继承（依赖构造函数传属性），回调方法用组合（用props传入更加方便）。

### 11.requestAnimationFrame
使用requestAnimationFrame要注意，在componentWillReceiveProps中,不要使用requestAnimationFrame中setState，因为此时可能导致这个setState在render之后，导致多了一次render，所以在componentWillReceiveProps中，不使用requestAnimationFrame中setState。也就是说不用requresAnimationFrame, setState会延迟渲染，等待此state更新成功。
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
Text如果不设置宽度，默认是一直延伸的，不会换行。默认占满容器宽度。可以设置固定宽度，或flex:1，或给它容器设置可计算宽度。

#### 14.2 Text中嵌套View,要设置width,height
在Text中的嵌套View,Image都要设置width,height。可能是因为Text的样式会继承。

#### 14.3 Text在View中布局
Text如果不设置宽度，会导致在flexDirection:row中，计算错误，会穿透本组件，到上一层容器(换行位置不对)。

``` js
<View style={{flexDirection: 'row'}}>
  <Text style={{marginRight: 3}}>Title</Text>
  <Text >This is a long contetn.</Text>
</View>
```

### 15. yield
 调用generator时，没有yield会造成generator没有预期行为，比如直接使用saga effects的call没有阻塞调用
``` js
import { takeLatest, delay } from 'redux-saga'
import { put, call, fork, take, race, select } from 'redux-saga/effects'

function* mySaga(){
  yield call(delay, 1000) // don't less yield call(delay, 1000)
}
``` 

### 16. RCTNativeAppEventEmitter vs DeviceEventManagerModule.RCTDeviceEventEmitter
RN中这两个Emitter应该没什么区别，我没有接受到消息，是因为我拼写中大小写有错误，浪费时间在寻找这两个Emitter区别。RCTNativeAppEventEmitter: Module that handles global application events.

- iOS: RCTEventEmitter <RCTBridgeModule>
``` Objective-C
@interface CalendarManager : RCTEventEmitter <RCTBridgeModule>

@end

@implementation CalendarManager
[self sendEventWithName:@"EventReminder" body:@{@"name": eventName}];
```
``` js
import { NativeEventEmitter, NativeModules } from 'react-native';
const { CalendarManager } = NativeModules;

const calendarManagerEmitter = new NativeEventEmitter(CalendarManager);

const subscription = calendarManagerEmitter.addListener(
  'EventReminder',
  (reminder) => console.log(reminder.name)
);
```

- iOS: bridge
``` Objective-C
@interface RNInmobi : NSObject <RCTBridgeModule>

@end

@implementation RNInmobi
@synthesize bridge = _bridge;
[self.bridge.eventDispatcher sendAppEventWithName:RCTInmobiEventName body:body];
```
```js
import { NativeModules, Platform, DeviceEventEmitter } from 'react-native';
DeviceEventEmitter.addListener('Inmobi_Resp', resp => {
  const callback = savedCallback;
  savedCallback = undefined;
  callback && callback(resp);
});
```

- Android: RCTDeviceEventEmitter
https://facebook.github.io/react-native/docs/native-modules-android
``` java
private void sendEvent(ReactContext reactContext,
                       String eventName,
                       @Nullable WritableMap params) {
  reactContext
      .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
      .emit(eventName, params);
}
...
WritableMap params = Arguments.createMap();
...
sendEvent(reactContext, "keyboardWillShow", params);
```
``` js
import { DeviceEventEmitter } from 'react-native';
...
   DeviceEventEmitter.addListener('keyboardWillShow', 
                      this.scrollResponderKeyboardWillShow);
```
- Android: RCTNativeAppEventEmitter
``` java
 getReactApplicationContext()
                .getJSModule(RCTNativeAppEventEmitter.class)
                .emit("QQ_Resp", resultMap);
```

``` js
import {NativeModules, NativeAppEventEmitter} from 'react-native';
NativeAppEventEmitter.addListener('QQ_Resp', resp => {
    const callback = savedCallback;
    savedCallback = undefined;
    callback && callback(resp);
});
```

总结: 原生中iOS用 RCTEventEmitter <RCTBridgeModule>， Android 用RCTDeviceEventEmitter。JS端用DeviceEventEmitter or NativeEventEmitter.

### 17. moment format 'M-D'
在排序时，没有考虑跨年的问题，只是按'M-D'排序，应该按'YYYY-M-D'排序。

### 18. 键盘
RN的键盘输入，比较麻烦，不是很方便。

* android 用padding， ios用position。
* padding是在后面添加一个键盘的高度，position应该是修改了position。所以padding在一个高度固定的View中，而不是ScrollView，就需要考虑控件后面的空白问题。
* 安卓计算Modal的flex:1时，扣除了键盘高度，IOS没有扣除键盘高度。

> 如果焦点变化了，并且当前焦点不可以输入，键盘自动隐藏。就像`this._textInput.blur()`这样。

> 如果使用KeyboardAvoidingView behavior='padding',它的容器高度可能会变化，比如Modal，那么在ScrollView中，并且顶部有个flex:1的View，它的高度包含了键盘高度。需要去掉这个高度，使用`View`包含它，这个其实就是不需要KeyboardAvoidingView了。
```js
renderContent() {
    return (
      <KeyboardAvoidingView behavior={IsAndroid ? 'padding' : 'position'}>
        {this.renderFooter()}
      </KeyboardAvoidingView>
    )
}
render (){
  return (
      <Modal
        visible={fullType == currentModal}
        animationType="fade"
        transparent={true}
        onRequestClose={() => this.closeModal()}>
        <View style={{ flex: 1 }}>
          <ScrollView
            style={{ flex: 1 }}
            contentContainerStyle={{ flex: 1 }}
            showsVerticalScrollIndicator={false}
            automaticallyAdjustContentInsets={false}
          >
            <TouchableOpacity
              style={styles.empty}
              onPress={() => this.closeModal()}>
            </TouchableOpacity>
            {
              IsAndroid ?
                <View>
                  {this.renderContent()}
                </View> :
                this.renderContent()
            }
          </ScrollView>
        </View>
      </Modal>
  )
}
```

``` js
import {KeyboardAvoidingView} from 'react-native'
<KeyboardAvoidingView
  style={{ paddingBottom: IsAndroid ? 260 * heightScale : 160 * widthScale }}
  behavior={IsAndroid ? 'padding' : 'position'}>
  {children}
</KeyboardAvoidingView>
```

### 19.Image dimension
Image source如果是本地，需要设置width,或者height.

### 19.1 本地图片

#### 19.1.1 只设置width，或者只设置height,然后加上resizeMode:'contain'
之前这么用，大部分没什么问题，但是如果这样用，图片显示没问题，但是图片占用的空间不是显示的大小。比如图片大小是100*50,
``` js
<Image style={{width:80,resizeMode:'contain'}} source={require(...)}>
```
如上，没有设置height,但是这个Image的实际高度是50,然后垂直居中显示。这样导致图片的相邻元素的位置不对。

#### 19.2 网络图片

#### 19.2.1 使用Image.getSize(url, (width,height)=>{}, error=>{})
在回调中setState({widht, height})

#### 19.2.2 使用flex:1 或者 position='absolute'
``` js
// 注意：容器中不能设置justifyContent和alignItems,不然图片不显示
<Image source={{uri: url}} style={{flex: 1, width: null, height: null}} resizeMode='contain'/>

//使用position='absolute'
<Image source={{uri: url}} style={{position: 'absolute', left: 0, top: 0, right: 0, bottom: 0}} resizeMode='contain'/>
```

如果Image的height，width不固定，用Image.getSize回调。如果容器size固定(不一定有width,height，而是可以是flex布局)，用flex:1或posiiton='absolute'。

> 注意本地图片如果也这么用，最好在外面加上一个View作为container, 这个container使用absolute或者flex:1。


### 20. tricky of ListView
ListView在ScrollView中，那么ListView不设置style={flex:1}, 那么ListView 和ScrollView的滑动事件很完美结合，表现为整体滑动。react-native-web中，Chrome表现和mobile一样，safari不行，ListView的父节点必须有个高度，手工计算它的高度，这个高度必须大于等于ListView content 的height, 那样滑动起来就像是一个整体，不然就会是局部滑动。
``` js
<ScrollView>
  <View style={{flex:1}} {/*这个flex:1在mobile app和chrome环境下会忽视，但是safari不行 */}> 
    <ListView
      dataSource={dataSource}
      renderRow={this.renderRow}
    />
  </View>
</ScrollView>
```

### 21. react-native run-android真机调试
Failed to install all. run-android on device
一种情况是真机没连上或者同时开了模拟器
第二种就是要这样执行
``` bash
adb devices
List of devices attached
85EABNGMFABC    device

react-native run-android --deviceId 85EABNGMFABC #这样可以成功安装到真机
```

[github issue](https://github.com/facebook/react-native/issues/9498)

### 22. flex布局中，子元素的宽高度
#### 22.1 flexDirection:'column'
子元素的宽度通常内容决定的，高度是固定高度或flex布局。

#### 22.2 flexDirection: 'row'
子元素的高度通常是**容器高度**，宽度是固定宽度或flex布局。但这个有意外，可能是父容器设置了`alignItems:'center'`，所以当此时子元素高度不是父容器高度时，按需要可以设置`alignSelf:'stretch'`。

#### 22.3 alignItmes:'center', flexDirection:'column'
`marginLeft: 20`，实际偏移10，因为alignItems:'center'的原因。这个是layout，影响占有空间。
`left:10`,实际偏移10。left一般和position:'relative'和position:'absolute'一起使用。注意在relative时候，原有位置空间不会消失。也会影响占有空间。

#### 23. lipo ios的库查询
有时候第三方sdk不支持x86_64（模拟器architecture),所以不能在模拟器上运行。查看skd支持的cpu的architecture，lipo -inof xxsdk.o

> ios release app, Project->Build Settings->Build Acitve Architecture Only,在release下是false,表示build出的.iap支持多个CPU架构。而通常debug下我们只需要x86_64（模拟器）的CPU架构就够了，或者当前真机的CPU架构。

#### 24. js class vs object
> 区别就是有没有逗号
如果是class
``` js
class A {
  fun1 = (a){
    console.warn(a)
  }
  //没有逗号','
  fun2 = (b){
    console.warn(b)
  }

}
```

如果是object
``` js
const A = {
  fun1(a){
    console.warn(a)
  },
  fun2(b){
    console.warn(b)
  }
}
```

### 25. android build error with 
发现自己写的react-native inmobi广告模块有问题，build.gradle的build tools版本不匹配
解决，使用和主build tool一致的版本号
造成冲突的原因，应该是此模块和主程序都使用了支持包

主build.gradle

``` gradle
android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"

    defaultConfig {
        minSdkVersion 16
        targetSdkVersion 22
        ...
    }
    ...
}

dependencies {
    ...
    compile "com.android.support:appcompat-v7:23.0.1"

}
```

react-native第三方模块的build.gradle
``` gradle
android {
    compileSdkVersion 23
    buildToolsVersion "23.0.1"

    defaultConfig {
        minSdkVersion 16
        targetSdkVersion 22
        ...
    }
    ...
}

ext {
    androidSupportLibVersion = "23.0.1"
}

dependencies {
    ...
    compile "com.android.support:design:${androidSupportLibVersion}"
    compile "com.android.support:appcompat-v7:${androidSupportLibVersion}"
    compile "com.android.support:recyclerview-v7:${androidSupportLibVersion}"
} 

```

修改第三方模块的build.gradle
``` gradle
 android {
-    compileSdkVersion 23
-    buildToolsVersion "23.0.1"
+    compileSdkVersion 25 //改成和主build.gradle一样
+    buildToolsVersion "25.0.2" //改成和主build.gradle一样
 
     defaultConfig {
         minSdkVersion 16
        targetSdkVersion 22
     }
```

### 26.读取Anroid的AndroidManifest.xml中的meta-data
meta-data的值读的时候不会自动类型转换，比如你要一个String,实际value是一个float，那么就会报警，返回一个null。
比如build.gradle中SomeValue值类型是int,那么AndroidMainfest.xml中的"${SomeValue}就是int,读取时就不能用getString.
``` java
//metaData.getString如果读到了float就会报警告，返回一个null
metaData.get(metaDataKey).toString();
```
我遇到的是grovey的问题，它读取一个Long的值，toString时变成了科学计数法，没有办法就只有加个前缀了，我觉得它应该有个方法toString时是直接String，不改变表示方法的。

### 27.map不返回
我有时遇到map使用的时候，不加return,导致页面不显示。跟lambada时更加要注意，是`=>()`还是`=>{}`。

### 28.Component bind this
Component中的回调如果是用了this.props或者**this.renderxxx()**，都是需要bind this的。

### 29. padStart(6, '')
Android对TypeScript`padStart(6, '')`不支持，还是改用
``` js
("0000"+str).slice(-4)
```

### 30. lineHeight
Android的lineHeight要求是Integer，不能是Double。IOS没问题。

### 31. StyleSheet.hairlineWidth
这个在iPhone5上，不能和backgroundColor一起使用。所以当前View不设backgroundColor,父容器设置backgroundColor。

### 32. react-native-svg ui polish
在Android平台，如果svg使用width: 12 * widthScale，那么可能出现icon的截边，所以使用固定宽度 6 来解决。

### 33. genymotion 修改时区
运行`adb shell`进入Android的shell环境。
查看当前时区 cat /data/property/persist.sys.timezone
修改当前时区 setprop persist.sys.timezone Asia/Shanghai

### 34. ScrollView tricky

``` js
<View style={styles.flexOne}>
    <ScrollView
      style={styles.flexOne}
      showsVerticalScrollIndicator={false}
      scrollEventThrottle={16}>
      <View style={{ flex: 1, alignItems: 'center' }}>
        {matchUp}
        {answersBox}
      </View>
      {this.renderFooter()}
    </ScrollView>
</View>
```
注意这里不能设置contentContainerStyle的高度或者flex,这个是内容撑开的。但是这里奇怪的地方是ScrollView内部的第一View用的是flex:1,这个flex:1对应的是ScrollView的高度，不是contentContainerStyle的高度，有点像minHeight。 所以renderFooter（）就会在内容不够长的时候在底部。内容足够长的时候在可以滑动下面。

### 35. tips
  - 1. minHeight对于不同内容而保存相同布局有奇效
  - 2. 注意action要是绑定后的action，而不是原始的action.就是要有dispatch。
  - 3. 请求时主要refreshing字段的使用，变换authToken要强制刷新。

### 36. flexWrap: 'wrap', flexDirection: 'row'
我之前这么用, container有个`containerWidth`，item的`itemWidth`就是containerWidth / columns,看似没问题，但是在IphonePlus或者其它情况下，itemWidth的值四舍五入了，和加在一起大于contaienrWidth，那么就换行了。
解决方法：
- itemWidth = Math.floor(containerWidth / columns)
- itemWidth的值小于contaienrWidth，itemWidth = (containerWidth - 10) / columns, 然后container的style, justifyContent: 'space-between', alignItems: 'center, 但是要注意不能太小，以致一行比原来可以多放一个item.

推荐第一种方法

### 37. 在reduce中使用state.mergeDeep
immutable mergeDeep(action.payload), 如果action.payload中有数组，那么是不会merge成功的(只有新的数组，没有旧的数组)，所以我直接返回了fromJS(action.payload),这个对参数有要求，就是要全面。

### 38. ToucchableOpacity vs TouchableWithoutFeedback
TouchableOpacity如果设置activityOpacity={1},那么表现和TouchableWithoutFeedback.

#### 不同点
- TouchableOpacity布局上更像是View，没有children也可以，TouchableWithoutFeedback必须要有个children，children不能为空。
- 在容器flexWrap:'wrap',TouchableWithoutFeedback中不能正常显示，使用TouchableOpacity能正常显示。所以TouchableOpacity更像View，而TouchableWithoutFeedback布局功能有所欠缺。

### 39. border 虚线
RN 支持borderStyle: 'dotted'，所以不需要用图片来代替虚线了。

### 40. Text
RN Text的默认字体大小是14,ios上是14.TODO:android, 系统设置默认字体？

### 41. TouchableOpacity, TouchableWithouFeedback的onPress回调函数
我遇到一个错误，`Malformed call from JS: field size are different`
原因是给react-native-navigation的navigator传错参数了。
它们的回调函数有参数event,比如我定义一个
```js 
onPressHandler =(title)=>{
  this.props.jump('xxx.xx', {title})
}
```
这里的title应该是一个string，但是实参是一个event的对象。所以在闭包中，一定要注意默认参数，比如onPress的。

### 42. Trying to add a 'RCTRawText [text: >]' to a 'RCTView'
这个是因为在JSX语法中，多了一个'>',这个是写代码不小心造成的。JSX所有代码都要在标签里。Android会导致crash,iOS不会crash.改正习惯`>`不能单独一行。

```js
//crash示例
render(){
  return (
    <View
      style={styles.container}>
      > //culprit
      <Text>示例</Text>
    </View>
  )
}

```

### 43. 模块设计

- 不用使用一个Page(Container)来共享逻辑，这让重构复杂。
- 有时候我想用数据代替逻辑处理props，比如数据model中有个字段是type,那么使用这个model时就可以自处理，而不用把逻辑(用props传递函数)从外部传入。增加数据的自描述能力，功能函数尽量无状态。
- 有顺序要求，使用Array，不用Object,因为Object.Keys()的顺序不是guaranntee。

### 44. RN中使用setImmediate
好像和browser、node中的表现不完全一样。相同的部分都是在执行栈结束后添加事件到任务队列。
不同点:
- browser、node中嵌套的setImmediate会在两个EventLoop中执行
- RN中嵌套的setImmediate还是会在同一个EventLoop中执行，来自官网0.56版本。

> TODO: setTImeout在app后台运行时应该也是起效的。被OS杀死就是重置了。

### 45. iOS的启动脚本中的/bin/sh
https://apple.stackexchange.com/questions/208408/profile-is-not-loaded-on-non-login-bin-sh
> /bin/sh速度比/bin/bash要快，但是/bin/sh只有登录的session有读取~/.profile，其它的session是不读~/.profile。所以加上env ENV=$HOME/.profile /bin/sh
我遇到的问题好像是node在xcode的Bundle React Native code and images中没能识别node
``` bash
export NODE_BINARY=node
../node_modules/react-native/scripts/react-native-xcode.sh
```
一开始我是想给sh加上node环境，发现其实我的sh是有node的，但是没有nvm，我以为是nvm问题，后来发现不是nvm的问题，而是我的sh的node不是nvm管理的node路径，是有brew安装yarn是安装了一个node的10.x的版本，解决方案:先卸载yarn，brew uninstall yarn,然后brew install yarn --without-node，发现环境现在OK了，sh不需要nvm,只需要node。

https://www.cnblogs.com/EasonJim/p/6850319.html

### 46. xcode 的文件夹Group和Reference
- xcode创建Resources时，用的Create Group with folder,右侧属性 Location是relative to Grup.这种是没有文件夹，只是一个引用，相当于内部的一个软连接，因为在项目文件夹下是看不到这Resources的。

- add Files一般的文件，可以选择Group和Reference,Group是黄色的，Reference是蓝色的。一般勾上"Copy Items if needed"

- 添加项目，用add Files，我是用的Reference,选中的项目文件.xcodeproj。

### 47.Text不占满父元素
设置父元素`alignItems: 'flex-start'`,因为默认父元素是`alignItems: 'stretch'`。

### 48.  Malformed calls from JS: field sizes are different.
这个是JS调用函数时的参数类型不对，检查参数类型。

### 50.尽量不使用数组的index作为key
iOS上Google地图上的markers会有闪烁的问题，一开始我以为是原生地图的绘制问题。后来我发现是<Marker>的key设置问题，我使用了数组的index，但是当新的markers数组请求到，用的还是同一个index，所以Dom diff时候，<Marker>会漂移，因为被当做了同一个组件（相同的key）。我一开始是在fetch的onSuccess的callback中手动关闭Callout，但是体验不好，Callout会消失，需要重新点开，最好在请求结束后保持打开状态，也就是组件不要被Dom diff后替代。
解决方案：使用maker的_id作为key,这是mongoDB的存储键，是唯一的，完美解决闪烁问题。Dom diff效率还是很高的,选择合适的key会解决这种漂移闪烁问题。

### 51.flexible and reusable
我觉得flexible可能更加重要些，或者说优先级高些，原因是我实际项目中，更改需求是非常常见的，所以代码要求非常灵活，以应对各种变化（UI,排版，数据格式）。项目时间长了，又要重构，以去除冗余代码，让代码可重用性高，但是在项目初期阶段，很难写出可重用性高的代码，因为对于未来的需求变化一无所知。所以在项目初期阶段，写一下面条代码也是可以的，对于细微的组件还是需要单独分开，以便将来重构。
> flexible和reusable初期阶段可能对立，当长期来看，是互补的。

### 52.image picke 集成
react-native-image-crop-picker，返回path没有“file://"前缀也可以使用在FormData中的uri.FormData中name不能是数字，也不能是null，必须是一个字符串值，没有值我用String(Date.now())。
上传图片，不能用Debug Remote，那个时候的JS的运行环境是brower，而RN运行时是用的JCore，所以不能Debug.RN内置的fetch用的内置的XmlHttpRequest，所以axios也是可以用的(都是封装XmlHttpRequest)。
``` js
const form = new FormData()
form.append('image', {
  uri: $filePath, // 'Private/xxx/xx.../xx.jpeg' 
  type: $mime, // 'image/jpeg'
  name: $name
})
```
后台node server中可以得到这个FormData,用的fileuplaod模块那么在req.files。
TODO:如果用multer模块会在body中？应该是按照FormData的type来确定。

一开始我Debug Remote时，文件是在是在body中，不在req.files里，关了Debug Remote时就进req.files.

很有帮助：https://github.com/g6ling/React-Native-Tips/tree/master/How_to_upload_photo%2Cfile_in%20react-native

> FormData 是XmlHttpRequest中新增的，File继承自Blob。Blob有两个属性:size和type。Blob.size (只读),Blob 对象中所包含数据的大小（字节）;Blob.type (只读),一个字符串，表明该Blob对象所包含数据的MIME类型,如果类型未知，则该值为空字符串。如果分块上传，使用有blob.slice(),那个用的Transfer-Encoding: chunked这个Header.

较全:https://deliciousbrains.com/using-javascript-file-api-to-avoid-file-upload-limits/
https://blog.csdn.net/foolish0421/article/details/73302336
https://www.cnblogs.com/xuehaoyue/p/6639029.html

> Fetch在Browser环境中不能设置ContentType:'multi/form-data',会报没有bundary的错误。因为Fetch上传文件时会自动设置这个ContentType,还会加上bundary.
### 53. cocoaPosd
现在RN的第三方模块都开始支持cocoaPods,如果是数组target，需要在Podfile中写ruby.
``` ruby
targetArray = ['sample-develop', 'sample-production']
targetArray.each  do |t|
  target t do
  end
end
```
增加了新的pod要去PROJECT->Configurations->Debug/Release中把配置都设置为None,然后执行pod install,那么新的配置文件就设置好了。
> 如果出现了pod的链接错误，去Link Binary With Libraries中删除无效的xx.a文件。

### 54. react-naitve link match error
比如我用`react-native link react-native-config` ,iOS部分出现`Error: Cannot read property 'match' of undefined`。原因是iOS的中已经适应了CocoaPods 如果你的iOS项目使用的是CocoaPods(包含Podfile)，链接库有podspec文件，那么反应式本地链接将使用Podfile链接库. 那么此时需要在 Podfile文件end 前加入你的pod,这个必须在执行link命令前添加。
``` ruby
    pod 'react-native-config', :path => '../node_modules/react-native-config' # dot env file config

```
https://www.jianshu.com/p/b41037546ef9

### 55. FlatList onEndReached 的触发问题
如果在FlatList中的items不能占满整个空间时，mounted后会触发onEndReached两次，第一次会渲染items,第二次会得到没有更多的信息（数据为空数组）。但是`刷新`中只会触发onEndReached`一次`，如果有判断条件依赖第二次fetch的结果（空数组）就会有bug,这部分逻辑在refresh是不起效。

### 56. chrome copy()
copy(var1)可以复制变量到剪切板，很方便。不需要JSON.stringify转字符串。

### 57. Statusbar 设置了hidden但在安卓上有时候会显示
原因是getCurrentActivity有时候会返回null.因为currentActivity只有在onHostResume和onNewIntent时候才设置，所以onCreat时没有设置，而React-Native在onCreate时候就开始js的渲染。解决方案是加个延时setTimeout，并没有一个好的方式，所以在gteCurrentActivity时候保证页面已经渲染了，或者加个判断。Cobrowse也会遇到这个问题，所以还是很普遍的。
https://github.com/facebook/react-native/issues/18345
onHostResume是ActivityEventListener的方法。onNewIntent是Activity的方法。

### 58. reducer和saga使用的是同一个action
当reducer和saga监听了同一个action，先是reducer触发，然后是saga,注意一点：不要在reducer里面修改action的payload，不然saga就是接受到修改后的payload,引发bug。如果要有修改需求，可以用Object.assgin做浅复制或者深复制。

### 59. react中state现在不能修改了
在16.0之前，state可以修改，但是有warning,16.0之后，state修改就会报错，说不能修改一个immutable或者freeze的object.具体表现是，primitive的类型可以修改(number, boolean)，object的类型不能修改(array)。

之前我们为了减少render次数，只想做shallow comparison，用的是react-addons-update，这个可以修改引用。后来有了immutable,功能更加强大。
https://reactjs.org/docs/update.html
https://immutable-js.github.io/immutable-js/docs/#/List

但是我们会遇到一个复合类型的state，像{printers:[{name:'', tags:[]}]},这种类型的state，不能在原来基础上修改，immutable是个好的方案，mergeDeep如果没改变，那么引用也不会改变。但是immutable每次都要重新有赋值操作。我在想如果使用lodash.cloneDeep是不是也可以呢？比immutable的劣势就是每次setState都会导致渲染。

这里有两个问题：
- 如何修改state: 1.lodash.cloneDeep 2.immutable修改
- 如何减少渲染次数，使用shouldUpdateComponent后者继承PureComponent: 1.immutable state 2.react-addons-update

### 60. realm 的RealmResults的在for循环中的问题
RN版本是0.58.5，在真机上jscore中for循环RealmResults会有问题，显示iterator不是一个function,而debug的时候因为是chrome的环境，没有这个问题。在emulator上也没有这个问题。所以我用了`for (let item of Object.values(record.items))`,如果给其它函数调用这个record,需要重新包装一下，不能在RealResuls中操作，而是重新创建一个对象, 不能在RealmResutls上直接修改。
在RN的0.59.10中好像没有这个问题了。不知道是RN修复了，还是Realm修复了。

```js
const { items, ...others } = record;
newRecord = { ...others, items: Object.values(items) };
```
注意：不能再RealmObject上直接修改值，需要重新浅copy,但是Object.assign({}, realmObject)不行，{...realmObject}也不行，只有{one, ...others} = realmObject, newObject= {one, ...others}可以。但是最后我选择了for...in..循环。
```js
    _transactionMergeItems = {};
    // In case we need to set the serviceCharge,so need a shallow copy of the realm object.
    for (let key in openOrderTransaction) {
      if (openOrderTransaction.hasOwnProperty(key)) {
        _transactionMergeItems[key] = openOrderTransaction[key];
      }
    }
```


### 61. RealmResults的序列化
由于RealmObject在序列化的时候，遇到外键的情况，可能导致循环引用，那么序列化就会有问题，表现为卡死。TODO:check later?
而且我的Redux中使用了Immutable作为对象类型，所以必然遇到这样的问题。
解决方案就是：
不要把RealmObject放到Redux中，也不要序列化它。可以传一个primaryKey,接收方再次查找一次数据库。


### 62. RealmObject是个引用，它的属性是通过方法调用的

比如我们查到一个RealmObject,每次调用它的某个属性，如productName,那么每次都是数据库中的实时存储的值。TODO: check?

### 63. React-Native-languages的json中的key
key不能含有`.`字符，不然匹配不到，`?`是可以匹配到的。
如果有{"info":"{{name}}'s ages is {{age}}"},调用的时候t('info', {name:'xx', age: 10}).

### 64. CALL_AND_RETRY_LAST Allocation failed - JavaScript heap out of memory
export NODE_OPTIONS="--max-old-space-size=4096"

node本身，或者说v8本身有一个默认配置:Currently, by default v8 has a memory limit of 512mb on 32-bit systems, and 1gb on 64-bit systems. The limit can be raised by setting –max-old-space-size to a maximum of ~1gb (32-bit) and ~1.7gb (64-bit), but it is recommended that you split your single process into several workers if you are hitting memory limits. 
所以我们需要配置一下运行内存。

### 65. redux-persist 发送REHYDRATE
redux-persist 发送REHYDRATE的时间是redux从持久化文件中恢复成功，此时我让它执行一个自定义的init saga。注意此时页面的执行是并发的，所以如果在页面中依赖的某种状态是在init中设置的，那么从页面的didmount中去改变它是可能是滞后的，导致依赖的这个状态一直不变。有两种方案：
1.在componentWillReceiveProps中判断更改的props，这个比较麻烦，要判断前后两个props。
2.在init中改变redux的state后，紧接着发出相应的处理action。这个方案比较优雅，明了。

所以一定不要事先认为saga的执行有先后关系，并依次编写业务，而是要把它们认为是并发的，需要靠action来串行执行。

### 66. string,integer使用===
使用`===`比较时，不但比较值，也比较类型，所以`'1' === 1`的值是`false`, 所以js也许需要注意类型，尽管它会自动转换类型。

### 67. NaN, Infinity
NaN, Infinity这些JS类型如果传到Native moduel,会导致App crash, 原因是Native无法处理这些类型。

null可以传到Native，Native module可以处理Double类型，但是不能直接getDouble，这样也会报错。要先判断类型，使用readableMap.getType(key)。

undefined的值，在Native直接是key不存在。

### 68. Saga的call 调用instance的方法
Saga的call调用instance方法，如果instance方法中用到了this.value，那么需要传递context，就是绑定this.
``` js
yield call([loggly, loggly.push], logData) // loggly.push中有用到this.属性值，所以需要传递context,不然this指向的值不对。
yield call(loggly.push.bind(loggly), logData) // 这样也是可以的，但是传递context更好。bind会返回新函数。
```
