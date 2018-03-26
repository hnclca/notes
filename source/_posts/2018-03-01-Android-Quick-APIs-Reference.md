---
title: Android Quick APIs Reference
comments: true
toc: true
date: 2018-03-01 14:21:47
tags:
	- android
	- quick reference
---
安卓平台的简单介绍。

<!-- more -->
### 平台
#### Linux内核
##### Binder
负责进程间通信。
AIDL封装了Binder通信细节。

##### Logger
##### Wake Locks
阻止应用进入睡眠模式。

##### Alarm Timer
从睡眠模式中唤醒设备，执行计划任务。

##### Low Memory Killer
应用载入内存很耗资源，为了实现应用间的快速切换，会缓存已启动的应用，启用应用过多时会导致内存不足。
该程序负责管理和回收内存，防止内存溢出。
当可用内存低于指定阈值时，会移除最不重要的应用。
应用的重要性取决于是否对用户可见。

##### File System
使用YAFFS2（Yet Another Flash File System）作为主文件系统。

* /boot -- 启动引导和Linux内核
* /system -- 系统文件和预装应用
* /recovery -- 恢复镜像
* /data -- 用户应用，运行时可读，受文件系统权限保护
* /cache -- 临时文件，频繁访问，设备重启后清空

#### Native Libraries
* SQLite -- 关系型SQL数据库
* WebKit -- HTML/CSS渲染和JS执行引擎
* OpenGL ES -- 高性能2D和3D渲染
* Open Core -- 多媒体框架
* OpenSSL -- SSL、TLS协议

#### 应用沙箱
##### Android Runtime(ART)
Android 5.0正式启用的JVM。支持多个DEX加载。
使用了AOT(ahead of time)技术在安装时将字节码转换为机器码。
Dalvik使用了JIT(just in time)在运行时将字节码转换为机器码的技术。

##### Compiled Android Applications
为兼容以前的Dalvik虚拟机，应用包内执行文件为DEX(Dalvik Executable)格式。
SDK提供了将Java字节码转换为DEX字节码的工具。
ART在应用安装过程中自动将DEX格式转换为OAT格式。

##### Application Sandbox
让每个应用在沙箱内运行，隔离应用数据和代码执行。

* 每个应用运行在专用的ART VM实例中
* 每个应用在安装时分配帐户，应用数据访问受文件系统权限保护
* 应用只能使用平台提供的通信接口跟系统和其他应用通信，这些接口受安卓权限保护

##### Zygote
安卓应用进行的母体。
系统启用时启动的核心进程之一。

* 一旦系统启动，Zygote启动新的ART VM实例，并初始化安卓核心服务，如Power, Telephony, Content和Package
* 由于创建ART VM实例和加载安卓框架组件很耗资源，Zygote通过复制自身解决这一问题，Zygote和刚复制的进程共享预加载的安卓框架资源

#### 应用框架
应用框架运行在ART VM顶部，为应用提供访问安卓平台和设备的接口。提供了如包管理器、电话管理器、位置管理器和通知管理器等服务。

##### Application
应用空间包含了所有面向用户的安卓应用，它们都运行在ART VM顶部。这些应用包括从安卓市场下载的第三方应用和系统应用，如启动器、联系人、电话和浏览器。

#### 安卓支持库
提供向后兼容的支持库，让低版本的平台使用高版本特性。
支持库不提供需要操作系统额外支持的特性。

### Development Enviroment
#### 安卓工具链
AOSP(Android Open Source Project)使用了已存在的开源开发工具集成构建全部开发环境，因而产生了全面的工具链。

##### SDK

* 平台API Java库
* 应用打包器
* 设备模拟器
* 字节码优化和混淆器
* 安卓设备调试桥
* 示例代码和教程
* 平台文档

##### NDK
安卓平台运行在Linux内核上，由Linux内核和BSD C库提供平台专用功能。
应用的性能关键部分需要使用C/C++或汇编一类语言生成的机器码。
除了提高性能以外，需要接入遗留或共享的代码，而不是重写一遍。
NDK是SDK的伴生工具，用于构建原生应用或无缝地嵌入原生代码到Java应用。

* 交叉编译器
* 调试器
* 平台头文件
* 相关文档

##### ADT
为Eclipse IDE平台提供的安卓开发插件，现在已经废弃。

##### Android Studio
基于Intellij Idea的自定义IDE，提供了超出ADT的额外的特性和改进，如构建变量和用户界面布局编辑器。

### Application Components
#### Activity
提供与用户交互的应用窗口。
负责创建窗口和布局用户界面组件。

##### 基类
* android.app.Activity

##### 清单注册

* manifest.application.activity
	* @android:name
	* @android:label
	* intent-filter
		* action
			* @android:name
		* category
			* @android:name
		* data
			* @android:mimeType

##### 生命周期
安卓平台的生命周期比桌面应用复杂许多，桌面应用的生命周期直接由用户控制，而安卓平台为了高效利用有限的系统资源管理着安卓组件的生命周期。
Activity经历拥有七种生命周期状态，在生命周期变化会通知Activity类中的生命周期勾子。重写生命周期方法时，一定要先调用父类方法。

* onCreate -- 首次创建，不可见，初始化实例和UI
* onStart -- 可见，不可交互
* onResume -- 在前台，可交互
* onPause -- 不在前台，可见，注意保存数据
* onStop -- 不可见
* onRestart -- 重新可见
* onDestroy -- 销毁

#### Intent

* action -- 必选
* data -- URI，必选
* type -- MIME类型，可选
* component -- 显式指定组件名，可选
* extras -- Bundle类型，使用键值对存储额外数据

##### Intent Resolution
安卓框架创建并发送Intent后，安卓平台要解析Intent，找到合适的处理组件。

* 显式意图 -- 指定了component
* 隐式意图 -- 未指定component

##### Intent Filter
组件可指定多个过滤器。
声明组件可处理的请求需要满足的条件。未指定过滤器的组件，只能显式调用。

##### Pending Intent
通常意图都是即时执行操作，有时也会用于注册回调。
常用于提供一个意图给其他应用，当条件满足或请求操作完成时，返回原应用。比如提供意图给通知栏，当用户点击通知时返回应用。
PendingIntent拥有的权限来自创建的应用，而不是执行的应用。

* getActivity
* getBroadcast
* getService
* getActivities

``` kotlin
val intent = Intent()
intent.action = Intent.ACTION_VIEW
intent.data = Uri.parse("content://contacts/people")
val pendingIntent = PendingIntent.getActivity(this, 0, intent, 0)
try {
	pendingIntent.send()
} catch (PendingIntent.CanceledException e) {
	e.printStackTrace()
}
```

#### Service
activity的寿命不足以长时间运行任务，比如下载大视频文件。
负责在后台完成长时间运行任务。
可供当前应用使用，也可暴露给其他应用使用。

##### 基类
* android.app.Service

##### 清单注册
* manifest.application.service
	* @android:name
	* @android:label
	* @android:exported
	* @android:permission

##### 自定义权限限制访问

1. 使用permission标签自定义权限
2. 使用android:permission指明Service需要的权限
3. 使用需要权限的Service时，清单中要使用uses-permission请求自定义权限

* manifest.permission
	* @android:name
	* @android:protectionLevel

##### 生命周期
由于运行在后台，不与用户直接交互，生命周期状态少于Activity。

* onCreate -- 首次创建
* onStartCommand -- 每次意图启动都被调用，可被多次调用
* onDestory -- 销毁，清除持有资源

##### 重启策略
用户不可见或不再交互的应用视为非重要应用，为了节省内存，安卓平台可能终止应用进程。Service运行在后台，因此有着随时被平台终止的可能。
应用启动策略取决于onStartCommand方法的返回值。

* START_FLAG_NOT_STICKY -- 平台杀掉后，收到显式调用重启
* START_FLAG_STICKY -- 平台杀掉后重启，无须保留初始意图
* START_FLAG_REDELIVERY -- 平台杀掉后重启，需要传递初始意图

##### 启动服务
服务与调用方在服务生命周期内没有关联。

###### 意图启动
``` kotlin
val intent = Intent(this, MyService.class)
startService(intent)
```

###### Intent Service
android.app.IntentService用来处理队列中的请求意图。

``` kotlin
class MyService : IntentService(MyService::class.java.simpleName) {
	override fun onHandleIntent(intent Intent) {
    	// handle intent
    }
}
```

##### 绑定服务
调用方与服务需要在服务生命周期内通信，服务与调用方生死相关。
Service的exported属性决定返回的服务类型。
Service中的onBind方法要根据服务类型返回合适的Binder服务通道。

``` kotlin
// Activity
bindService(Intent(this, MyService::class.java), object : ServiceConnection {
		override fun onServiceDisconnected(cmpName: ComponentName) {
			// service disconnected
		}

		override fun onServiceConnected(cmpName: ComponentName, iBinder: IBinder) {
        	// service connected
		}

	}, Context.BIND_AUTO_CREATE)
```

###### 本地服务
``` kotlin
// Service
internal inner class LocalBinder extends Binder {
    val service: Service
        get() = this@MyService
}

val localBinder = LocalBinder()

override onBind(intent Intent): IBinder {
	return localBinder
}

// Activity
override fun onServiceConnected(cmpName: ComponentName, iBinder: IBinder) {
    val localBinder =
        iBinder as MyService.LocalBinder
    val service = localBinder.service
}
```

###### 远程服务
远程调用服务时，调用方与服务属于不同的线程，通信依靠Binder。
可使用AIDL定义通信接口或使用简单消息队列传递基本类型数据。

1. AIDL
AIDL文件后缀名为.aidl，位于src/main/aidl/packagename/目录下。
编译过程中，AIDL接口自动生成实现类供调用方和服务方通过binder通信通道。
``` kotlin
// AIDL
interface IMyServiceInterface {
	fun add(a: Int, b: Int)
}
// Implementing the Stub Interface
class MyService : Service() {
    private val serviceBinder = object : IMyServiceInterface.Stub() {
        override fun add(a: Int, b: Int) = a + b
    }
    override fun onBind(intent: Intent?): IBinder {
        return serviceBinder
    }
}
// Activity
override fun onServiceConnected(cmpName: ComponentName, iBinder: IBinder) {
    myServiceInterface =
        IMyServiceInterface.Stub.asInterface(iBinder)
    try {
        val total = myServiceInterface.add(10, 20)
    } catch (e: RemoteException) {
        e.printStackTrace()
    }
}
```

2. 消息队列
当服务与调用方法通信需求较小时，AIDL则过于重量。
android.os.Messager为服务与调用方的提供简单消息队列。
消息队列可传递基本类型数据，不需自定义接口。
``` kotlin
// Service
companion object {
    const val MESSAGE_DOWNLOAD = 1
}
private val messenger = Messenger(RequestHandler())
override fun onBind(intent: Intent?): IBinder {
    return messenger.binder
}
class  RequestHandler : Handler() {
    override fun handleMessage(msg: Message) {
        when (msg.what) {
            MESSAGE_DOWNLOAD -> {
                // start downloading
            }
            else -> super.handleMessage(msg)
        }
    }
}
// Activity
override fun onServiceConnected(cmpName: ComponentName, iBinder: IBinder) {
	val service = Messenger(iBiner)
    val downloadMessage = Message.obtain(null, MESSAGE_DOWNLOAD)
    try {
    	service.send(downloadMessage)
    } catch (e: RemoteException) {
        e.printStackTrace()
    }
}
```

#### 系统服务
安卓平台以服务方式提供多种设备和平台特征。
安卓框架以APIs方式提供这些服务，无需通过意图、AIDL和绑定。
向getSystemService方法传递服务名称获取服务接口。
大多系统服务需要指定权限。

* android.app.ActivityManager
与全局的Activity状态交互

* android.os.PowerManager
控制设备电量配置

* android.app.AlarmManager
计划执行等待意图

* android.app.NotificationManager
通过在通知栏显示通知，让用户注意后台事件

* android.location.LocationManager
允许应用接收位置信息

* android.app.DownloadManager
允许应用请求长时间运行的HTTP下载，下载服务在后台处理下载，完成后提示系统。

* android.net.ConnectivityManager
查询设备的连接状态

* android.net.wifi.WifiManager
与WiFi网络交互

* android.app.UiModeManager
查询和操作设备的UI模式，如车载模式或正常模式

* android.view.inputmethod.InputMethodManager
控制输入方法

* android.app.KeyguardManager
锁定或解锁键盘屏幕

* android.app.SearchManager
访问平台搜索功能

* android.view.WindowManager
访问顶部窗口管理器，以自定义窗口

* android.view.LayoutInflater
使用给定的资源填充布局

* android.os.Vibrator
在静音模式下控制设备振动提示用户

#### Content Provider
负责暴露应用数据给其他应用，提供了数据封装性与安全性。

##### 基类
除onCreate的方法外，其他方法可能被不同线程调用多次，实现类应线程安全。

* android.content.ContentProvider
	* onCreate -- 实例化
	* getType -- MIME类型
	* query, insert, update, delete

UriMather定义Uri匹配规则。
规则格式：AUTHORITY, CONTENT_PATH(/#), QUERY_ALL/QUERY_BY_ID
``` java
content://com.example.booksprovider/books/1
content://com.example.booksprovider/books/#2
```


##### 约定
* AUTHORITY -- 最好包含包名，类似于网络域名
* CONTENT_PATH
* CONTENT_TYPE -- "vnd.android.cursor.dir/vnd.AUTHORITY.CONTENT_PATH"
* CONTENT_ITEM_TYPE -- "vnd.android.cursor.item/vnd.AUTHORITY.CONTENT_PATH"

##### 清单注册
* manifest.application.provider
	* @android:name
	* @android:authorities
	* @android:enabled
	* @android:exported

##### 安全性
* @android:readPermission
* @android:writePermission
* @android:Permission

##### 访问
``` java
getContentResolver()
```

##### 系统Content Provider

* Alarm Clock Provider
访问闹钟，操作现有闹钟

* Browser Provider
访问浏览器书签和历史记录

* Calendar Provider
访问并操作日程

* Contacts Provider
访问联系人

* Call Log Provider
访问接入和拨出电话

* Document Provider
访问用户文档，包括云文档

* Media Store Provider
访问设备上多媒体文件的元数据

* Setting Provider
访问系统级别设备选项

* Telephony Provider
访问电话数据，包括短信、采集和APN列表

* User Dictionary Provider
访问用户定义的单词，供输入法联想文本

* Voicemail Provider
访问用户语音邮件

#### 广播消息
提供系统范围的消息总线机制。该机制让应用和系统传播事件和状态改变信息给感兴趣的组件，通过广播视作意图的消息。

##### 发送广播消息
如果仅发送广播消息给应用内组件，应该使用LocalBroadcastManager的sendBroadcast，优化传递和确保消息安全性。

``` kotlin
val intent = Intent()
intent.action = "com.appress.bookreceiver.action.BOOK_NEW"
intent.addCategory(Intent.CATEGORY_DEFAULT)
intent.data = Uri.parse("http://www.apress.com/978143023879")
sendBroadcast(intent)
```

##### 接收广播消息
* android.content.BroadcastReceiver

``` kotlin
class BookBroadcastReceiver : BroadcastReceiver() {
	override fun onReceiver(context: Context, intent: Intent) {
    }
}
```

##### 清单注册
* manifest.application.receiver
	* @android:name
	* @android:enabled
	* @android:exported
	* intent-filter
		* action
		* category

##### 代码注册
``` kotlin
val receiver = BookBroadcastReceiver()
intentFilter = IntentFilter()
intentFilter.addAction("com.apress.bookreceiver.action.BOOK_NEW")
intentFilter.addCategory(Intent.CATEGORY_DEFAULT)
registerReceiver(receiver, intentFilter)
```

##### 生命周期
广播接收器仅存活于onReceiver方法执行期间。

##### 安全性
增加广播消息和广播接收器的权限来提高安全性。

#### Context
提供应用环境的全局信息的访问接口，访问应用资源，包括启动activities和services。上下文的寿命取决于类作为上下文的时间。像Activity一旦销毁，实例就不再提供上下文专用特性。
Activity和Service都继承片android.content.Context。

#### Application
因为Activity和Service的存活时间受限，因此不适合存储应用全局状态。
android.app.Application类存活在应用运行期间。
Application类本身并不提供任何信息存储机制。可自定义Application类存储全局状态数据。
跟Activity一样，Application类提供了生命周期勾子，可用于初始化和释放资源。

### Application Resources
解耦代码和资源有两点优势：1.应用本地化，2.设备专用体验。
安卓框架提供了全面的APIs和资源组织结构，让应用根据运行时特征无缝地选择合适的资源。

#### 资源结构
* src/main/res
	* drawable
	* layout
	* values
##### 资源分组
编译应用时，安卓工具链会自动生成R类，为代码提供访问途径。
所有资源可通过getResource获取。

|资源组|子文件夹|代码引用|资源引用|
|::||||
|属性动画|animator|R.animator|@animator/file|
|补间动画|anim|R.anim|@anim/file|
|颜色状态列表|color|R.color|@color/file|
|图片|drawable|R.drawable|@drawable/file|
|布局|layout|R.layout|@layout|
|菜单|menu|R.menu|@menu/file|
|原始资源|raw|R.raw||
|简单值|values|R.arrays||
|||R.bool|@bool/id|
|||R.color|@color/id|
|||R.dimen|@dimen/id|
|||R.integer|@integer/id|
|||R.plurals|@plurals/id|
|||R.string|@string/id|
|||R.style|@style/id|
|XML|xml|R.xml|@xml/file|

##### 属性动画
属性动画是在指定时间时间内改变对象属性的动画。

* set
	* objectAnimator
		* @android:propertyName
		* @android:duration
		* @android:valueFrom
		* @android:valueTo
		* @android:valueType

``` java
animatorSet = (AnimatorSet)AnimatorInflater.loadAnimator(this, R.animator.example);
```

##### 补间动画
应用一系列变换产生的动画。
* set
	* scale
		* @android:fromXscale
		* @android:toXscale
		* @android:duration
	* rotate
		* @android:fromDegree
		* @android:toDegree
		* @android:duration

``` java
animation = AnimationUtils.loadAnimation(this, R.anim.tween)
textView.startAnimation(animation)
```

##### 颜色状态列表
描述视图不同状态下的颜色。

* selector
	* item
		* @android:color
		* @android:state_pressed/focused/...

``` java
colorStateList = getResources().getColorStateList(R.color.button)
```

##### 图片
绘制到屏幕的图片，代码用Resources.getDrawable方法获取图片资源。
###### 位图
支持PNG、JPEG、GIF、Webp格式。

###### XML位图
为已存在的位图添加额外属性。指向已有位图资源，添加如抗锯齿、防抖动属性。

* bitmap
	* @android:src
	* @android:antialis

###### 9patch图片
可拉伸的位图。相比普通位图，多一个.9扩展名，如button.9.png。
9patch制作工具：SDK/tools/draw9patch.bat/sh

* 左边和上边标记允许拉伸的位图
* 右边和下边标记内容边界。

###### XML9patch图片
跟XML位图类似。为已存在的9patch图片提供额外参数。

* nine-patch
	* @android:src
	* @android:dither

###### 形状
XML定义的通用形状。支持所有基本类型：矩形、椭圆、线和圆，可添加圆角、渐变和颜色等。

* shape
	* @android:shape
	* corners
		* @android:radius
	* gradient
		* @android:startColor
	* padding
		* @android:top

###### 状态列表
对应对象不同状态的一组图片。

* selector
	* item
		* @android:drawable
		* @android:state_pressed/focused/...

##### 布局资源
定义UI的架构。是包含视图组件和位置属性的列表。

##### 菜单资源
定义应用菜单结构，如选项菜单、上下文菜单和子菜单。

* menu
	* item
		* @android:id
		* @android:title
		* @android:orderInCategory
		* @android:showAsAction

``` java
getMenuInflater().inflate(R.menu.my, menu)
```

##### 原始资源
不属于任何资源分组的资源。该文件夹下资源可以是任意类型。

``` java
inputStream = getResource().openRawResource(R.raw.attributions)
```

##### 值资源
值资源都是简单值，比如字符串、整数、逻辑值和颜色。
一个值文件可包含多种值资源。
每个值资源拥有自己的id，可根据需要灵活地组织。如将所有的颜色至于colors.xml或者按activity分类创建独立的资源文件。

* resources
	* string
		* @name
	* string-array
		* @name
		* item
	* plurals  -- 字符串
		* @name
		* item
			* quantity -- zero/one/two/few/many/other
	* bool
	* color
		* @name -- #RGB/#ARGB/#RRGGBB/#AARRGGBB
	* dimen
		* @name -- dp/sp/pt/px/mm/in
	* integer
	* integer-array
		* @name
		* item
	* array
		* @name
		* item -- color，不仅限于字符串和整数，无需元素类型相同。

``` java
TypedArray colors = getResources().obtainTypedArray(R.array.colors);
background = colors.getColor(0, Color.BLACK);
```

##### XML资源
任意的XML格式资源。
与原始资源不同，安卓工具链在编译时对XML资源进行预解析。

``` java
XmlResourceParser parser = getResources().getXml(R.xml.configuration);
try {
	for (int eventType = parser.getEventType();
    			eventType != XmlPullParser.END_DOCUMENT;
                eventType = parser.next()) {
        switch (eventType) {
        	case XmlPullParse.START_TAG:
            	String tagName = parser.getName();
                break;
        	case XmlPullParse.TEXT:
            	String text = parser.getText();
                break;
        	case XmlPullParse.END_TAG:
            	String tagName = parser.getName();
                break;
        }
    }
} catch (XmlPullParserException e) {
	e.printStackTrace();
} catch (IOException e) {
	e.printStackTrace();
}
```

#### 默认和备选资源
为不同的设备配置提供备选资源。
备选资源与默认资源必须有相同的名称或ID。
为了在不同配置上使用同一资源，可以使用资源别名。

#### 处理运行时变化
安卓框架在检测到设备配置变化时会重启activity，如设备方向变化、语言变化等。
可通过配置android:configChanges属性阻止重启，而是调用Activity.onConfiguationChanged方法，可重写该方法处理设备配置变化。

* manifest.application.activity
	* @android:configChanges

#### 资产
默认路径：src/main/assets
与资源相比，安卓工具链不会处理资产文件夹，因此应用内保持原有的目录结构和文件名。
资产文件夹中的资源不存在唯一资源ID，向android.content.res.AssetManager对象传入文件名进行访问。

``` java
InputStream inputStream = getAssets().open("file.dat");
```

##### Webview使用资产
Webivew访问静态资源。

``` java
webView.loadUrl("file:///android_assets/index.html")
```

#### Apk扩展文件
由于谷歌商店限制应用包大小不超过50M，而像游戏一类的应用，资源比较大，为了解决这一问题，允许添加两个Apk扩展文件，每个大小2GB。一个为主扩展文件，另一个为补丁扩展文件。
扩展文件在安装应用时下载，一般放在外存中，对文件格式不做限制。
路径：storage/Android/obb/packagename。

### Layouts and Views
#### 布局
每个layout都是ViewGroup类的子类型，包含独立视图和其他视图组。
每个布局都要提供位置逻辑，并绘制元素。

##### 定义布局
* XML资源（推荐）
* 代码

##### 布局需求
必须指定视图和视图组的尺寸属性，宽高。
除了具体尺寸数值外，有两个常量值

* matchParent -- 跟父视图一样大
* wrapContent -- 刚好能包括其内容

##### 通用布局
* LinearLayout
	* @android:orientation -- 默认水平方向
	* child view
		* @android:layout_weight -- 空间分配，默认平均分配，值为0
		* @android:layout_gravity

* RelativeLayout
	* child view
		* @android:layout_centerInParent/.. -- 相对父视图
		* @android:layout_toEndOf/... -- 相对锚点视图
		* @android:layout_alignWithParentIfMissing -- 锚点不可见

##### 动态布局
###### Adapter
将数据转换为视图。

* ArrayAdapter -- 数组数据
* SimpleCursorAdapter -- 数据库数据
* CustomAdapter -- 继承BaseAdapter
	* getCount
	* getItem
	* getItemId -- 返回指定位置的行ID，如果没有，默认返回位置
	* getView

数据变动更新

* notifyDataSetChanged -- 通知更新
* setNotifyOnChange

###### AdapterView
持有Adapter实例。

* ListView
* GridView
	* @android:stretchMode
	* @android:numColumns
	* @android:columnWidth
	* @android:horizontalSpacing/..
	* @android:gravity
* Spinner
	* @android:spinnerMode
	* @android:dropDownHorizontalOffset/..
	* @android:gravity
	* @android:dropDownSelector(dropdown)
	* @android:dropDownWidth(dropdown)
	* @android:popupBackground(dropdown)
	* @android:prompt(dialog)

项目选择事件

* setOnItemSelectedListener

#### 视图

##### 输出
* TextView
* ImageView
* ProgressBar
* Space

##### 输入
* EditText
* Button
* ImageButton
* ToggleButton
* Switch
* CheckBox
	* isChecked
	* OnCheckedChangeListener
* RadioButton/RadioGroup
	* OnCheckedChangeListener

#### Fragment
UI模块化和复用性。

##### 基类
android.app.Fragment

##### 生命周期

* onAttach -- 绑定activity
* onCreate -- 首次创建，activity可能未完全创建
* onCreateView -- 实例化并返回UI实例
* onActivityCreated -- activity实例化且视图层次实例化
* onStart -- 可见
* onResume -- 可见可交互
* onPause -- 不可见
* onDestroyView -- 视图解绑
* onDestroy
* onDetach -- activity解绑

##### 添加到activity

###### 布局添加
* fragment
	* @android:name
###### 代码添加
* FrameLayout
	* @android:id="@+id/fragment_container"

* Fragment
	* set/getArguments
	* getActivity

* FragmentManager
	* beginTransaction
	* findFragmentById

* FragmentTransaction
	* add
	* replace
	* commit
	* addBackStack
	* popBackStack



``` java
fragmentManager = getFragmentManager()
fragmentTransaction = fragmentManager.beginTransaction()
fragmentTransaction.add(R.id.fragment_container, fragment)
fragmentTransaction.commit()
```

### User Interface
#### ActionBar

``` java
// add
actionBar  = getActionBar();

// remove
actionBar.hide();
```

##### 菜单

* menu
	* item
		* @android:id
		* @android:title
		* @android:icon
		* @android:showAsAction
			* = ifRoom(recommand
			* = never
			* = withText
			* = always
			* = collapseActionView
		* @android:onClick

``` java
getMenuInflater().inflate(R.menu_actions, menu);
// selection
boolean consumed = true;
switch (item.getItemId()) {
	...
    default:
    	consumed = super.onOptionsItemSelected(item);
}
return consumed;
```

##### 动作视图
提供快速访问的视图，直接出现在动作栏而不只是动作图标。如SearchView。

* menu
	* item
		* @android:actionViewClass
		* @android:showAsAction
			* = collapseActionView

``` java
getMenuInflater().inflate(R.menu_actions, menu);
searchMenuItem = menu.findItem(R.id.action_search);
searchView = (SearchView)searchMenuItem.getActionView();
```

##### 动作提供者
使用自定义布局替换动作按钮，管理动作行为。如ShareActionProvider。

* menu
	* item
		* @android:actionProviderClass


``` java
getMenuInflater().inflate(R.menu_actions, menu);
menuItem = menu.findItem(R.id.action_share);
shareActionProvider = (ShareActionProvider)menuItem.getActionProvider();
intent = new Intent(Intent.ACTION_SEND);
intent.setType("image/*");
shareActionProvider.setShareIntent(intent);
```

#### Toast
仅用于前台消息，后台消息请使用通知。

* android.widget.Toast
	* makeText()
	* show()
	* LENGTH_SHORT
	* LENGTH_LONG

#### 对话框

##### 基类
* android.app.Dialog

##### 对话框分类

* android.app.AlertDialog.Builder
	* setTitle()
	* setMessage()
	* create()
	* setPositiveButton()
	* setNegativeButton()
	* setNeutralButton()
	* setItems()
	* setMultiChoiceItems()
	* setSingleChoiceItems()
	* setView()

* android.app.DatePickerDialog
* android.app.TimePickerDialog
* android.app.ProgressDialog
	* setProgressStyle()
	* setTitle()
	* setMessage()
	* setMax()
	* incrementProgressBy()
* android.app.DialogFragment -- dialog.show方法不处理生命周期事件
	* show() -- 结合FragmentManager进行管理。

#### 通知
后台消息工具，出现在通知栏。

``` java
notificationManager = (NotificationManager)getSystemService(Context.NOTIFICATION_SERVICE);
notification = new Notification.Builder(this)
		.setContentTitle()
        .setContentText()
        .setSmallIcon()
        .setContentIntent(pendingIntent)
        .build()
notificationManager.notify(1, notification);
```

##### 后退栈
由于通知允许用户点击通知访问深层activity，这会导致深层activity启动时无后退栈。如消息通知，点击后直接打开消息详情，后退时不会返回到消息列表。

* TaskStackBuilder
``` java
intent = new Intent(this, ToastActivity.class);
taskStackBuilder = TaskStackBuilder.create(this);
taskStackBuilder.addParentStack(MessageActivity.class);
taskStackBuilder.addNextIntent(intent);
pendingIntent = taskStackBuilder.getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);
```

* AndroidManifest.xml
``` xml
<activity android:name=".InboxActivity" />
<activity android:name=".MessageActivity"
	android:parentActivityName=".InboxActivity" />
```

##### 动作按钮

* android.app.NotificationBuilder
	* addAction()

##### 更新
拥有唯一通知标识符。

* android.app.Notification.InboxStyle
	* addLine()
	* setSummaryText()
* android.app.Notification.Builder
	* setStyle()

##### 取消

* android.app.NotificationManager
	* cancel()
	* cancelAll()

### Storing Data
#### 简单文件
##### 内存
私有数据，应用卸载时移除。

* android.content.Context
	* openFileOutput
	* openFileInput
	* getCacheDir

##### 外存

###### 权限

* android.permission.WRITE_EXTERNAL_STORAGE
* android.permission.READ_EXTERNAL_STORAGE

###### 检查外存是否可用
外存是可移除的，访问外存关需要检查有效性。

* android.os.Environment
	* getExternalStorageState()
	* MEDIA_UNKOWN -- 未知存储状态
	* MEDIA_REMOVED -- 拔出
	* MEDIA_UNMOUNTED -- 未安装
	* MEDIA_CHECKING -- 检查运行中
	* MEDIA_NOFS -- 文件系统未知
	* MEDIA_MOUNTED
	* MEDIA_READ_ONLY -- 以只读模式加载
	* MEDIA_SHARED -- 通过USB添加到电脑
	* MEDIA_BAD_REMOVAL -- 错误移除，需要检查
	* MEDIA_UNMOUNTABLE -- 媒体错误

###### 访问外存

* android.os.Environment
	* getExternalStorageDirectory() -- 不推荐使用顶层目录
	* getExternalStoragePublicDirectory() -- 不推荐使用顶层目录
	* DIRECTORY_ALARMS
	* DIRECTORY_DCIM
	* DIRECTORY_DOCUMENTS
	* DIRECTORY_DOWNLOADS
	* DIRECTORY_MOVES
	* DIRECTORY_MUSIC
	* DIRECTORY_NOTIFICATIONS
	* DIRECTORY_PICTURES
	* DIRECTORY_PODCASTS
	* DIRECTORY_RINGTONES
* android.content.Context
	* getExternalFilesDir() -- 传入null时返回顶层目录
	* getExternalCacheDir()

##### JSON
Javascript Object Notation。

* android.util.JsonWriter
	* setIndent()
	* beginObject()
	* name()
	* value()
	* endObject()
	* close()
* android.util.JsonReader
	* beginObject()
	* hasNext()
	* nextName()
	* nextString()
	* skipValue()
	* endObject()
	* close()

#### 共享配置
##### 访问
###### Activity共享
* android.app.Activity.getPreferences()

###### 上下文默认共享
* android.preference.PreferenceManager
	* getDefaultSharedPreferences()

###### 通用共享
* android.content.Context
	* getSharedPreferences()

#### 编辑、获取和监听
* android.content.SharedPreferences
	* edit()
	* (un)registerOnSharedPreferenceChangeListener()
* SharedPreferences.Editor
	* put/getString()
	* put/getBoolean()
	* commit()
* SharedPreferences.OnSharedPreferenceChangeListener

##### 配置界面

* android.preference.PreferenceFragment
	* addPreferencesFromResource()
* PreferenceScreen
	* PreferenceCategory
		* @android:title
		* EditTextPreference/...
			* @android:key
			* @android:title
			* @android:summary

#### 关系数据库
* android.database.sqlite.SQLiteOpenHelper
	* onCreate()
	* onUpgrade()
	* onDowngrade() -- 默认抛出异常
	* getWritableDatabase()
	* getReadableDatabase()
* android.database.sqlite.SQLiteDatabase
	* execSQL()
	* insert/update/query/delete()
* android.content.ContentValues
	* put()
* android.database.Cursor
	* moveToFirst()
	* getString()
	* moveToNext()

#### 安卓备份服务
备份用户下载应用和系统配置列表到云端。升级到新安卓设备时，能无缝地恢复应用和系统配置。

##### 注册
注册地址：[Register for Android Backup Service](https://developer.android.google.cn/google/backup/signup.html)

##### 清单配置
* manifest.application
	* @android:backupAgent
	* meta-data
		* @android:name="com.google.android.backup.api_key"
		* @android:value="AED2xe..."


##### 备份
不会自动备份和恢复应用数据目录。需要应用提供备份代理实现处理平台专用备份和恢复操作。
备份的文件要注意同步，因为应用和备份服务会同时操作文件。

* android.app.backup.BackupAgent
* android.app.backup.BackupAgentHelper
	* addHelper()
* android.app.backup.FileBackupHelper
* android.app.backup.SharedPreferenceBackupHelper
* android.app.backup.BackupManager
	* dataChanged()

##### 测试
``` bash
$ adb shell bmgr backup com.example.backupandroid -- 请求备份
$ adb shell bmgr run -- 强制备份
$ adb uninstall com.example.backupandroid
$ adb install com.example.backupandroid
```

### Sensors and Location
#### APIs
* android.hardware.SensorManager
	* getDefaultSensor()
	* getSensorList()
	* (un)registerListenere()
	* requestTriggerSensor()
	* cancelTriggerSensor()
	* SENSOR_DELAY_NORMAL
	* SENSOR_DELAY_UI
	* SENSOR_DELAY_GAME
	* SENSOR_DELAY_FASTEST
* android.hardware.Sensor
	* getResolution()
	* getReportingMode()
	* TYPE_ACCELEROMETER  (x, y, z)(m/s^2)
	* TYPE_AMBIENT_TEMPERATURE  (℃)
	* TYPE_GAME_ROTATION_VECTOR  (x, y, z)
	* TYPE_GEOMAGNETIC_ROTATION_VECTOR (x, y, z)
	* TYPE_GRAVITY  (x, y, z)(m/s^2)
	* TYPE_GYROSCOPE  (x, y, z)(rad/s)
	* TYPE_GYROSCOPE_UNCALIBRATED  (x, y, z)(rad/s)
	* TYPE_HEART_RATE  (bpm)
	* TYPE_LIGHT  (lx)
	* TYPE_LINEAR_ACCELERATION (x, y, z)(m/s^2)
	* TYPE_MAGNETIC_FIELD (x, y, z)(μT)
	* TYPE_MAGNETIC_FIELD_UNCALIBRATED (x, y, z)(μT)
	* TYPE_PRESSURE  (hPa/mbar)
	* TYPE_PROXIMITY  (cm)
	* TYPE_RELATIVE_HUMIDITY  (%)
	* TYPE_ROTATION_VECTOR  (x, y, z, w)
	* TYPE_SIGNIFICANT_MOTION  N/A
	* TYPE_STEP_DETECTOR  (x)
	* REPORTING_MODE_CONTINUOUS
	* REPORTING_MODE_ON_CHANGE
	* REPORTING_MODE_ONE_SHOT
	* REPORTING_MODE_SPECIAL_TRIGGER
* android.hardware.SensorEventListener
	* onAccuracyChanged()
	* onSensorChanged()
* android.hardware.TriggerEventListener
	* onTrigger()
* android.hardware.SensorEvent
	* accuracy
	* sensor
	* timestamp
	* values

#### 位置
##### 权限
* android.permission.ACCESS_COARSE_LOCATION  -- 基站和WiFi
* android.permission.ACCESS_FINE_LOCATION  -- GPS，网络位置和WiFi综合

##### APIs
使用广播接收器监听位置提供者状态变化。
有两种监听方式：
1. 持续监听位置变化
2. 到过给定地理位置附近时接收接近提醒。

* android.location.LocationManager
	* isProviderEnabled() -- 检查指定位置提供者是否可用
	* requestLocationUpdates/removeUpdates()
	* requestSingleUpdate()
	* add/removeProximityAlert()
	* getLastKnownLocation()
	* GPS_PROVIDER
	* NETWORK_PROVIDER
	* PASSIVE_PROVIDER
	* PROVIDERS_CHANGED_ACTION

* android.location.LocationListener
	* onLocationChanged
	* onStatusChanged
	* onProviderEnabled
	* onProviderDisabled

* android.location.Location
	* HorizontalAccuracyMeters/..
	* Altitude/Latitude
	* Bearing -- 方向角度
	* Provider
	* Time

### Media and Camera
#### 音频管理器

* android.media.AudioManager
	* isMicrophoneMute()
	* setMicrophoneMute()
	* isSpeakerphoneOn()
	* setSpeakerphoneOn()
	* get/setStreamVolume()
	* getStreamMaxVolume()
	* setStreamMute()
	* setStreamSolo()
	* isMusicActive()
	* STREAM_ALARM
	* STREAM_DTMF
	* STREAM_MUSIC
	* STREAM_NOTIFICATION
	* STREAM_RING
	* STREAM_SYSTEM
	* STREAM_VOICE_CALL
	* USE_DEFUALT_STREAM_TYPE
	* FLAG_ALLOW_RINGER_MODES
	* FLAG_PLAY_SOUND
	* FLAG_REMOVE_SOUND_AND_VIBRATE
	* FLAG_SHOW_UI
	* FLAG_VIBRATE

#### 播放音频
* android.media.MediaPlayer
	* setDataSource()
	* setAudioStreamType()
	* prepare() -- 密集访问IO，非UI线程调用。
	* prepareAsync()
	* setOnPreparedListener()
	* start/stop/release()
* android.media.AsyncPlayer  -- 网络或SD卡，一次播放一个。
	* play()
* android.media.SoundPool  -- 音频集合，适用于音效
	* load/play/stop/uload/release()
* android.media.SoundPool.Builder --- API 21新增
	* setMaxStreams()
	* setAudioAttributes()
	* build()
* android.media.AudioAttributes
* android.media.AudioAttributes.Builder
	* setContentType()
	* setUsage()
	* build()

#### 录制音频
* android.media.MediaRecorder
	* setAudioSource()
	* setAudioEncoder()
	* setOutputFormat()
	* setOutputFile()
	* prepare()
	* start()
	* stop()
	* release()
* android.media.MediaRecorder.AudioSource
	* CAMCORDER
	* DEFAULT
	* MIC
	* REMOTE_SUBMIX
	* VOICE_CALL
	* VOICE_COMMUNICATION
	* VOICE_DOWNLINK
	* VOICE_RECOGNITION
	* VOICE_UPLINK

* android.media.MediaRecorder.AudioEncoder
	* AMR_NB

* android.media.MediaRecorder.AudioEncoder
	* THREE_GPP

* android.permission.RECORD_AUDIO

#### 播放视频

* android.view.SurfaceView
	* getHolder()
* android.view.Surface
* android.view.SurfaceHolder
	* getSurface()
* android.view.SurfaceHolder.Callback
	* surfaceCreated()
	* surfaceChanged()
	* surfaceDestoryed()
* android.media.MediaPlayer
	* setSurface()
	* setScreenOnWhilePlaying()
	* setDataSource()
	* setOnPreparedListener()
	* prepareAsync()
	* release()
* android.media.MediaPlayer.OnPreparedListener
	* onPrepared()

#### 录制音频

* android.media.MediaRecorder
	* setVideoSource()
	* setVideoEncoder()
* android.media.MediaRecorder.VideoSource
	* CAMERA
* android.media.MediaRecorder.VideoEncoder
	* MPEG_4_SP

#### 摄像机

* android.permission.CAMERA
* android.hardware.camera2.CameraManager
	* getCameraIdList()
	* getCameraCharacteristics()
	* openCamera()
* android.hardware.camera2.CameraCharacteristics
	* get()
	* LENS_FACING
	* LENS_FACING_FRONT
	* LENS_FACING_BACK
	* SCALER_STREAM_CONFIGURATION_MAP
* android.hardware.camera2.params.StreamConfigurationMap
	* getOutputSizes()
* android.hardware.camera2.CameraDevice
	* createCaptureRequest()
	* createCaptureSession()
	* TEMPLATE_MANUAL
	* TEMPLATE_PREVIEW
	* TEMPLATE_RECORD
	* TEMPLATE_STILL_CAPTURE
	* TEMPLATE_VIDEO_SNAPSHOT
	* TEMPLATE_ZERO_SHUTTER_LAG
* android.hardware.camera2.CameraDevice.StateCallback
	* onOpened()
	* onError()
	* onDisconnect()
	* onClosed()
* android.hardware.camera2.CameraCaptureSession
	* capture()/..
	* setRepeatingRequest()/..
	* stopRepeating()
* android.hardware.camera2.CameraCaptureSession.StateCallback
	* onConfigured()
	* onConfigureFailed()
* android.hardware.camera2.CameraCaptureSession.CaptureCallback
	* onConfigureCompleted()
* android.view.SurfaceHolder
	* setFixedSize()
* android.hardware.camera2.CaptureRequest
	* COLOR_CORRECTION_MODE
	* CONTROL_AE_MODE/..
	* FLASH_MODE
	* JPEG_QUALITY
	* SCALER_CROP_REGION
	* SENSOR_SENSITIVITY

* android.hardware.camera2.CaptureRequest.Builder
	* addTarget()
	* set()
	* build()