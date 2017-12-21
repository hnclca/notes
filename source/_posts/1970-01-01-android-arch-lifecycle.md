---
title: android.arch.lifecycle
comments: false
toc: true
date: 1970-01-01 00:00:00
tags:
	- android
	- references
---

Lifecycle -- 生命周期感知组件(common)
LiveData -- 数据持有组件(extension)
ViewModel -- 数据创建和管理组件(extension)

<!-- more -->
#### Annotations
##### OnLifecycleEvent
**摘要**：
标记生命周期事件回调监听方法。

**库名**：
android.arch.lifecycle:common:1.0.3

**公有方法**：
``` java
Lifecycle.Event value()
```
**示例代码**：
``` java
class TestObserver implements LifecycleObserver {
   @OnLifecycleEvent(ON_STOP)
   void onStopped() {}
}
```

#### Interfaces

##### LifecycleObserver
**摘要**：
生命周期观察者接口。没有任何方法，依赖 OnLifecycleEvent 注解方法。
**库名**：
android.arch.lifecycle:common:1.0.3

**直接子类**：
*	DefaultLifecycleObserver

##### DefaultLifecycleObserver
**摘要**：
回调监听LifecycleOwner状态变化。使用java8编版本时，该接口优于注解 OnLifecycleEvent。
**库名**：
android.arch.lifecycle:common-java8:1.0.0

**继承关系**：
*	LifecycleObserver

**公有方法**：
``` java
default void onCreate(LifecycleOwner owner)
default void onDestroy(LifecycleOwner owner)
default void onPause(LifecycleOwner owner)
default void onResume(LifecycleOwner owner)
default void onStart(LifecycleOwner owner)
default void onStop(LifecycleOwner owner)
```

##### LifecycleOwner
**摘要**：
生命周期被观察者接口。拥有一个Lifecycle，供自定义组件处理生命周期事件，而不用在Activity或Fragment中编写实现代码。
**库名**：
android.arch.lifecycle:commmon:1.0.3
**直接子类**：
*	LifecycleActivity(Deprecated)
*	LifecycleFragment(Deprecated)
*	LifecycleRegistryOwner(interface)(Deprecated)
*	LifecycleService
*	ProcessLifecycleOwner

**公有方法**：
``` java
Lifecycle getLifecycle()
```

##### LifecycleRegistryOwner(Deprecated)
**摘要**：
android.support.v7.app.AppCompatActivity(26.1.0)已继承LifecycleOwner接口。
实际由非公开SupportActivity类直接继承。

##### Observer&lt;T&gt;
**摘要**：
监听LiveData的简单回调。
**库名**：
android.arch.lifecycle:extensions:1.0.0
**公有方法**：
``` java
void onChanged (T t)
```

##### ViewModelProvider.Factory
**摘要**：
实例化ViewModels的工厂接口。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**直接子类**：
*	ViewModelProvider.NewInstanceFactory
*	ViewModelProviders.DefaultFactory

**公有方法**：
``` java
T create (Class<T> viewmodelClass)
```

##### ViewModelStoreOwner
**摘要**：
拥有ViewModelStore的范围(activity或fragment)，在配置发生改变时持有ViewModelStore，在范围销毁时，清除ViewModelStore。
**库名**：
android.arch.lifecycle:extensions:1.0.0

**直接子类**：
*	ViewModelProvider.NewInstanceFactory
*	ViewModelProviders.DefaultFactory

**公有方法**：
``` java
T create (Class<T> viewmodelClass)
```


#### Classes
##### Lifecycle
**摘要**：
定义拥有生命周期的对象。
**库名**：
android.arch.lifecycle:common:1.0.3
**直接子类**：
*	LifecycleRegistry

**公有方法**：
添加/移除观察者，获取当前的生命周期状态。
``` java
abstract void	addObserver(LifecycleObserver observer)
abstract Lifecycle.State	getCurrentState()
abstract void	removeObserver(LifecycleObserver observer)
```

##### LifecycleRegistry
**摘要**：
可以处理多个观察者的Lifecycle实现。被Fragments和Support Activities(26.1.0)使用，也可自定义使用。
**库名**：
android.arch.lifecycle:runtime:1.0.3
**继承关系**：
*	Lifecycle

**构造器**：
``` java
LifecycleRegistry(LifecycleOwner provider)
```
**公有方法**：
``` java
int	getObserverCount()
void	handleLifecycleEvent(Lifecycle.Event event)
void	markState(Lifecycle.State state)
```

##### LifecycleActivity
**摘要**：
android.support.v7.app.AppCompatActivity(26.1.0)已继承LifecycleOwner接口。
实际由非公开SupportActivity类直接继承。

##### LifecycleFragment
**摘要**：
Fragment(26.1.0)已继承LifecycleOwner接口。

##### LifecycleService
**摘要**：
生命周期感知型Service

**库名**：
android.arch.lifecycle:extensions:1.0.0

**继承关系**：
*	android.app.Service
*	LifecycleOwner

**构造器**：
``` java
LifecycleService()
```

##### ServiceLifecycleDispatcher
**摘要**：
Service分发生命周期事件的辅助类，仅当LifecycleService不可用时使用。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**构造器**：
``` java
// 通常是服务自身。
ServiceLifecycleDispatcher(LifecycleOwner provider)
```

**公有方法**：
``` java
Lifecycle	getLifecycle()

// 在super.onBind调用前调用。
void	onServicePreSuperOnBind()

// 在super.onCreate调用前调用。
void	onServicePreSuperOnCreate()

// 在super.OnDestroy调用前调用。
void	onServicePreSuperOnDestroy()

// 在super.onStart或onStartCommand调用前调用。
void	onServicePreSuperOnStart()
```

##### ProcessLifecycleOwner
**摘要**：
整个应用进程生命周期提供者。适用于app进入前台或退回后台事件。
可视为所有Activities组合的生命周期。
*	Lifecycle.Event.ON_CREATE 只发送一次
*	Lifecycle.Event.ON_DESTROY 永远不发送
*	Lifecycle.Event.ON_START, ON_RESUME在第一个activity发送之后发送
*	Lifecycle.Event.ON_PAUSE, ON_STOP在最后一个activity发送之后延迟发送
	*	延迟足够长，以保证因为配置改变activities摧毁重建过程中不会发送任何消息。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**继承关系**：
LifecycleOwner

**公有方法**：
``` java
static LifecycleOwner	get()
```

##### LiveData&lt;T&gt;
**摘要**：
用于指定生命周期的数据持有类。生命周期观察者与生命周期拥有者成对存在，在生命周期存活期内（STARTED/RESUMED）数据的改变会被通知给观察者。生命周期走向DESTROYED状态时，观察者会自动移除，避免产生内存泄漏问题。
当观察者由0变1和由1变0时，LiveData的激活和失活方法会得到通知，用来获取和释放资源文件。
主要用于持有ViewHolder的独立数据，也可用于应用内

不同模块间的共享数据。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**直接子类**：
*	MutableLiveData&lt;T&gt;

**构造器**：
``` java
LiveData()
```

**公有方法**：
``` java
T	getValue()

boolean	hasObservers()

// 是否存在激活的观察者
boolean	hasActiveObservers()

void	observe(LifecycleOwner owner, Observer<T> observer)

void	removeObserver(Observer<T> observer)

void	removeObservers(LifecycleOwner owner)

// 添加给内置的一直激活的LifecycleOwner，以接收所有事件。
void	observeForever(Observer<T> observer)
```

**可继承方法**：
``` java
// 激活、失活方法。
void	onActive()
void	onInactive()

// 推送设置指定值的任务到主线程。
void	postValue(T value)
void	setValue(T value)
```

##### MutableLiveData&lt;T&gt;
**摘要**：
公开了setValue(T)和postValue(T)方法。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**继承关系**：
LiveData&lt;T&gt;

**直接子类**：
*	MediatorLiveData&lt;T&gt;

**构造器**：
``` java
MutableLiveData()
```

**公有方法**：
``` java
void	postValue(T value)
void	setValue(T value)
```

##### MediatorLiveData&lt;T&gt;
**摘要**：
中介人LiveData，观察多个LiveData，并响应它们的变化事件。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**继承关系**：
*	MutableLiveData&lt;T&gt;

**构造器**：
``` java
MediatorLiveData()
```

**公有方法**：
``` java
<S> void	addSource(LiveData<S> source, Observer<S> onChanged)
<S> void	removeSource(LiveData<S> toRemote)
```

##### LiveDataReactiveStreams
**摘要**：
将LiveData的输入输出转换为响应流格式。

**库名**：
android.arch.lifecycle:reactivestreams:1.0.0

**公有方法**：
``` java
/** 依据响应流发布者创建一个可观察的LiveData流。当LiveData被激活时，它接收订阅发布者的消息，失活时，
* 订阅取消，LiveData持有发布者发布的最后一个数据。当添加新的观察者时，它会自动收到LiveData持有数据
* 的通知消息，但可能不是发布者发布的最后一个数据。
* LiveData并不处理错误，错误将视为它持有数据的状态，如果接收到发布者的错误，错误将
* 被传递到主线程，从而引发应用崩溃。
*/
static <T> LiveData<T>	fromPublisher(Publisher<T> publisher)

/**
* 将指定的LiveData流转换为响应流发布者。
* 使用良好发布者实现的流，大多数的消费者能让库以背压方式处理，需手动调用request(long)。
* 当发布者被订阅时，观察者会添加给指定LiveData。可订阅对象调用request时，观察者将连接到数据流。
* 调用request(Long.MAX_VALUE)相当于创建一个无背压的无界流。如果有限请求次数达到0时，
* 观察者会获取到最后一个项目的缓存。无背压请求时间内收到的项目都将被丢弃。
*/
static <T> Publisher<T>	toPublisher(LifecycleOwner lifecycle, LiveData<T> liveData)
```

##### Transformations
**摘要**：
LiveData的转换类。用于在观察者的生命周期内传递信息。仅当观察者观察到LiveData对象返回时才进行转换计算。
由于转换计算的延迟性，生命周期的相关行为被隐式传递，无需显式调用或依赖项。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**公有方法**：
``` java
// 在主线程中转换LiveData中持有的数据类型。
static <X, Y> LiveData<Y>	map(LiveData<X> source, Function<X, Y> func)

// func在主线程中执行，应用func函数得到的是另一个LiveData，X->Y涉及了两个LiveData，最终返回的是一个MediatorLiveData。
static <X, Y> LiveData<Y>	switchMap(LiveData<X> trigger, Function<X, LiveData<Y>> func)
```

##### ViewModel
**摘要**：
为Activity或fragment准备和管理数据。由它处理activity或fragment与应用其他部分的沟通工作。
ViewModel关联一个作用范围，一个activity或fragment，仅在作用范围内存活。
因配置发生变化时，ViewModel并不会被销毁，而是重连到新的作用范围。
Activity或fragment通过LiveData或DataBinding观察ViewModel中的数据变化。
ViewModel的唯一职责就是为UI管理数据，从不访问视图结构或持有activity或fragment的引用。
ViewModels可作为同一activity不同fragments的沟通层，fragments可使用相同的键访问关联activity的ViewModel，从而实现fragments间的解耦合，无需fragments直接联系。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**构造器**：
``` java
ViewModel()
```

**可继承方法**：
``` java
// 当ViewModel不再需要时调用。用于清除订阅事件，避免内存泄漏。
void	onCleared()
```

##### AndroidViewModel
**摘要**：
应用上下文感知型ViewModel。其子类必须有个接收Application为唯一参数的构造器。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**继承关系**：
*	ViewModel

**构造器**：
``` java
AndroidViewModel(Application application)
```

**公有方法**：
``` java
<T extends Application> T	getApplication()
```

##### ViewModelProvider
**摘要**：
为范围(activity或fragment)提供ViewModel的工具类。
默认的ViewModelProvider由ViewModelsProviders获取。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**构造器**：
使用factory创建ViewModels，使用owner或store持有ViewModels。
``` java
ViewModelProvider(ViewModelStoreOwner owner, ViewModelProvider.Factory factory)

ViewModelProvider(ViewModelStore store, ViewModelProvider.Factory factory)
```

**公有方法**：
返回已存在的或创建一个新的ViewModel。
``` java
<T extends ViewModel> T	get(Class<T> modelClass)
<T extends ViewModel> T	get(String key, Class<T> modelClass)
```

##### ViewModelProvider.NewInstanceFactory
**摘要**：
简单工厂，调用给定类的无参构造器。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**继承关系**：
*	ViewModelProvider.Factory

**直接子类**：
*	ViewModelProviders.DefaultFactory

**构造器**：
``` java
ViewModelProvider.NewInstanceFactory()
```

##### ViewModelStore
**摘要**：
存储ViewModels的类。在配置发生改变时保持ViewModelStore实例：当旧持有者因为配置变化而销毁，新持有者应当拥有原来的ViewModelStore实例。
如果持有者被销毁且不再重建，它应该调用clear()通知ViewModels不再使用。
ViewModelStores为activities和fragments提供ViewModelStore。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**构造器**：
``` java
ViewModelStore()
```

**公有方法**：
``` java
final void	clear()
```

##### ViewModelStores
**摘要**：
ViewModelStore的工厂类。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**公有方法**：
``` java
static ViewModelStore	of(Fragment fragment)
static ViewModelStore	of(FragmentActivity activity)
```

##### ViewModelProviders
**摘要**：
ViewModelStore工具类。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**构造器**：
``` java
ViewModelProviders()
```

**公有方法**：
``` java
// 默认使用ViewModelProviders.DefaultFactory
static ViewModelProvider	of(Fragment fragment)
static ViewModelProvider	of(FragmentActivity activity)

static ViewModelProvider	of(Fragment fragment, ViewModelProvider.Factory factory)
static ViewModelProvider	of(FragmentActivity activity, ViewModelProvider.Factory factory)
```

##### ViewModelProviders.DefaultFactory
**摘要**：
默认ViewModelProvider.Factory，调用无参构造器创 AndroidViewModel和ViewModel。

**库名**：
android.arch.lifecycle:extensions:1.0.0

**构造器**：
``` java
ViewModelProviders.DefaultFactory(Application application)
```

#### Enums
##### Lifecycle.Event
**摘要**：
生命周期的各个状态事件。

**库名**：
android.arch.lifecycle:common:1.0.3

**枚举实例**：
``` java
Lifecycle.Event 	ON_CREATE
Lifecycle.Event 	ON_START
Lifecycle.Event 	ON_RESUME
Lifecycle.Event 	ON_PAUSE
Lifecycle.Event 	ON_STOP
Lifecycle.Event 	ON_DESTROY
Lifecycle.Event 	ON_ANY
```

##### Lifecycle.State
**摘要**：
生命周期的各个状态。

**库名**：
android.arch.lifecycle:common:1.0.3

**枚举实例**：
``` java
Lifecycle.State 	DESTROYED
Lifecycle.State 	INITIALIZED
Lifecycle.State 	CREATED
Lifecycle.State 	STARTED
Lifecycle.State 	RESUMED
```

**公有方法**：
``` java
boolean	isAtLeast(Lifecycle.State state)
```

