---
title: React Native Notes
comments: true
toc: true
date: 2018-03-27 09:19:42
tags:
	- android
	- javascript
---

[TOC]
[React Native](https://facebook.github.io/react-native/)学习笔记。


<!-- more -->

### 基础
#### 组件
类似于安卓的View控件。render方法中实现每个控件的布局方法。

##### 定义并导出组件
``` javascript
class MyComponent extends React.Component {
	render() {
    	return <Text>My component</Text>;
    }
}
export default MyComponent
```

##### 导入并使用组件
``` javascript
import MyComponent from './MyComponent';
class Main extends React.Component {
	render() {
    	return <MyComponent>;
    }
}
```

##### 注册组件访问入口
``` javascript
AppRegistry.registerComponent('MyApp', () => Main);
```

##### 渲染器和JSX
JSX将HTML直接嵌入到JS代码中，实现模板和组件的关联。
渲染器返回JSX格式的页面展示逻辑。

``` javascript
render() {
    const txt = 'Hello';
    function say(name){
    	return 'I am '+name;
    }
    return (
        <View>
            <Text>This is a title!</Text>
            <Text>{txt}</Text>
            <View>
            	<Text>{say('React')}</Text>
            </View>
        </View>
	);
}
```

##### 组件生命周期
###### 初始化
* getDefaultProps -- 初次创建调用
* getInitialState
* componentWillMount -- 初次创建调用
* render
* componentDidMount -- 加载完成

###### 存活期
* componentWillReceiveProps
* shouldComponentUpdate
* componentWillUpdate -- 初次渲染时不调用
* render
* componentDidUpdate -- 更新完成

###### 销毁和清除
* componentWillUnmount

##### 内置组件和APIs
###### 基本组件
* View
* Text
* Image
* TextInput
* ScrollView
* StyleSheet

###### 用户交互
* Button
* Picker
* Slider -- 对应安卓的SeekBar
* Switch

###### 列表
* FlatList
* SectionList

###### IOS专用
* ActionSheetIOS
* AlertIOS
* DatePickerIOS
* ImagePickerIOS
* NavigatorIOS
* ProgressViewIOS
* PushNotificationIOS
* SegmentedControlIOS
* TabBarIOS

###### 安卓专用
* BackHandler -- 设备后退键
* DatePickerAndroid
* TimerPickerAndroid
* DrawerLayoutAndroid
* PermissionAndroid
* ProgressBarAndroid
* ToastAndroid
* ToolbarAndroid
* ViewPagerAndroid

###### 其他
* ActivityIndicator
* Alert
* Animated
* CameraRoll
* Clipboard
* Dimensions
* KeyboardAvoidingView
* Linking
* Modal
* PixelRatio
* RefreshControl
* StatusBar
* WebView

#### 属性和状态
##### 属性
自定义组件的属性。
组件类似于安卓中的控件，属性也类似于控件中的属性。
属性值可以是{pic}，类似于DataBinding的效果。

``` javascript
class User extends Component {
    render(){
        const user = this.props.data;
        this.props.onReady('I am ready!');
        return(
            <View>
                <Text>
                    score: {this.props.score}
                    type: {this.props.type}
                    Name: {user.name}
                    Age: {user.age}
                </Text>
            </View>
        );
    }
}
//dufaultProps
User.propTypes = { score: React.PropTypes.number };
User.defaultProps = { score: 0 };
var user = {name: 'foo', age: 21};
class Main extends Component {
    handleReady(str){
    	console.log(str);
	}
	render(){
        return(
            <View>
            	<User type="Dev" data={user} onReady={this.handleReady}/>
            </View>
        );
    }
}
```

##### 状态
属性在组件运行过程中是不可变的，状态类似于展示数据，是可变的，使用setState方法修改状态。

``` Javascript
class Timer extends Component {
    constructor(props) {
    	super(props);
    	this.state = {count: 0};
    }
    componentDidMount() {
    	let that = this;
    	setInterval(function () {
    		that.increase();
    	}, 1000);
    }
    increase() {
    	this.setState({count: this.state.count + 1});
    }
    render() {
        return (
            <View>
            	<Text>count: {this.state.count}</Text>
            </View>
        );
    }
}
class Main extends Component {
    render(){
        return(
            <View>
           		<Timer/>
            </View>
        );
    }
}
```

##### 区别

* 属性
	* 在组件树中传递数据和配置
	* 属性视为不可变值，不要在组件内部直接修改
	* 使用事件处理器在子组件间传递属性
* 状态
	* 存储简单的视图状态，如是否显示下拉选项
	* 使用this.setstate修改状态，不要直接使用this.state

#### 样式
内置组件的属性之一style。样式键值与CSS类似，除了命名采用驼峰式，而不是全小写，短横线分隔。

##### 定义样式
* StyleSheet.create()
	* container
		* flex
		* backgroundColor
	* text
		* fontSize
		* color

##### 使用样式
* View/Text/Image
	* style

##### 继承样式

* View
	* parentColor -- 将样式作为属性传递
	* style={[BaseStyles.text, Styles.text]} -- 样式合成

##### 属性
组件特有属性。
RN中尺寸使用dp（设备无关像素）。

* View
	* borderColor
	* elevation
	* opacity
	* width/height
	* flex -- 分配剩余空间，1代表全部
		* 仅限父控件的剩余空间分配，如果父控件无width和height或flex值，父控件剩余的空间为0，仅指定flex值的子控件将不可见
	* position
		* absolute -- top/left为绝对值
			* 可搭配transform属性
		* relative -- 默认布局，top/left为相对值
* Image
	* resizeMode
	* tintColor
	* overlayColor
* Text
	* color
	* fontFamily
	* writingDirection
* Flex
	* flexDirection
	* alignItems
	* justifyContent
* Transform
	* transform
	* rotation

#### 布局
##### Flexbox
为不同屏幕尺寸提供一致性布局的布局算法。
类似于安卓的线性布局的扩展。
通常组合flexDirection, alignItems和justifyContent确保正确布局。
在安卓中flexDirection默认为column，flex仅支持单个数字。

* flex -- 分配比例
* flexDirection -- 方向
	* row
	* column(default)
* justifyContent -- 剩余空间分配
	* flex-start/end/
	* center
	* space-around/between/evently
* alignItems -- 所有子item次轴的对齐方式
	* flex-start/end
	* center
	* stretch -- 要求子控件次轴上无具体尺寸值，否则无效果
* alignSelf -- 当前item对齐方式
* flexWrap -- 是否折行
* 方框模型
	* margin[Oriented]
	* border[Oriented]Width
	* padding[Oriented]

##### 尺寸

* Dimensions
	* window
		* width/height
		* scale

### 手势响应系统
手势响应系统管理手势的生命周期。 触摸会经历几个阶段来确定用户真实的意图。
手势响应系统允许组件在不知道组件的其他信息下允许组件协商触摸交互。

* 最佳实践
	* 反馈或高亮 -- 用户触摸给予反馈
	* 取消能力
* 触摸高亮和触摸XX
	* 这类组件提供了组件事件属性
		* onPress
		* onLongPress

#### 生命周期

##### 协商期 -- 决定是否应答者

* View.props
	* onStartShouldSetResponder
	* onMoveShouldSetResponder

##### 请求期 -- 请求成为应答者
* View.props
	* onResponderGrant
	* onResponderReject -- 其他组件已经是应答者，且不释放

##### 应答期
* View.props
	* onResponderMove
	* onResponderRelease -- 类似于touch up
	* onResponderTerminationRequest -- 其他组件请求成为应答者，是否释放
	* onResponderTerminate -- 应答者移除
		* 释放应答者给其他组件
		* 系统直接移除，如IOS的控制中心或通知中心

#### evt合成触摸事件结构

* evt
	* nativeEvent
        * changedTouches
        * identifier
        * locationX/Y -- 相对元素
        * pageX/Y -- 相对根元素
        * target -- 接收触摸事件的元素的节点ID
        * timestamp
        * touches -- 当前屏幕的触摸点

#### 捕获ShouldSet处理器
用于父组件拦截子组件成为响应者。类似安卓事件处理机制。

* View.props
	* onStartShouldSetResponderCapture -- 父组件
	* onMoveShouldSetResponderCapture -- 父组件

#### 平移响应器
* PanResponder.create
	* onStartShouldSetPanResponder
	* onStartShouldSetPanResponderCapture
	* onMoveShouldSetPanResponder
	* onMoveShouldSetPanResponderCapture
	* onPanResponderGrant
	* onPanResponderMove
	* onPanResponderRelease
	* onPanResponderTerminationRequest
	* onPanResponderTerminate
	* onShouldBlockNativeResponder
* gestureState
	* stateID
	* moveX/Y -- 最新的屏幕坐标
	* x0/y0 -- 屏幕坐标
	* dx/y -- 累计的距离
	* vx/y -- 当前的速度
	* numberActiveTouches -- 当前触摸点数量

### JS环境
#### 运行时
两种环境非常相似，还是有可能出现不一致问题。

* 大多情况RN使用支持Safari的JS引擎JavaScriptCore
	* IOS中JavaScriptCore不使用JIT技术，因为缺少可写的可执行内存
* Chrome调试时使用Chrome的V8作为JS引擎，通过WebSockets传递原生代码

#### JS语法转换器
语法转换器允许使用新的JS语法，无需等待所有解释器支持。
RN装载的是Babel JS编译器。

启用的转换功能：

* ES5
	* 保留字：promise.catch(function() { });
* ES6
	* 箭头功能 onPress={() =&gt; this.setState({pressed: true})}
	* 块范围： let greeting = 'hi';
	* 调用传播： Math.max(...array);
	* 类：class C extends React.Component { render() {} }
	* 常量：const answer = 42;
	* 解构：var {isActive, style} = this.props;
	* for遍历：for (var num of [1, 2, 3]) {}
	* 模块：import React, { Component } from 'react';
	* 计算属性：var key = 'abc'; var obj = {[key]: 10};
	* 对象简洁方法：var obj = { method() { return 10; } };
	* 对象短符号：var name = 'vjeux'; var obj = { name };
	* 可少参数：function(type, ...args) { }
	* 字符串模板：var who = 'world'; var str = `Hello ${who}`;

#### 支持的标准函数

* 浏览器
	* console.{log, warn, error, info, trace, table}
	* CommonJS require
	* XMLHttpRequest, fetch
	* {set, clear}{Timeout, Interval, Immediate}, {request, cancel}AnimationFrame
	* navigator.geolocation
* ES6
	* Object.assign
	* String.prototype.{startsWith, endsWith, repeat, includes}
	* Array.from
	* Array.prototype.{find, findIndex}
* ES7
	* Array.prototype.{includes}
* ES8
	* Object.{entries, values}
* 专用
	* __DEV__

### 直接操作
不通过属性或状态触发全部子组件树的重渲染。而是类似于在浏览器中直接修改DOM节点的方法，调用setNativeProps直接修改DOM节点的属性。
频繁调用setNativeProps会造成性能瓶颈，通常用于创建连续性动画，避免组件层次渲染和视图重调整的开销。
setNativeProps是不可少的方法，在原生层（DOM，UIView）中存储状态，而不是在React组件中。在使用这个方法解决问题前，请先尝试setState和shouldComponentUpdate方法。

#### setNativeProps在TouchableOpacity中
在TouchableOpacity中使用setNativeProps方法更新所有子组件的不透明度。
NativeMethodsMixin.js中的setNativeProps方法实际是RCTUIManager.updateView方法的包装，跟ReactNativeBaseComponent.js中 receiveComponent重渲染的方法调用结果是一样的。

#### 复合组件与setNativeProps
继承React.Component的组件创建的复合组件不能直接设置样式属性，因此不能调用setNativeProps方法。除非包装原生组件或传递样式属性给子类。

``` javascript
import React from 'react';
import { Text, TouchableOpacity, View } from 'react-native';

class MyButton extends React.Component {
  render() {
    return (
      <View>
        <Text>{this.props.label}</Text>
      </View>
    )
  }
}

export default class App extends React.Component {
  render() {
    return (
      <TouchableOpacity>
        <MyButton label="Press me!" />
      </TouchableOpacity>
    )
  }
}
```

##### 传递setNativeProps到子组件
TouchableOpacity实际上也是一个复合组件，依赖子组件的setNativeProps方法，同时需要子组件执行触摸事件处理。
TouchableHighlight受原生视图支持，仅需实现setNativeProps方法。

``` javascript
import React from 'react';
import { Text, TouchableOpacity, View } from 'react-native';

class MyButton extends React.Component {
  setNativeProps = (nativeProps) => {
    this._root.setNativeProps(nativeProps);
  }

  render() {
    return (
      <View ref={component => this._root = component} {...this.props}>
        <Text>{this.props.label}</Text>
      </View>
    )
  }
}

export default class App extends React.Component {
  render() {
    return (
      <TouchableOpacity>
        <MyButton label="Press me!" />
      </TouchableOpacity>
    )
  }
}
```

#### 使用setNativeProps方法清除输入文本
当用户输入很快且bufferDelay值低时TextInput控件的controlled属性有时会丢弃字符。一些开发人员偏好调用setNativeProps直接操作TextInput的值。

``` javascript
import React from 'react';
import { TextInput, Text, TouchableOpacity, View } from 'react-native';

export default class App extends React.Component {
  clearText = () => {
    this._textInput.setNativeProps({text: ''});
  }

  render() {
    return (
      <View style={{flex: 1}}>
        <TextInput
          ref={component => this._textInput = component}
          style={{height: 50, flex: 1, marginHorizontal: 20, borderWidth: 1, borderColor: '#ccc'}}
        />
        <TouchableOpacity onPress={this.clearText}>
          <Text>Clear text</Text>
        </TouchableOpacity>
      </View>
    );
  }
}
```

#### 避免与渲染方法冲突
如果调用setNativeProps修改的属性正被渲染方法调用，将产生冲突问题。
因为组件重渲染属性改变，之前setNativeProps设置的属性将忽略或被重写。

#### setNativeProps与shouldComponentUpdate
明智地应用shouldComponentUpdate可以避免重调整未改变的组件子树，因此为性能考虑应使用setState代替setNativeProps。

#### 其他的原生方法
这些方法被大多数的React Native的内置组件支持，在不受原生视图直接支持的复合组件上不可用。

* measure(callback)
	* 异步回调参数：x/y/width/height/pageX/pageY
	* 渲染完成前不可用，使用onLayout属性即时获取测量值
* measureInWindow(callback)
	* 异步回调参数：x/y/width/height
	* 如果React根视图在其他原生视图中内嵌，参数值为绝对坐标
* measureLayout(relativeToNativeNode, onSuccess, onFail)
	* 类似于measure，测量返回的是相对的x/y值
	* ReactNative.findNodeHandle(component)获取组件原生节点句柄
* focus()
	* 给定输入框或视图请求获取焦点，实际触发行为依赖于平台和视图类型
* blur()
	* 移除输入框或视图的焦点，与focus()方法相反

### 颜色引用
#### RGB
支持RGB和RGBA，十六进制和方法调用都可。

* '#f0f'
* '#FF00FF'
* 'rgb(255, 0, 255)'
* 'rgba(255, 255, 255, 1.0)'
* '#f0ff'
* '#ff00ff00'

#### 色调、饱和度和亮度
Hue, Saturation, Lightness。
支持方法调用。

* 'hsl(360, 100%, 100%)'
* 'hsla(360, 100%, 100%, 1.0)'

#### 透明度
使用英文作为'rgba(0, 0, 0, 0)'简单写法。

* 'transparent'

#### 颜色命名
遵守[CSS 3规范](https://www.w3.org/TR/css-color-3/#svg-color)。

* aliceblue (#f0f8ff)
* antiquewhite (#faebd7)
* aqua (#00ffff)

### 架构

#### MVC
* Action -- 事件源
* Controller -- 总控
* Model
* View

#### Flux
* User Interactions -- 事件源
* Action Creators -- 动作生成器，与Web API交互
* Actions -- 动作
* Dispatcher -- 动作分发
* Callbacks -- 动作消费，触发回调
* Store -- 数据存储
* Change Event + Store Queries -- 更新事件和数据
* React Views -- 更新视图

#### 数据流
* Actions -- 动作
* Dispatcher -- 动作分发
    * Message Store -- Message View(同时受到Thread View的影响)
    * Thread Store -- Thread View

#### Redux
结合Flux和函数式编程。
设计思想：Web应用是个状态机，视图与状态一一对应，所有状态都保存在一个对象里面。

##### Store
具有唯一性，保存数据的容器。

* redux.createStore(reducer)
	* getState()
	* dispatch(action) -- 传递action给Reducer
	* subscribe(listener) -- 监听状态改变，返回值为解除订阅的函数

##### Reducer
动作处理器，接受state和action入参，返回新的state。
redux中的combineReducers可以合并Reducers。
纯函数，遵守函数式编程约定：

* 不得改写参数
* 不能调用系统的I/O的API
* 不能调用Date.now()或Math.random()方法，相同的输入必须得到相同的输出

##### Action & ActionCreator

* Action -- 引起State变化的参数
	* type: 常量字符串
	* variables -- 动作数据
* ActionCreator -- 生成Action对象的方法

##### Component

* User Interaction -- 触发事件生成
* listener -- 更新State

#### react-redux

* actions
	* navigationActions
	* todosActions
	* addTodoAction(type, data...)
* redux.bindActionCreators(Actions, dispatch)
* reducers
	* redux.combineReducers
* store
	* createStore(reducers)
* react-redux.connect(map...)(App)
	* mapStateToProps
	* mapDispatchToProps
* react-redux.provider
	* store

### TODO Reactive App
#### 项目组织结构
* actions
	* index.js
	* navigation.js
	* todos.js
* components
	* broswer-view
	* common
		* Main.js
		* Header.js
	* completed-view
* constants
	* ActionType.js
	* ServerAPIs.js
* containers
	* App.js
	* BroswerView.js
* reducers
	* index.js
	* navigation.js
	* todos.js
* store
	* configurationStore.js
* styles
	* Basic.js
	* Index.js
	* Theme.js
* root.js

#### 命令约定
* 容器名：Pascal风格，如ModuleNameView.js
* 组件目录：全小写，短横线分隔，如module-name-view
* 事件方法名：handle + 事件名
* 渲染方法名：render + 方法名
* 类型映射：mapStateToProps, mapDispatchToProps
* 动作类型：全大写，下划线分隔
* reducer：根据处理的动作取有意义的名称
* 样式：根据功能划分，如Basic定义全局样式、Theme定义全局颜色和透明度

#### 组件和容器的区别
* 目的
	* 组件 -- 数据如何展示
	* 容器 -- 数据如何工作，如获取、状态更新
* Redux是否可感知
	* 组件 -- 否
	* 容器 -- 是
* 数据读取
	* 组件 -- 从属性中读取数据
	* 容器 -- 订阅Redux状态
* 数据改变
	* 组件 -- 从属性中调用回调
	* 容器 -- 分发redux动作
* 是否可写
	* 组件 -- 手写
	* 容器 -- react-redux生成

### 网络
#### Fetch
火狐开发的Web API，类似于XMLHttpRequest。

``` javascript
// 默认
fetch('https://mywebsite.com/mydata.json')

// 自定义
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  }),
});

// 处理
async function getMoviesFromApi() {
  try {
    let response = await fetch(
      'https://facebook.github.io/react-native/movies.json'
    );
    let responseJson = await response.json();
    return responseJson.movies;
  } catch (error) {
    console.error(error);
  }
}
```

#### XMLHttpRequest API
RN内置API，可直接使用，或使用封装后的frisbee或axios第三方开源库。

``` javascript
var request = new XMLHttpRequest();
request.onreadystatechange = (e) => {
  if (request.readyState !== 4) {
    return;
  }

  if (request.status === 200) {
    console.log('success', request.responseText);
  } else {
    console.warn('error');
  }
};

request.open('GET', 'https://mywebsite.com/endpoint/');
request.send();
```

#### WebSocket
支持WebSocket，单个TCP的全双工通信。
``` javascript
var ws = new WebSocket('ws://host.com/path');

ws.onopen = () => {
  // connection opened
  ws.send('something'); // send a message
};

ws.onmessage = (e) => {
  // a message was received
  console.log(e.data);
};

ws.onerror = (e) => {
  // an error occurred
  console.log(e.message);
};

ws.onclose = (e) => {
  // connection closed
  console.log(e.code, e.reason);
};
```


### 持久化

* AsyncStorage
* redux-persist

### 页面导航
#### React Navigation
npm安装react-navigation

``` javascript
import {
  StackNavigator,
} from 'react-navigation';

const App = StackNavigator({
  Home: { screen: HomeScreen },
  Profile: { screen: ProfileScreen },
});

class HomeScreen extends React.Component {
  static navigationOptions = {
    title: 'Welcome',
  };
  render() {
    const { navigate } = this.props.navigation;
    return (
      <Button
        title="Go to Jane's profile"
        onPress={() =>
          navigate('Profile', { name: 'Jane' })
        }
      />
    );
  }
}
```

#### NavigatorIOS
基于UINavigationController构建，react-native内置组件。

``` javascript
<NavigatorIOS
  initialRoute={{
    component: MyScene,
    title: 'My Initial Scene',
    passProps: {myProp: 'foo'},
  }}
/>
```

### 平台专用代码
实现平台独立的视图组件。
平台专用的属性要加上@platform注释。

#### 平台模块
使用Platform进行平台判断，适用于逻辑简单的代码。

* react-native.Platform
	* OS
		* ios
		* android
	* select()
	* Version

#### 平台专用文件扩展
在js后缀名前加ios/android平台扩展标识，RN会根据运行的平台加载相应的文件。
平台扩展标识不仅适用于代码也适用于资源文件。

``` javascript
BigButton.ios.js
BigButton.android.js

const BigButton = require('./BigButton');
```

### 资源文件
可使用平台扩展名添加平台专用资源。
require()用于react native方式静态引入图像、音视步和文档等。

#### 静态图像
类似于IOS可使用@2X, @3X后缀名为IOS设备提供不同精度的图像。
在Windows平台上，添加图像后，要重启打包工具。
代码中图片名称必须是静态的，而不是动态拼接的。
图像资源要求设置width和height，如果想动态的缩放图像，需要设置属性{ width: undefined, height: undefined }。
Image的source属性接收的是Object而不是字符串，是为了追加额外属性，如大小、裁剪等。

IOS忽略的边角样式属性有：

* borderTopLeftRadius
* borderTopRightRadius
* borderBottomLeftRadius
* borderBottomRightRadius

#### 静态非图像资源
支持格式：.mp3, .wav, .mp4, .mov, .html和.pdf。
修改打包配置文件可以支持其他类型资源。
**注意**：视频必须使用绝对定位，因为尺寸信息未传递给非图像资源。直接链接到Xcode或安卓assets文件夹的视频（Hybrid App），不受此限制。

#### 混合App静态资源
这种方式不提供安全性检查，必须自行确保资源可用。

``` javascript
# xcode
<Image source={{uri: 'app_icon'}} style={{width: 40, height: 40}} />
# android
<Image source={{uri: 'asset:/app_icon.png'}} style={{width: 40, height: 40}} />
```

#### 网络图像
必须指定图像尺寸，由于IOS的ATS(android transport security)要求，推荐使用https协议。

``` javascript
// GOOD
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}}
       style={{width: 400, height: 400}} />

// BAD
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}} />

// Custom
<Image
  source={{
    uri: 'https://facebook.github.io/react/logo-og.png',
    method: 'POST',
    headers: {
      Pragma: 'no-cache',
    },
    body: 'Your Body goes here',
  }}
  style={{width: 400, height: 400}}
/>
```

##### Uri数据图像
获取编码图像数据，使用scheme为data的uri地址。
推荐仅用于非常小的动态图像，比如DB中的图标。
``` javascript
// include at least width and height!
<Image
  style={{
    width: 51,
    height: 51,
    resizeMode: Image.resizeMode.contain,
  }}
  source={{
    uri:'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg==',
  }}
/>
```

##### 缓存控制（IOS）
* Image
	* source
		* cache
			* default
			* reload -- 不缓存
			* force-cache -- 不存在缓存时下载，否则加载缓存，无论是否过期
			* only-if-cache -- 不存在缓存时加载失败，否则加载缓存

##### 主线程外图像解析
图像解析在主线程外的线程执行，需要在图像加载过程中，显示占位图像。

### 动画
#### Animated API

##### 支持组件类型
* View
* Text
* Image
* ScrollView
* Animated.createAnimatedComponent()自定义组件

##### 执行动画

* Animated.start()
* Animated.stop()

##### 配置动画

* Animated.timing()
* Animated.spring()
* Animated.decay()

##### 组织动画

* Animated.sequence()
* Animated.parallel()

##### 动画插值计算

* Animated.Value()
	* interpolate() -- 支持多分段，字符映射
		* inputRange
		* outputRange
* Animated.divide()

##### 动态值跟跟踪
动画值可以跟踪其他值。Animated.ValueXY()，ValueXY()是用来处理二维交互平移和拖动的便捷方式。

``` javascript
Animated.spring(follower, {toValue: leader}).start();
Animated.timing(opacity, {
  toValue: pan.x.interpolate({
    inputRange: [0, 300],
    outputRange: [1, 0],
  }),
}).start();
```

##### 手势跟踪

* Animated.event()
	* nativeEvent
		* contentOffset
			* x
	* gestureState

##### 动画监听

* Animated.spring
	* stopAnimation(callback)
	* addListener(callback)

##### 本地驱动
动画是否序列化的。
useNativeDriver属性，将序列化后的动画传递给本地驱动执行，而不是使用JS桥接的方式。一旦动画开始执行，Js线程将被阻塞，不再对动画产生影响。
动画值仅兼容一个驱动，确保所有动画值使用相同驱动。
Animated.event同样支持useNativeDriver属性。由于React Native的异步特性，没有开启本地驱动的动画始终落后手势动作一帧。

``` javascript
Animated.timing(this.state.animatedValue, {
  toValue: 1,
  duration: 500,
  useNativeDriver: true, // <-- Add this
}).start();
```

###### 注意事项
本地驱动属性不适于所有动画。

* 不支持布局相关属性
	* 支持transform和opacity
	* 不支持flexbox和位置属性
* 不支持非直接事件
	* 支持ScrollView.onScroll
	* 不支持PanResponder
* 阻止VirtualizedList渲染更多行
	* 在滚动列表时执行长时间或循环动画，需要关闭isInteraction属性

##### 安卓注意事项
在样式中使用transform属性时，需要指定perspective属性，否则android上无法渲染该样式。

``` javascript
<Animated.View
  style={{
    transform: [
      {scale: this.state.scale},
      {rotateY: this.state.rotateY},
      {perspective: 1000}, // without this line this Animation will not render on Android while working fine on iOS
    ],
  }}
/>
```

#### LayoutAnimation API
用于全局配置用于下一场景的动画的创建和更新。

``` javascript
// 在Android平台使用时需要配置
UIManager.setLayoutAnimationEnabledExperimental &&
  UIManager.setLayoutAnimationEnabledExperimental(true);
```

#### 其他

##### requestAnimationFrame()
接收函数方法参数，让其在下次重绘前执行。是所有基于Js动画APIs的基础构件。一般情况下无需直接调用该方法。

##### setNativeProps()
允许直接修改本机支持的组件（不同于组合组件），而无需修改状态和重新渲染组件层次。
如果发现动画掉帧，则要考虑开启setNativeProps或shouldComponentUpdate进行优化。如果已经开启了useNativeDriver属性，则需要使用IteractionManager延迟密集型计算任务。
监测帧率工具：FPS Monitor。

### 辅助功能
由于iOS与Android平台提供的辅助技术不一致。故React Native的实现因平台不同而有所不同。

#### 辅助属性

* accessible -- 默认所有可触摸的组件可以访问
* accessibilityLabel -- 供语音读取
* accessibilityTraits(iOS) -- 告知访问的组件特征
	* none/button/link/header/...
* accessibilityViewIsModal(iOS) -- 是否忽略同级元素
* onAccessibilityTap(iOS) -- 双击触发函数
* onMagicTap(iOS) -- 双指双击触发函数
* accessibilityComponentType(Android)
	* none/button/radiobutton...
* accessibilityLiveRegion(Android) -- 当组件动态改变时，提示用户策略
	* none -- 不提醒
	* polite -- 提醒
	* assertive -- 中止语音立马提醒
* importantForAccessibility(Android) -- 组件重叠时，语音采取策略
	* auto
	* yes/no
	* no-hide-descendants

#### 辅助信息
Accessibilityinfo API可用于判断屏幕阅读当前是否激活。

#### 辅助事件（Android）
使用UIManager触发辅助函数。

``` javascript
import { UIManager, findNodeHandle } from 'react-native';

_onPress: function() {
  const radioButton = this.state.radioButton === 'radiobutton_checked' ?
    'radiobutton_unchecked' : 'radiobutton_checked'

  this.setState({
    radioButton: radioButton
  });

  if (radioButton === 'radiobutton_checked') {
    UIManager.sendAccessibilityEvent(
      findNodeHandle(this),
      UIManager.AccessibilityEventTypes.typeViewClicked);
  }
}

<CustomRadioButton
  accessibleComponentType={this.state.radioButton}
  onPress={this._onPress}/>
```

#### 测试语音辅助(iOS)
启用语音功能：【通用】-【辅助功能】-【画外音】。

### 用户交互
#### 配置文本输入
* 第一行自动获取焦点
* 显示期望数据格式提示文字
* 启用或禁用自动首字母大写和自动更正
* 选择键盘类型
* 配置返回按扭为下一项或提交功能

#### 键盘可见时管理布局

* KeyboardAvoidingView

#### 增大点击区域
确保可交互元素大小大于等于44x44。
改变大小属性：padding, minWidth, minHeight
不改变布局属性：hitSlop

#### 使用安卓波纹
安卓API 21启用了材质化设计波纹给予用户交互区域反馈。

* TouchableNativeFeedback -- 仅适用于安卓API 21及以上
* react-native-platform-touchable（github项目）

#### 屏幕方向锁定

XCode: 【通用】-【部署】-【设备方向】，操作前需要在设备菜单中指定iPhone
Android: 清单文件中的android:screenOrientation=”portrait”属性。

### 计时器

#### Timers

* set/clearTimeout -- 尽可能快地触发
* set/clearInterval
* set/clearImmediate
* request/cancelAnimationFrame -- 所有帧刷新后触发

#### InteractionManager
原生应用流畅的原因在于在交互和动画过程中避免了昂贵操作。
React Native限制了仅有一个js线程，配合InteractionManager可让交互或动画结束后执行长时间计划任务。

``` javascript
InteractionManager.runAfterInteractions(() => {
  // ...long-running synchronous task...
});

var handle = InteractionManager.createInteractionHandle();
// run animation... (`runAfterInteractions` tasks are queued)
// later, on animation completion:
InteractionManager.clearInteractionHandle(handle);
// queued tasks run if all handles were cleared
```

##### 不同调度方案的区别

* requestAnimationFrame() -- 用于随时间变化的动画代码
* setImmediate/setTimeout/setInterval() -- 延时执行，可能延迟动画
* runAfterInteractions() -- 延时执行，不影响活动动画

#### TimerMixin
用于解决组件卸载后计时器执行的问题。
非内置组件，需要安装react-timer-mixin。若使用EState6，推荐使用react-mixin。

``` javascript
import TimerMixin from 'react-timer-mixin';

var Component = createReactClass({
  mixins: [TimerMixin],
  componentDidMount: function() {
    this.setTimeout(() => {
      console.log('I do not leak!');
    }, 500);
  },
});
```

### 调试

#### 应用内开发者菜单
##### 启动快捷键（IOS）
RN支持少量用于IOS模拟器的快捷键。
启用方法：【硬件菜单】-【键盘】-【连接硬件键盘】

##### 访问应用内开发者菜单
进入菜单方法：摇动设备或选择IOS模拟器中的硬件菜单中的摇动手势、Android模拟器的M菜单。
快捷键方法：⌘+D（IOS）, Ctrl+M(Android)
adb方法：
``` bash
$ adb shell input keyevent 82
```

##### 重载JS
无需重编译，通过重载即可即时运行JS代码
开启自动重载：开发者菜单【启用实时加载】
开启热重载：开发者菜单【热重载】，功能实现不完全，出现问题时，重加载重置应用

手动重载方法：

* 开发者菜单【重载】
* 快捷键
	* IOS - ⌘ + R
	* Android - R键双击

有以下情况时，必须要重新编译

* 添加新资源文件到原生App包内，如android的res/drawable，xcode的Images.xcassets。
* 修改了原生代码（OC/Swift/Java/C++）。

##### 性能监控
开发者菜单中【Perf 监视器】

#### 应用内错误与警告
错误和警告显示在应用程序的开发版本中。正式版中自动禁用。

##### 错误
以全屏的红色警告框显示，称为红框。可使用console.error()手动触发。

##### 警告
黄底显示，称为黄框。可使用console.warn()手动触发。

抑制警告：

* 全部禁用
	* console.disableYellowBox = true;
	* IS_TESTING(CI/Xcode中)

* 部分禁用
``` javascript
import {YellowBox} from 'react-native';
YellowBox.ignoreWarnings(['Warning: ...']);
```

#### 谷歌开发工具
开发者菜单【远程调试JS】
调试网址：http://localhost:8081/debugger-ui

F12快捷键打开开发者工具。推荐启用捕获异常暂时功能。
**注意**：React Developer Tools扩展插件不可用。

#### 自定义JS调试器
这种方式执行的调试应该是个短期进程，不应超过200kB输出。

* 设置REACT_DEBUGGER环境变量
* 开发者菜单【远程调试JS】

```
REACT_DEBUGGER="node /path/to/launchDebugger.js --port 2345 --type ReactNative
// 远程调试JS触发命令内容：
$ node /path/to/launchDebugger.js --port 2345 --type ReactNative /path/to/reactNative/app
```

#### React Developer Tools
独立版，非Chrome插件版。

``` bash
// 全局安装
$ npm install -g react-devtools
$ react-devtools

// 仅当前项目安装，命令执行目录，项目目录
$ npm install --save-dev react-devtools
// 添加"react-devtools": "react-devtools"到package.json/scripts区域
$ npm run react-devtools
```

#### 调试弹出的应用程序
弹出指的是为CRNA配置自定义构建的过程。
这里介绍对react-native init或CRNA(Create React Native App)生成的且弹出的项目进行调试的方法。

##### 访问控制台日志
CRNA默认开启控制台日志。

``` bash
// terminal
$ react-native log-ios
$ react-native log-android
// ios
【调试】-【开启系统日志】
// android
$ adb logcat *:S ReactNative:V ReactNativeJS:V
```

##### Chrome Developer Tools
CRNA默认已配置。
如果调试过程中出现异常，可能是扩展不兼容的问题，请禁用全部扩展，一个一个启用排查问题。

* IOS
	* 修改RCTWebSocketExecutor.m中的localhoast的IP地址为电脑IP地址
	* 开发者菜单【远程调试JS】
* Android
	* Android 5.0及以上设备
	* adb reverse tcp:8081 tcp:8081
* 通用
	* 开发者菜单【设备配置】
	* 【设备的调试服务主机】为电脑IP地址

##### Stetho(Android)
* 添加依赖项
* 添加Stetho包装类
* 初始化Stetho
* 运行App -- react-native run-android
* chrome://inspect 【内省设备】

``` java
debugCompile 'com.facebook.stetho:stetho:1.5.0'
debugCompile 'com.facebook.stetho:stetho-okhttp3:1.5.0'
// debug构建类，release构建类中方法体为空。
public class StethoWrapper {
    public static void initialize(Context context) {
      Stetho.initializeWithDefaults(context);
    }

    public static void addInterceptor() {
      OkHttpClient client = OkHttpClientProvider.getOkHttpClient()
             .newBuilder()
             .addNetworkInterceptor(new StethoInterceptor())
             .build();

      OkHttpClientProvider.replaceOkHttpClient(client);
    }
}
// 在自定义的Application中初始化
public void onCreate() {
  super.onCreate();

  if (BuildConfig.DEBUG) {
      StethoWrapper.initialize(this);
      StethoWrapper.addInterceptor();
  }

  SoLoader.init(this, /* native exopackage */ false);
}
```

### 性能
#### 60帧
流畅动画要求每秒60帧，一帧执行时间在16.67ms内，用户感知延迟的时间为100ms。
##### JS帧率（JS线程）
大多业务逻辑运行在JS线程上。
丢帧：Js线程对帧没有反应。

##### UI帧率（主线程）
动画由原生主线程而非Js线程执行。

#### 常见性能问题
##### 开发模式运行（dev=true）
使用发行版测试性能。

##### 使用console.log语句
删除console.log语句或调试库redux-logger。
可使用babel插件移除console.*调用。

``` bash
$ npm i babel-plugin-transform-remove-console --save
$ gedit .babelrc

{
  "env": {
    "production": {
      "plugins": ["transform-remove-console"]
    }
  }
}
```

##### ListView初始化渲染大型列表
使用FlatList或SectionList组件替代。
实现getItemLayout跳过渲染项目测量来优化渲染速度。

##### 重新渲染未改变的视图
ListView: 提供rowHasChanged函数
使用不可变的数据结构，浅层比较代替深层比较
实现shouldcomponentupdate指明更新条件

##### Js线程同一时间执行大量任务
使用InteractionManager
考虑LayoutAnimation -- 仅适用于不可中断的静态动画
设置useNativeDriver

##### 屏幕移动（平移、旋转）视图
启用shouldRasterizeIOS或renderToHardwareTextureAndroid。
注意不要滥用此配置，以免耗尽内存。

##### 动画中图像的大小
在IOS上，调整图像组件大小，都会从原始图像重新裁剪并缩放。
使用transform: [{scale}]样式属性来设置动画大小。

##### 可触摸组件响应不及时
组件事件响应函数中执行了大量任务引发。

``` javascript
handleOnPress() {
  // Always use TimerMixin with requestAnimationFrame, setTimeout and
  // setInterval
  this.requestAnimationFrame(() => {
    this.doExpensiveAction();
  });
}
```

##### 缓慢导航过渡
原因：导航动画由Js线程实现。
解决方案，移交给主线程执行。

#### 分析
关闭开发模式。

* 使用开发者菜单【性能监视器】
* Instruments工具(IOS)
* systrace(Android)
* 谷歌开发工具【性能】（不准确）

##### 使用systrace分析安卓UI性能
###### 收集分析数据
time：数据收集用时
sched：调度信息
gfx: 图像信息，如帧边界
view: 测量、布局和绘图用时

``` bash
$ sdk/platform-tools/systrace/systrace.py --time=10 -o trace.html sched gfx view -a {packagename}
```

###### 查看分析数据
用chrome打开trace.html文件，使用WASD键进行缩放。
如果控制台输出Object.observe不是一个方法的错误，则要使用谷歌追踪工具打开：

* chrome://tracing
* 选择加载
* 选择trace.html
* 启用16ms帧边界高亮功能 -- 已经三星手机不支持该功能

###### 找到应用进程
假如分析包名为com.facebook.adsmanager，因为内核名称限制，实际名称是book.adsmanager。

左侧会显示如下线程：

* UI线程 -- 关键字Choreographer, traversals和DispatchUI
* Js线程 -- 关键字JSCall, Bridge.executeJSCall
* 原生模块线程(UIManager) -- 关键字NativeCall, callJavaModuleMethod和onBatchComplete
* 渲染线程 -- 关键字DrawFrame和queueBuffer

###### 识别问题
交错颜色区别每一帧。一帧代表16ms，所有的UI操作都要在16ms内完成。
对超出一帧边界的方法进行排查。

* Js线程问题排查
	* shouldComponentUpdate
* UI线程问题排查
	* onDraw
	* 动画或交互过程中创建了新的UI
* 渲染线程问题排查
	* renderToHardwareTextureAndroid配置
	* needsOffscreenAlphaCompositing是否禁用
	* 更详细信息使用Tracer for OpenGL ES工具

#### 拆分和内联需求
应用程序比较大时，需要考虑拆分和使用内联需求。
使用打包器的unbundle功能优化软件包的加载，要求实际使用时与屏幕内联。

##### 加载JS
react-native执行JS代码前，需要将代码加载到内存。如果JS代码大小为50M，不拆分则必须加载50M，拆分后可以仅加载需要加载的部分，其他部分随着运行需要逐渐加载。

##### 内联需求
在代码中增加判断，实现需要时加载。

##### 启用拆分功能
IOS: 拆分功能将创建一个索引文件，RN据此一次加载一个模块。
Android: 拆分功能为每个模块创建一组文件，也可以强制生成单个文件（不推荐）。

``` bash
// Xcode，修改构建短语Bundle React Native code and images
export BUNDLE_COMMAND="unbundle" -- 添加当前行
export NODE_BINARY=node ../node_modules/react-native/packager/react-native-xcode.sh

// Android
apply from: "../../node_modules/react-native/react.gradle"
// or
project.ext.react = [
  bundleCommand: "unbundle",
  xtraPackagerArgs: ["--indexed-unbundle"] -- 单个索引文件
]
```

##### 配置预加载和内联需求
拆分代码后，调用require加载时需要额外开销的。遇到未加载的模块，require需要给JsBridge发送消息。应用启动时会受到较大影响，配置部分模块预加载可以降低影响。

###### 添加打包器配置文件
1. 创建packager文件夹，创建config.js文件，内容如下：
``` javascript
const config = {
  getTransformOptions: () => {
    return {
      transform: { inlineRequires: true },
    };
  },
};
module.exports = config;
```

2. 修改Xcode的构建短语：
``` bash
export BUNDLE_COMMAND="unbundle"
export BUNDLE_CONFIG="packager/config.js" -- 添加当前行
export NODE_BINARY=node
../node_modules/react-native/packager/react-native-xcode.sh
```

3. 修改项目build.gradle文件
``` groovy
project.ext.react = [
  bundleCommand: "unbundle",
  bundleConfig: "packager/config.js"
]
```

4. 更新package.json中脚本区域的start配置
``` json
"start": "node node_modules/react-native/local-cli/cli.js start --config ../../../../packager/config.js"
```

5. 启动包服务器
``` bash
$ npm start
$ react-native run-android
```

###### 调查加载的模块
在根文件index.[ios|android].js中，在初始导入后添加如下内容：
``` javascript
const modules = require.getModules();
const moduleIds = Object.keys(modules);
const loadedModuleNames = moduleIds
  .filter(moduleId => modules[moduleId].isInitialized)
  .map(moduleId => modules[moduleId].verboseName);
const waitingModuleNames = moduleIds
  .filter(moduleId => !modules[moduleId].isInitialized)
  .map(moduleId => modules[moduleId].verboseName);

// make sure that the modules you expect to be waiting are actually waiting
console.log(
  'loaded:',
  loadedModuleNames.length,
  'waiting:',
  waitingModuleNames.length
);

// grab this text blob, and put it in a file named packager/moduleNames.js
console.log(`module.exports = ${JSON.stringify(loadedModuleNames.sort())};`);
// 根据需要添加如下内容，修改模块名，使用systrace调查有问题的加载模块
require.Systrace.beginEvent = (message) => {
  if(message.includes(problematicModule)) {
    throw new Error();
  }
}
```

每个App都不同，因此只需加载首屏所需模块即可，当调试完成后，将loadedmodulenames的输出放入packager/modulename.js中。

###### 修改模块路径
modulename.js存放的是模块名，实际加载时需要的是模块路径，因此添加packager/generateModulePaths.js完成这一任务：

``` javascript
// @flow
/* eslint-disable no-console */
const execSync = require('child_process').execSync;
const fs = require('fs');
const moduleNames = require('./moduleNames');

const pjson = require('../package.json');
const localPrefix = `${pjson.name}/`;

const modulePaths = moduleNames.map(moduleName => {
  if (moduleName.startsWith(localPrefix)) {
    return `./${moduleName.substring(localPrefix.length)}`;
  }
  if (moduleName.endsWith('.js')) {
    return `./node_modules/${moduleName}`;
  }
  try {
    const result = execSync(
      `grep "@providesModule ${moduleName}" $(find . -name ${moduleName}\\\\.js) -l`
    )
      .toString()
      .trim()
      .split('\n')[0];
    if (result != null) {
      return result;
    }
  } catch (e) {
    return null;
  }
  return null;
});

const paths = modulePaths
  .filter(path => path != null)
  .map(path => `'${path}'`)
  .join(',\n');

const fileData = `module.exports = [${paths}];`;

fs.writeFile('./packager/modulePaths.js', fileData, err => {
  if (err) {
    console.log(err);
  }

  console.log('Done');
});
```

这个方案不是绝对可靠，因为它忽略了特定于平台的文件，初步测试，可以处理95%以上的案例。

###### 更新config.s
添加新生成的modulePath.js文件。
preloadedModules指明bundle被加载require执行前，需要加载的模块。这些模块已经预加载，内联需求不会带来性能提升，需要配置在内联需求的backlist中，因为Js处理引入导入时会花费额外时间解析内联需求。

``` javascript
const modulePaths = require('./modulePaths');
const resolve = require('path').resolve;
const fs = require('fs');

const config = {
  getTransformOptions: () => {
    const moduleMap = {};
    modulePaths.forEach(path => {
      if (fs.existsSync(path)) {
        moduleMap[resolve(path)] = true;
      }
    });
    return {
      preloadedModules: moduleMap,
      transform: { inlineRequires: { blacklist: moduleMap } },
    };
  },
};

module.exports = config;
```

###### 测试性能改进
在使用拆分和内联需求前，确保记录使用前后的启动时间。

### 与原生应用集成
#### IOS（未验证）
##### 关键概念
* 安装RN依赖项和目录结构
* 理解将要使用的RN组件
* 使用CocoaPods添加组件依赖
* 使用JS开发RN组件
* 添加RCTRootView到App，它作为容器服务于RN组件
* 运行RN服务，运行App
* 验证App中的RN部分是否动作正常

##### 先决条件
###### 建立目录结构
为保证顺畅体验，复制ios源项目到ios子文件夹
###### 安装JS依赖
* 在根目录创建package.json文件
``` javascript
{
  "name": "MyReactNativeApp",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  }
}
```

* npm安装react-native-cli和yarn，在根目录执行react-native init命令
``` bash
$ npm install -g react-native-cli yarn
$ react-native init
```

* 添加node_modules到.gitignore文件

###### 安装CocoaPods
``` bash
$ brew install cocoapods
```

##### 添加RN到App
###### 配置CocoaPods依赖
使用CocoaPods配置App需要的子特性。
支持的子特性列表目录：node_modules/react-native/React.podspec。
子特性根据功能命名，如核心特性提供AppRegistry, StyleSheet, View和其他核心RN库。RCTText特性提供Text库，RCTImage特性提供Image库。
可通过Podfile指定依赖的子特性。在ios文件夹下运行CocoaPods初始化命令。该初始化命令提供一个样板文件，可根据需要进行修改，完成后执行install命令。

``` bash
$ pod init
$ pod install
```

###### 代码集成
index.js是RN应用的入口。可以是个简单文件，仅require其他文件，也可以包含全部的运行代码。

* 在根目录创建index.js文件
* 在index.js中添加RN代码实现界面
	* 定义组件
	* 使用AppRegistry.registerComponent注册组件
* 添加RCTRootView到App
	* 添加访问入口
	* 创建RCTRootView，配置参数
		* initWithBundleURL
		* moduleName
		* initialProperties
		* launchOptions
* 测试集成
	* 添加ATS例外，IOS不允许直接访问HTTP资源
	* 运行JS服务器：npm run start
	* 运行应用：react-native run-ios
* 发行版前准备，RN打包
	* 运行react-native-xcode.sh执行预打包
	* 替换RCTRootView的NSURL参数
``` object c
[[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
```

#### Android
##### 关键概念

* 创建RN依赖和目录结构
* 使用JS开发RN组件
* 添加ReactRootView到App
* 运行RN服务和应用
* 验证App上的RN界面功能

##### 先决条件
###### 建立目录结构
拷贝Android项目到android文件夹。

###### 安装JS依赖
* 在根目录创建package.json文件，添加node_modules到.gitignore文件
``` javascript
{
  "name": "MyReactNativeApp",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start"
  }
}
```

* npm安装react-native-cli和yarn，在根目录执行react-native init命令
``` bash
$ npm install -g react-native-cli yarn
$ react-native init // 自动安装react, react-native依赖
```

* 添加node_modules到.gitignore文件

##### 添加RN到App
###### 配置Maven依赖
* 在项目build.gradle文件中添加依赖
``` groovy
dependencies {
    compile 'com.android.support:appcompat-v7:23.0.1'
    ...
    compile "com.facebook.react:react-native:0.54.4" // 在 node_modules/react-native/android中查看最新版本
}
```

* 在根build.gradle配置本地maven
``` groovy
allprojects {
    repositories {
        maven {
            // All of React Native (JS, Android binaries) is installed from npm
            url "$rootDir/../node_modules/react-native/android"
        }
        ...
    }
    ...
}
```

###### 清单配置
* 配置权限INTERNET
* 注册DevSettingsActivity

###### 代码集成
* 在根目录创建index.js文件
* 添加RN代码，编写页面布局
* 配置开发错误覆盖权限SYSTEM_ALERT_WINDOW（API 23及以上）
* 添加ReactRootView到App
	* 创建自定义Activity, 创建ReactRootView
	* 注册自定义Activity到清单，主题为NoActionBar
	* 重写生命周期方法，交由ReactInstanceManager控制
	* hook设备键方法onKeyUp

* 测试集成
	* 运行App
	* 运行JS服务器：yarn run start
	* 打开开发者菜单配置JS服务器地址和端口
	* 开发者菜单【启用】
	* 开发者菜单【重载JS】或退出应用再访问RN页面

* 发布前RN打包
``` bash
// 添加到package.json的scripts块中
"bundle-android": "react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/{app-package-name}/src/main/assets/index.android.bundle --assets-dest android/{app-package-name}/src/main/res/"
$ yarn run bundle-android
```

### TV设备构建
#### Android
##### 构建变更
###### 清单文件
``` xml
 <application
  ...
  android:banner="@drawable/tv_banner"
  >
    ...
    <intent-filter>
      ...
      <!-- Needed to properly create a launch intent when running on Android TV -->
      <category android:name="android.intent.category.LEANBACK_LAUNCHER"/>
    </intent-filter>
    ...
  </application>
```

###### JS代码
安卓TV支持已经添加到Platform.android.js中。

验证代码如下：

``` javascript
var Platform = require('Platform');
var running_on_android_tv = Platform.isTV;
```

##### 代码变更

###### 触摸控制
Touchable混合用于检测焦点改变和使用已知方法在TV遥控器选择视图时改变组件样式和初始化正确行为。

* TouchableHighlight/TouchableOpacity/TouchableNativeFeedback
	* touchableHandleActivePressIn -- 获取焦点
	* touchableHandleActivePressOut -- 失去焦点
	* touchableHandlePress -- 选择

###### TV控制器输入
ReactAndroidTVRootViewHelper原生类创建为TV遥控器事件创建按键事件处理器。当TV遥控事件产生，该类发出JS事件。
JS事件被TVEventHandler接收处理，开发都要自定义TV遥控器事件的处理，创建TVEventHandler，并监听这些事件。

``` javascript
var TVEventHandler = require('TVEventHandler');

...

class Game2048 extends React.Component {
  _tvEventHandler: any;

  _enableTVEventHandler() {
    this._tvEventHandler = new TVEventHandler();
    this._tvEventHandler.enable(this, function(cmp, evt) {
      if (evt && evt.eventType === 'right') {
        cmp.setState({board: cmp.state.board.move(2)});
      } else if(evt && evt.eventType === 'up') {
        cmp.setState({board: cmp.state.board.move(1)});
      } else if(evt && evt.eventType === 'left') {
        cmp.setState({board: cmp.state.board.move(0)});
      } else if(evt && evt.eventType === 'down') {
        cmp.setState({board: cmp.state.board.move(3)});
      } else if(evt && evt.eventType === 'playPause') {
        cmp.restartGame();
      }
    });
  }

  _disableTVEventHandler() {
    if (this._tvEventHandler) {
      this._tvEventHandler.disable();
      delete this._tvEventHandler;
    }
  }

  componentDidMount() {
    this._enableTVEventHandler();
  }

  componentWillUnmount() {
    this._disableTVEventHandler();
  }
```

###### 设备菜单支持
模拟器上点击M菜单键会打开开发者菜单。在安卓TV遥控器上，要长按【播放/暂停】键。

###### 已知问题
* TextInput不可用 -- 无法获取到焦点

### 真机运行
#### Android
##### App运行

1. 打开开发者选项，开启调试模式
	* 设置-关于手机-构建号上点击七次
1. USB连接手机（确保只一个连接手机）
1. 运行App: react-native run-android

##### 连接到开发服务器
###### 使用ADB反向
要求Android手机系统5.0及以上。

``` bash
$ adb reverse tcp:8081 tcp:8081
```

###### 使用Wifi连接

1. 手机和电脑在同一网络
2. 打开手机上的RN App
3. 打开应用内开发者菜单
4. 打开设备设置-设备调试服务主机，输入电脑IP地址，端口8081
5. 返回开发者菜单，选择加载JS

### 升级RN版本
#### CRNA
参照github上的项目修改package.json中对应的react-native, expo和app.json中sdkVersion的版本。
[react-community/create-react-native-app](https://github.com/react-community/create-react-native-app/blob/master/react-native-scripts/template/README.md#updating-to-new-releases)

#### 混合项目
react-native init和自定义构建的CRNA(弹出)。

##### 基于Git升级
react-native-git-upgrade模块提供功能，无需安装新版react-native包：

* 根据旧版和新版模板文件计算Git补丁
* 应用补丁到源代码

###### 升级步骤

* 安装Git，配置执行文件到PATH目录
* npm安装react-native-git-upgrade
* 执行react-native-git-upgrade命令，推荐注明升级版本、
* 解决冲突

##### 其他

* yarn安装最新版本react native
``` bash
$ yarn info react-native
$ yarn add react-native
```

* 更新项目模板
``` bash
$ react-native init
$ react-native upgrade
```

##### 手动升级
根据版本发行日志，识别需要手动更改的地方。

### 原生模块
为了复用原生代码或获取高性能，app有时需要访问平台API，RN未提供相应模块。
RN允许编写原生代码，访问全部平台特性。

#### IOS

##### 原生模块实现步骤
[iOS Calendar Module Example](https://facebook.github.io/react-native/docs/native-modules-ios.html#ios-calendar-module-example)

1. 实现RCTBridgeModule协议
2. 实现类调用RCT_EXPORT_MODULE()宏，公开原生模块
3. 调用RCT_EXPORT_METHOD()宏，公开方法给JS
4. JS文件中导入原生模块并调用模块方法

RCT_EXPORT_METHOD使用第一个冒号前的部分原生方法名作为JS方法名，RCT_REMAP_METHOD宏用于存在多个原生方法名第一个冒号前名称重复的情况。

原生模块在OC代码中调用[CalendarManager new]实例化。桥接方法的返回值一直是void。因为RN的桥方法是异步的，返回值给JS只能通过回调或事件发送方式。

##### 参数类型
RCT_EXPORT_METHOD支持标准JSON对象类型。

* 字符串 (NSString)
* 数值 (NSInteger, float, double, CGFloat, NSNumber)
* 布尔值 (BOOL, NSNumber)
* 数组 (NSArray) of any types from this list
* 键为字符串，值为支持的数据类型的对象 (NSDictionary)
* 方法 (RCTResponseSenderBlock)
* RCTConvert支持的类型

##### 回调
**注意**：这部分是内容是实验性质的。

RCTResponseSenderBlock仅接受一个参数，参数数组。
参数数组遵守Node约定，第一个参数为Error对象，其他的则为方法结果。
原生模块只应调用回调一次，可以存储回调，之后再调用。这种模式通常用于包装需要委托的iOS API方法，比如RCTAlertManager。
如果回调从未调用，会有些内存泄漏。
如果同时传递了onSuccess和onFail回调，只需调用其中一个。
如果想传递错误类对象给JS，使用RCTUtils.h中的RCTMakeError。目前仅传递错误样式的字典给JS，未来可能实现自动生成JS的Error对象。

##### Promises
原生模块可实现Promises来简化代码，特别是使用ES2016的async/await语法时。
当桥接原生方法的最后两个参数是RCTPromiseResolveBlock和RCTPromiseRejectBlock时，相应的JS方法会返回一个JS Promise对象。意味着你可以在async函数中使用await关键字调用js方法并等待返回结果。

``` javascript
async function updateEvents() {
  try {
    var events = await CalendarManager.findEvents();

    this.setState({events});
  } catch (e) {
    console.error(e);
  }
}

updateEvents();
```

##### 线程
原生方法不应对调用方法的线程做任何假定。
RN在一个独立的串行GCD队列中调用原生模块方法，但这个实现细节可能会改变。
- (dispatch_queue_t)methodQueue方法允许原生模块指定调用方法的队列。
RCTAsyncLocalStorage模块可以创建自己的队列，避免React队列不在等待可能较慢的磁盘访问时被阻止。
指定的methodQueue将对模块所有的方法共享。如果只有一个方法是长时间执行的，在方法内部使用dispatch_async在另一个队列上执行特定方法的代码，而不影响其他队列。

###### 模块间共享队列
模块初始化时methodQueue会调用一次，之后由桥接器保存，无需自行保存，除非需要在模块中使用。
如果需要在多个模块间共享相同的队列，则需要自行保存且返回相同的队列实例，不能仅仅返回一个同名称的队列。

##### 依赖注入
桥接器自动初始化任何已注册的RCTBridgeModules。有里需要自行初始化原生模块实例。

* 实现RCTBridgeDelegate协议
* 使用委托初始化RCTBridge
* 使用初始化的RCTBridge初始化RCTRootView

##### 导出常量
原生模块可以在运行时导出常量。这功能在传递静态数据时非常有用，否则这些数据传递需要通过桥接器。
注意常量导出仅发生在模块初始化时，运行过程中更改constantsToExport的值不会影响到JS环境。

``` oc
- (NSDictionary *)constantsToExport
{
  return @{ @"firstDayOfTheWeek": @"Monday" };
}
```

JS使用
``` javascript
console.log(CalendarManager.firstDayOfTheWeek);
```

###### 枚举常量
NS_ENUM定义的枚举需要扩展RCTConvert。

``` oc
@implementation RCTConvert (StatusBarAnimation)
  RCT_ENUM_CONVERTER(UIStatusBarAnimation, (@{ @"statusBarAnimationNone" : @(UIStatusBarAnimationNone),
                                               @"statusBarAnimationFade" : @(UIStatusBarAnimationFade),
                                               @"statusBarAnimationSlide" : @(UIStatusBarAnimationSlide)}),
                      UIStatusBarAnimationNone, integerValue)
@end
```

也可以用导出常量方法定义枚举常量：
``` oc
- (NSDictionary *)constantsToExport
{
  return @{ @"statusBarAnimationNone" : @(UIStatusBarAnimationNone),
            @"statusBarAnimationFade" : @(UIStatusBarAnimationFade),
            @"statusBarAnimationSlide" : @(UIStatusBarAnimationSlide) };
};

RCT_EXPORT_METHOD(updateStatusBarAnimation:(UIStatusBarAnimation)animation
                                completion:(RCTResponseSenderBlock)callback)
```

##### 发送事件到JS
原生模块可以直接发送事件到JS，无需直接调用。

* 继承RCTEventEmitter
* 实现supportedEvents
* 调用self sendEventWithName
* 创建NativeEventEmitter(customNativeModule)实例
* 添加和移除监听器

###### 优化零听众
为了避免在零听众情况下发布事件消耗不必要资源，可重写RCTEventEmitter类中的startObserving和stopObserving，在其中修改是否有听众标识。

##### 导出Swift
Swift不支持宏语法。而是使用@objc注解标识导出模块和导出方法。
使用oc创建一个私有实现文件，为RN桥接器注解模块提供所需信息，这里使用了React/RCTBridgeModule中的RCT_EXTERN_MODULE和RCT_EXTERN_METHOD宏。

##### 第三方模块制作注意事项
swift静态库要求Xcode 9或以上版本。
为了在包含iOS静态库的Xcode项目中使用swift，App项目中必须包含swift代码和oc桥接头。如果应用中不含任何swift代码，swift和桥接头文件可以是空的。

#### Android
推荐开启Gradle守护进程，加速构建。在gradle.properties中添加org.gradle.daemon=true。

##### Toast模块

### 原生UI组件
RN仅包装了最关键的平台组件，如ScrollView和TextInput。
RN提供了包装已有原生UI组件的方法，使其无缝接入到RN App中。

#### IOS
[iOS MapView example](https://facebook.github.io/react-native/docs/native-components-ios.html#ios-mapview-example)

##### 公开原生UI组件步骤
这里前缀名推荐使用RNT，为了不与其他框架产生冲突。

1. 继承RCTViewManager，实现组件管理者
2. 添加RCT_EXPORT_MODULE()标识宏
3. 实现-(UIView *)view方法
	* 不要设置UIView实例的frame或backgroundColor属性，JS会重写
	* 若有要求，实现-(UIView *)view方法返回实例的子类，追加属性

MapView包装组件代码：
``` oc
// RNTMapManager.m
#import <MapKit/MapKit.h>
#import <React/RCTViewManager.h>

@interface RNTMapManager : RCTViewManager
@end

@implementation RNTMapManager

RCT_EXPORT_MODULE()

- (UIView *)view
{
  return [[MKMapView alloc] init];
}

@end
```

在JS中导入使用组件（MapView暂时不能使用JS进行控制。）：
``` javascript
// MapView.js

import { requireNativeComponent } from 'react-native';

// requireNativeComponent automatically resolves 'RNTMap' to 'RNTMapManager'
module.exports = requireNativeComponent('RNTMap', null);

// MyApp.js

import MapView from './MapView.js';

...

render() {
  return <MapView style={{ flex: 1 }} />;
}
```

##### 属性
封装原生UI组件的一个好处在于可以通过桥接器扩展一些原生属性。

###### 公开UI组件属性

简单属性：
``` oc
// RNTMapManager.m
RCT_EXPORT_VIEW_PROPERTY(zoomEnabled, BOOL)
```

复杂属性：
``` oc
// RNTMapManager.m
RCT_CUSTOM_VIEW_PROPERTY(region, MKCoordinateRegion, MKMapView)
{
  // 当MKCoordinateRegion为null时使用defaultView.region
  [view setRegion:json ? [RCTConvert MKCoordinateRegion:json] : defaultView.region animated:YES];
}
```

自定义类型转换RCTConvert:
``` oc
// RNTMapManager.m

#import "RCTConvert+Mapkit.m"

// RCTConvert+Mapkit.h

#import <MapKit/MapKit.h>
#import <React/RCTConvert.h>
#import <CoreLocation/CoreLocation.h>
#import <React/RCTConvert+CoreLocation.h>

@interface RCTConvert (Mapkit)

+ (MKCoordinateSpan)MKCoordinateSpan:(id)json;
+ (MKCoordinateRegion)MKCoordinateRegion:(id)json;

@end

@implementation RCTConvert(MapKit)

+ (MKCoordinateSpan)MKCoordinateSpan:(id)json
{
  json = [self NSDictionary:json];
  return (MKCoordinateSpan){
    [self CLLocationDegrees:json[@"latitudeDelta"]],
    [self CLLocationDegrees:json[@"longitudeDelta"]]
  };
}

+ (MKCoordinateRegion)MKCoordinateRegion:(id)json
{
  return (MKCoordinateRegion){
    [self CLLocationCoordinate2D:json],
    [self MKCoordinateSpan:json]
  };
}

@end
```

###### 使用UI组件属性
``` js
// MyApp.js
<MapView zoomEnabled={false} style={{flex: 1}} />
```

###### 文档注释公开属性
MapView是RNTMap对应的包装类，有助于属性类型和原生类型的验证，减少OC和JS代码的不匹配问题。


``` js
// MapView.js
import PropTypes from 'prop-types';
import React from 'react';
import {requireNativeComponent} from 'react-native';

class MapView extends React.Component {
  render() {
    return <RNTMap {...this.props} />;
  }
}

// MapView.js

MapView.propTypes = {
  /**
   * A Boolean value that determines whether the user may use pinch
   * gestures to zoom in and out of the map.
   */
  zoomEnabled: PropTypes.bool,

  /**
   * The region to be displayed by the map.
   *
   * The region is defined by the center coordinates and the span of
   * coordinates to display.
   */
  region: PropTypes.shape({
    /**
     * Coordinates for the center of the map.
     */
    latitude: PropTypes.number.isRequired,
    longitude: PropTypes.number.isRequired,

    /**
     * Distance between the minimum and the maximum latitude/longitude
     * to be displayed.
     */
    latitudeDelta: PropTypes.number.isRequired,
    longitudeDelta: PropTypes.number.isRequired,
  }),
};


var RNTMap = requireNativeComponent('RNTMap', MapView);

module.exports = MapView;

// MyApp.js
render() {
  var region = {
    latitude: 37.48,
    longitude: -122.16,
    latitudeDelta: 0.1,
    longitudeDelta: 0.1,
  };
  return (
    <MapView
      region={region}
      zoomEnabled={false}
      style={{ flex: 1 }}
    />
  );
}
```

###### 私有化属性
私有部分原生UI组件属性。

``` javascript
var RCTSwitch = requireNativeComponent('RCTSwitch', Switch, {
  nativeOnly: {onChange: true},
});
```

##### 事件
传递事件给原生UI组件。
不能直接添加属性给-(UIView *)view方法返回的MKMapView实例，而是添加给其子类。

###### 添加属性onRegionChange
``` oc
// RNTMapView.h

#import <MapKit/MapKit.h>

#import <React/RCTComponent.h>

@interface RNTMapView: MKMapView

@property (nonatomic, copy) RCTBubblingEventBlock onRegionChange;

@end

// RNTMapView.m

#import "RNTMapView.h"

@implementation RNTMapView

@end
```

###### 定义事件处理属性
在RNTMapManager中定义事件处理属性，委托它调用管理的组件的事件处理器。

``` oc
// RNTMapManager.m

#import <MapKit/MapKit.h>
#import <React/RCTViewManager.h>

#import "RNTMapView.h"
#import "RCTConvert+Mapkit.m"

@interface RNTMapManager : RCTViewManager <MKMapViewDelegate>
@end

@implementation RNTMapManager

RCT_EXPORT_MODULE()

RCT_EXPORT_VIEW_PROPERTY(zoomEnabled, BOOL)
RCT_EXPORT_VIEW_PROPERTY(onRegionChange, RCTBubblingEventBlock)

RCT_CUSTOM_VIEW_PROPERTY(region, MKCoordinateRegion, MKMapView)
{
    [view setRegion:json ? [RCTConvert MKCoordinateRegion:json] : defaultView.region animated:YES];
}

- (UIView *)view
{
  RNTMapView *map = [RNTMapView new];
  map.delegate = self;
  return map;
}

#pragma mark MKMapViewDelegate

- (void)mapView:(RNTMapView *)mapView regionDidChangeAnimated:(BOOL)animated
{
  if (!mapView.onRegionChange) {
    return;
  }

  MKCoordinateRegion region = mapView.region;
  mapView.onRegionChange(@{
    @"region": @{
      @"latitude": @(region.center.latitude),
      @"longitude": @(region.center.longitude),
      @"latitudeDelta": @(region.span.latitudeDelta),
      @"longitudeDelta": @(region.span.longitudeDelta),
    }
  });
}
@end
```

###### 在JS中调用事件处理

``` javascript
// MapView.js
class MapView extends React.Component {
  _onRegionChange = (event) => {
    if (!this.props.onRegionChange) {
      return;
    }

    // process raw event...
    this.props.onRegionChange(event.nativeEvent);
  }
  render() {
    return (
      <RNTMap
        {...this.props}
        onRegionChange={this._onRegionChange}
      />
    );
  }
}
MapView.propTypes = {
  /**
   * Callback that is called continuously when the user is dragging the map.
   */
  onRegionChange: PropTypes.func,
  ...
};

// MyApp.js
class MyApp extends React.Component {
  onRegionChange(event) {
    // Do stuff with event.region.latitude, etc.
  }

  render() {
    var region = {
      latitude: 37.48,
      longitude: -122.16,
      latitudeDelta: 0.1,
      longitudeDelta: 0.1,
    };
    return (
      <MapView
        region={region}
        zoomEnabled={false}
        onRegionChange={this.onRegionChange}
      />
    );
  }
}
```

##### 样式
所有的RN视图都是UIView的子类，共享一些样式属性。一些组件会有些默认属性，如UIDataPicker大小固定。
默认属性对布局算法能否正常执行很重要，又要允许重写默认属性。
DatePickerIOS通过将原生UI组件包装到具有灵活样式的额外视图中，并在内部原生组件上使用固定样式完成需求。

``` javascript
// DatePickerIOS.ios.js
import { UIManager } from 'react-native';
var RCTDatePickerIOSConsts = UIManager.RCTDatePicker.Constants;
...
  render: function() {
    return (
      <View style={this.props.style}>
        <RCTDatePickerIOS
          ref={DATEPICKER}
          style={styles.rkDatePickerIOS}
          ...
        />
      </View>
    );
  }
});

var styles = StyleSheet.create({
  rkDatePickerIOS: {
    height: RCTDatePickerIOSConsts.ComponentHeight,
    width: RCTDatePickerIOSConsts.ComponentWidth,
  },
});
```

默认属性是获取原生组件的实际帧导出的常量：

``` oc
// RCTDatePickerManager.m
- (NSDictionary *)constantsToExport
{
  UIDatePicker *dp = [[UIDatePicker alloc] init];
  [dp layoutIfNeeded];

  return @{
    @"ComponentHeight": @(CGRectGetHeight(dp.frame)),
    @"ComponentWidth": @(CGRectGetWidth(dp.frame)),
    @"DatePickerModes": @{
      @"time": @(UIDatePickerModeTime),
      @"date": @(UIDatePickerModeDate),
      @"datetime": @(UIDatePickerModeDateAndTime),
    }
  };
}
```

#### Android

### 链接库（IOS）
不是所有的App都会使用所有的原生功能，包含支持功能的代码会影响App的大小。因此RN使用独立的静态库提供功能特性。
RN提供的静态库目录：react-native/Libraries。
其中一些是纯JS库，只需require导入，一些库依赖一些原生代码，否则直接添加会抛出异常。

#### 链接含有原生代码的静态库步骤
##### 自动链接

1. 安装有原生依赖的库
2. 链接原生依赖

``` bash
//--save: 添加到package.json的dependencies块中
//--save-dev：添加到package.json的devDependencies块中
$ npm install {library-with-native-dependencies} --save
// 如果IOS项目使用CocoaPods且链接库有podspec文件，react-native link使用podfile链接库
$ react-native link
```

##### 手动链接
如果库包含原生代码，里面一定有后缀为.xcodeproj的文件。

1. 将.xcodeproj文件放到Xcode的Libraries中
2. 打开主项目文件，选择构建阶段，将刚刚放入Xcode的Libraries中的库的Products文件导入到【链接二进制库】
3. 打开主项目文件，选择构建设置，找到【头文件搜索路径】，添加库的头文件
	* 仅当需要在原生代码中使用库内容时，执行此步骤
	* 不推荐使用【递归】，特别在使用cocoapods时，可能引发构建失败

### 运行模拟器
#### iOS
``` bash
$ react-native run-ios
$ react-native run-ios --simulator="iPhone 4s"
$ xcrun simctl list devices -- 查看设备名称
```

### 原生代码与RN代码交互（IOS）
RN受React.js启发，所以信息流的基本思想是一致的。
React的流是单向的，RN维护组件层级，每个组件依赖父组件和自身内部状态，视为属性，数据按自顶向下习惯从父组件传递到子组件。当父组件依赖于后代的状态时，后代要传递回调给父组件，实现父组件更新。

#### 属性

##### 原生传递属性给RN
RCTRootView用于内嵌RN视图到原生组件。
RCTRootView初始化时允许传递任意属性到RN App中。
initialProperties参数是NSDictionary的实例，会自动转换为JSON对象传递到顶层JS组件中。
RCTRootView还提供了可读写的属性appProperties。当appProperties改变后，RN会根据新属性值重新渲染组件。设置属性只能在主线程，获取属性可以在任意线程。不能一次更新一些属性，建议封装到包装类中。

**注意**：新属性更改后，顶层RN组件的componentWillReceiveProps和 componentWillUpdateProps方法不会被调用，可以在componentWillMount中访问新属性。

##### RN传递属性给原生
原生组件通过调用RCT_CUSTOM_VIEW_PROPERTY公开组件属性，RN通过这些属性传递属性值。

##### 属性的局限性
现有回调机制允许JS触发指定原生行为，之后在JS中处理行为结果。
像JS动作触发RN视图从原生父视图移除行为，因为信息需要自顶向上，这是属性无法满足的。

* 不支持回调

#### 事件与原生模块
##### 原生调用RN方法（事件）
事件不保证执行时间，因为事件在另外的线程进行处理。
RCTViewManager作为视图的委托者，通过桥接器发送事件到JS。

* 事件可以随处发送，可能给项目引入意大利面条式（非结构化、难维护）的依赖
* 事件共享命名空间，命名冲突无法静态检测，难以调试
* 使用同一RN组件的多个实例时，要在事件中引入标识符进行区分

##### RN调用原生方法（原生模块）
JS桥接器为每个原生模块的创建一个实例。它们可以公开任意方法和常量到RN。
原生模块的单例性限制了上下文内嵌机制。
RN视图内嵌在原生视图中，通过原生模块机制，公开的方法中不仅要包含方法参数，还要包含原生视图的标识，该标识用于获取要更新的原生视图的引用。也就是说在原生模块中要持有标识符到原生视图的映射。
RCTUIManager使用这种方案管理RN视图。
原生模块也可以公开已存在的原生库给JS。

**注意**：所有的原生模块共享命名空间。

#### 布局计算流
集成原生和RN，需要一种方法整合两种不同的布局系统。

##### RN中内嵌的原生组件布局
所有的RN视图都是UIView的子类。

##### 原生中内嵌的RN组件布局
###### RN内容大小固定
当RN根视图大小固定，唯一要确保的就是RN的内容能包含在指定的大小内。
一般使用flexbox布局，不要使用相对布局。
通过rootView.frame可以动态更新根视图大小，内容布局由RN搞定。

###### RN内容大小不固定
不要让JS和原生尺寸都定义为适应性的。比如RCTRootView适配模式为RCTRootViewSizeFlexibilityHeight时，顶层JS组件布局的宽度不要是flex的。
RN布局计算在一个特别线程，而原生UI视图更新在主线程，这会造成原生和RN临时UI不一致问题。
RN在RCTRootView成为其他视图的子视图之前不执行布局计算，如果你想在RCTRootView的尺寸已知前隐藏根视图，使用UIView的hidden属性，在委托方法中可改变视图可见性。

* 使用ScrollView包裹RN视图
* 让RCTRootView的持有者决定适配模式，RN计算内容大小并传递给RCTRootView的委托

RCTRootView支持的4种适配模式：
* RCTRootViewSizeFlexibilityNone(default) -- 大小不因内容而改变
* RCTRootViewSizeFlexibilityWidth
* RCTRootViewSizeFlexibilityHeight
* RCTRootViewSizeFlexibilityWidthAndHeight

### App扩展（IOS）
App可见性可在主应用外提供自定义的功能和内容。

#### 扩展的内存使用
扩展组件在正常App沙箱外加载，极有可能多个App扩展同时加载，受到内存少限制。

##### 今日部件
内存大小限制为16MB。RN实现内部占用较高，容易超出内存限制。

##### 其他应用扩展
比今日部件内存稍大，如自定义键盘扩展为48MB，共享扩展为120MB。