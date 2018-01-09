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
简单Mapkey，value类型为Class&lt;? extends ViewModel&gt;。

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

##### NetworkBoundResource
抽象db与service之间的公共交互。如数据源（本地与网络）监听，数据更新。由于这里涉及了两个数据源，所以引入了LiveData中介人。核心逻辑为：设置数据为加载状态，先查询本地数据库，如果不需要网络查询，直接返回本地数据，否则执行网络查询，成功则处理保存数据，再从数据库返回数据，否则返回数据加载失败信息。
###### 实例方法
* fetchFromNetwork
调用抽象方法，完成网络数据获取。

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

##### Status
枚举类，标识任务状态：加载中，成功，失败。

##### Resource
返回结果包装类：status, message, data(T)

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
login, avatar_url, name, company, repos_url, blog
primaryKeys: login

*	Repo
id, name, full_name, description, stargazers_count, owner(login, url)
primaryKeys: name, owner_login
indices: id, owner_login

*	Contributor
login, contributions, avatar_url, repoName, repoOwner
primaryKeys: repoName, repoOwner, login
foreignKeys: Repo.name, Repo.onwer_login

*	RepoSearchResult
query, repoIds, totalCount, next
primaryKeys: query
TypeConverters: repoInds(List&lt;Integer&gt;, String)

###### daos
*	UserDao
Inserts: User
Querys: (login) -&gt; LiveData&lt;User&gt;

*	RepoDao
Inserts: Repos..., Contributors, Repositories, Repo, RepoSearchResult
Querys: (login, name) -&gt; LiveData&lt;Repo&gt;, (repoOwner, repoName) -&gt; LiveData&lt;List&lt;Contributor&gt;&gt;, (owner) -&gt; LiveData&lt;List&lt;Repo&gt;&gt;, (query) -&gt; List&lt;RepoSearchResult&gt;, (repoIds) -&gt; LiveData&lt;List&lt;Repo&gt;&gt;, (query) -&gt; RepoSearchResult

#### DataBinding
##### BindingAdapters
自定义visibleGone属性方法。

##### FragmentBindingAdapters
自定义imageUrl属性方法。实现Glide加载图片。这里需要传入上下文。所以构造函数为FragmentBindingAdapters(Fragment fragment)。

##### FragmentDataBindingComponent
用于传递上下文给FragmentBindingAdapters。创建绑定时作为传递参数。
``` java
        ContributorItemBinding binding = ContributorItemBinding
                .inflate(LayoutInflater.from(parent.getContext()), parent, false, dataBindingComponent);
```

#### ImageLoader
Glide加载图片，FragmentBindingAdapters自定义的绑定适配器方法中调用。

#### Logger
使用Timber包装android.util.Log类。
在GithubApp中进行配置。
``` java
Timber.plant(new Timber.DebugTree());
```
在需要日志记录的地方调用Timber静态方法执行日志记录。

#### Utils
##### AbsentLiveData
在数据为空时，返回持有null的LiveData实例。

##### LiveDataCallAdapter
Retrofit转换器，将Call&lt;T&gt;转换为LiveData&lt;ApiResponse&lt;R&gt;&gt;

##### LiveDataCallAdapterFactory
LiveDataCallAdapter工厂类。执行类型检测，返回类型符合需求时返回CallAdapter，否则抛出异常。

##### AutoClearedValue
Fragment生命周期持有数据对象。通过注册FragmentLifecycleCallbacks监听，在onFragmentViewDestroyed方法时清除数据，并解除注册。

##### Objects
简化对象比较时的空指针问题。

##### RateLimiter
用于判断何时获取网络数据的工具类。

#### ViewModel
##### GithubViewModelFactory
创建ViewModel工厂。ViewModel是使用集合依赖进行注入。

##### RepoViewModel
仓库及其贡献者。静态内部类Repo持有RepoOwner与RepoName。返回Repo和List&lt;Contributor&gt;。

##### SearchViewModel
搜索关键字、仓库列表、下一页处理器。静态内部类LoadMoreState, NextPageHandler。

##### UserViewModel
用户名、用户、仓库列表。

#### others
##### NavigationController
MainActivity下属三个fragment间的导航控制控制器。

##### RetryCallback
重试按钮回调。

##### DataBoundListAdapter
使用数据绑定和DiffUtil的RecyclerView.Adapter基类。

##### DataBoundViewHolder
DataBoundListAdapter相应的ViewHolder，持有binding(T)。

##### RepoListAdapter && ContributorAdapter
RepoList与ContributorList配置器

### 构建
#### dagger配置
``` gradle
    dataBinding {
        enabled = true
    }
```

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