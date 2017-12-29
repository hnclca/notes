---
title: GithubBrowserSample
comments: false
toc: true
date: 1971-01-01 08:00:00
tags:
	- sample
	- open source
	- java
---

[TOC]

[GithubBrowserSample][sample]使用安卓架构组件和Dagger2的简单app。

### 功能介绍
#### 搜索仓库
搜索Github上的仓库。

#### 仓库页
展示仓库描述及贡献者。

#### 贡献者及其仓库页
展示贡献者及其创建的仓库。

<!-- more -->

### 依赖库
*	Android Support Library
AppCompat, RecycleView, CardView, Design, ConstraintLayout
*	Android Architecture Components
ROOM, Lifecycle
*	Android Data Binding
*	Dagger 2
*	Retrofit
*	Glide
*	Timber
*	espresso
*	mockito

### 项目结构
#### DI
##### AndroidInjection与AndroidSupportInjection
Dagger2提供的Android类型注入工具类。

##### AndroidInjectionModule
Dagger2提供的Android类型实例依赖模块。

##### AppInjector
辅助类，在Application中调用，激活AppComponent组件，使用registerActivityLifecycleCallbacks实现Activity类的统一注入。
同时使用getSupportFragmentManager().registerFragmentLifecycleCallbacks实现fragment统一注入。
**注意**:
Dagger提供了DaggerAppCompatActivity和DaggerFragment基类，可移除，直接在Application中创建组件对象。

##### Injectable
自定义可注入接口，标识Activity/Fragment类是否可注入。
**注意**:
Dagger提供了DaggerAppCompatActivity和DaggerFragment基类，可移除。

##### AppComponent
根组件，单例，由于application是平台实例化，这里使用了实例注入@BindsInstance。

##### AppModule
###### 提供方法
*	GithubService
Retrofit建造实例，配置GsonConverter转换器与LiveDataCallAdapter回调监听。
*	GithubDb
Room建造实例。
*	UserDao
db.userDao()
*	RepoDao
db.repoDao()

###### 依赖模块
*	ViewModelModule

##### ViewModelModule
使用@Binds提供了ViewModelProvider.Factory方法，使用Map MultiBinding将User, Repo, Search的ViewModel对象添加到Map中。

##### ViewModuleKey
简单Mapkey，value类型为Class<? extends ViewModel>。

##### MainActivityModule和FragmentBuildersModule
使用了@ContributesAndroidInjector注解简化创建AndroidInjector实例方法。

#### 线程
##### AppExecutors
线程池：io, network, mainThread.

##### 线程注解
线程注解@MainThread， @WorkerThread

#### Repository
##### RepoRepository & UserRepository
Db（执行事务操作），Dao, Service, executors协作完成数据操作。

##### NetworkBoundResouce
抽象db与service之间的公共交互。如数据源（本地与网络）监听，数据更新。由于这里涉及了两个数据源，所以引入了LiveData中介人。核心逻辑为：设置数据为加载状态，先查询本地数据库，如果不需要网络查询，直接返回本地数据，否则执行网络查询，成功则处理保存数据，再从数据库返回数据，否则返回数据加载失败信息。
###### 抽象方法
* loadFromDb()
加载本地数据库数据。

* shouldFetch(data)
判断是否需要网络更新。

* createCall()
创建网络请求

* processResponse(response)
处理网络数据。

* saveCallResult(data)
保存数据

* onFetchFailed()
处理网络失败。

##### FetchNextSearchPageTask
获取搜索结果的下一页内容。

#### WebService
##### GithubService
管理Http APIs。

##### RepoSearchResponse
POJO类，成员：total，nextPage，items

##### ApiResponse
网络请求结果封装类。
成员：code, body(T), errorMessage, links(Map)
解析headers取得连接匹配，通过links获取nextPage网址。

#### Storage
##### GithubDb
###### entities
*	User
*	Repo
*	Contributor
*	RepoSearchResult

###### daos
*	UserDao
*	RepoDao

##### GithubTypeConverters
##### RepoDao
##### UserDao


#### DataBinding


#### ImageLoader
#### Logger

### 构建

### 测试
#### 本地单元测试
##### ViewModel

##### Repository

##### WebService

#### 设备测试

##### UI

##### Database

#### 代码覆盖工具JaCoCo

#### Lint

[sample]:https://github.com/googlesamples/android-architecture-components/tree/master/GithubBrowserSample