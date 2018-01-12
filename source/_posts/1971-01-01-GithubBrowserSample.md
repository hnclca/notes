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
Room, Lifecycle
*	Android Data Binding
*	Dagger 2
*	Retrofit
*	Glide
*	Timber
*	espresso
*	mockito

### 项目结构
#### DI
##### 组件
###### AppComponent
根组件，单例，由于application是平台实例化，这里使用了实例注入@BindsInstance。

##### 模块
###### AndroidInjectionModule或AndroidSupportInjectionModule
Dagger2提供的Android类型实例依赖模块。

###### AppModule
全局单例依赖对象。

*	GithubService
Retrofit建造实例，配置GsonConverter转换器与LiveDataCallAdapter回调监听。

*	GithubDb
Room建造实例。

*	UserDao
db.userDao()

*	RepoDao
db.repoDao()

###### ViewModelModule
使用@Binds提供了ViewModelProvider.Factory方法，使用Map MultiBinding将User, Repo, Search的ViewModel对象添加到Map中。

*	SearchViewModel
*	UserViewModel
*	RepoViewModel

###### MainActivityModule和FragmentBuildersModule
使用了@ContributesAndroidInjector注解简化创建AndroidInjector实例方法。

##### 辅助类
###### AndroidInjection与AndroidSupportInjection
Dagger2提供的Android类型注入工具类。
**注意**:
Dagger提供了DaggerAppCompatActivity和DaggerFragment基类，在基类中已调用了inject方法，直接继承时，无需手动调用。

###### AppInjector
辅助类，在Application中调用，激活AppComponent组件，使用registerActivityLifecycleCallbacks实现Activity类的统一注入。
同时使用getSupportFragmentManager().registerFragmentLifecycleCallbacks实现fragment统一注入。
**注意**:
Dagger提供了DaggerAppCompatActivity和DaggerFragment基类，可移除，直接在Application中创建组件对象。

###### Injectable
自定义可注入接口，标识Activity/Fragment类是否可注入。
**注意**:
Dagger提供了DaggerAppCompatActivity和DaggerFragment基类，可移除。

###### ViewModuleKey
简单Mapkey，value类型为Class&lt;? extends ViewModel&gt;。

#### 线程
##### AppExecutors
线程池：io, network, mainThread.

##### 线程注解
线程注解@MainThread， @WorkerThread

#### 数据层
##### RepoRepository & UserRepository
使用Db, Dao, Service, executors协作完成数据操作。唯一暴露给界面层的对象。

###### NetworkBoundResource
抽象db与service之间的公共交互。如数据源（本地与网络）监听，数据更新。由于这里涉及了两个数据源，所以引入了LiveData中介人。核心逻辑为：设置数据为加载状态，先查询本地数据库，如果不需要网络查询，直接返回本地数据，否则执行网络查询，成功则处理保存数据，再从数据库返回数据，否则返回数据加载失败信息。

* fetchFromNetwork
具体方法，调用抽象方法完成网络、本地数据获取逻辑。

* loadFromDb()
* shouldFetch(data)
* createCall()
* processResponse(response)
* saveCallResult(data)
* onFetchFailed()
抽象方法由子类实现各业务方法。

###### FetchNextSearchPageTask
获取搜索结果的下一页内容。

###### 与界面层通信数据包装类
* Resource
返回结果包装类：status, message, data(T)

* Status
枚举类，标识数据状态：加载中，成功，失败。

###### GithubService
管理Http APIs。负责网络数据获取。

* ApiResponse
网络请求结果包装类。
成员：code, body(T), errorMessage, links(Map)
解析headers取得连接匹配，通过links获取nextPage网址。

###### GithubDb
本地数据库管理类。

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

*	UserDao
Inserts: User
Querys: (login) -&gt; LiveData&lt;User&gt;

*	RepoDao
Inserts: Repos..., Contributors, Repositories, Repo, RepoSearchResult
Querys: (login, name) -&gt; LiveData&lt;Repo&gt;, (repoOwner, repoName) -&gt; LiveData&lt;List&lt;Contributor&gt;&gt;, (owner) -&gt; LiveData&lt;List&lt;Repo&gt;&gt;, (query) -&gt; List&lt;RepoSearchResult&gt;, (repoIds) -&gt; LiveData&lt;List&lt;Repo&gt;&gt;, (query) -&gt; RepoSearchResult

#### DataBinding
##### FragmentDataBindingComponent
绑定上下文参数，传递给DataBindingUtil。管理BindingAdapters。
``` java
        ContributorItemBinding binding = ContributorItemBinding
                .inflate(LayoutInflater.from(parent.getContext()), parent, false, dataBindingComponent);
```

###### BindingAdapters
与上下文无关的BindingAdapters。

###### FragmentBindingAdapters
与上下文有关的BindingAdapters。

#### ImageLoader
Glide加载图片，在FragmentBindingAdapters自定义的绑定适配器方法中调用。

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
界面层获取数据的唯一访问入口。
##### GithubViewModelFactory
创建ViewModel工厂。ViewModel是使用集合依赖进行注入。

##### RepoViewModel
仓库及其贡献者。输入RepoId，输出Repo及List&lt;Contributor&gt;。

##### SearchViewModel
搜索仓库。输入query，输出List&lt;Repo&gt;。

##### UserViewModel
用户及用户Repos。输入login，输出User, List&lt;Repo&gt;。

#### 界面工具
##### NavigationController
MainActivity下属三个fragment间的导航控制控制器。

##### RetryCallback
重试按钮回调接口。

##### DataBoundListAdapter
使用数据绑定和DiffUtil的RecyclerView.Adapter基类。

* RepoListAdapter
* ContributorAdapter

##### DataBoundViewHolder
DataBoundListAdapter相应的ViewHolder，持有binding(T)。

### 构建
#### DataBing配置
``` gradle
    dataBinding {
        enabled = true
    }
```

#### 版本及库管理
version.gradle
##### 引入
``` gradle
buildscript {
	apply from: 'version.gradle'
    ...
}
```

##### 变量
``` gradle
// 声明
ext.deps = [:]
def support = [:]
support.v4 = "..."
deps.support = support

// 使用
dependencies {
	implementation deps.support.v4
}
```

##### 方法
``` gradle
// 声明
def addRepos(RepositoryHandler handler) {
	handler.google()
    handler.jcenter()
    handler.maven { url '...' }
}
ext.addRepos = this.&addRepos

// 使用
allprojects {
	addRepos(repositories)
}
```

#### 测试
##### 测试覆盖率
``` gradle
android {
	buildTypes {
    	debug {
        	testCoverageEnabled !project.hasProperty('android.injected.invoked.from.ide')
        }
    }
}

jacoco {
    toolVersion = "0.7.4+"
}

task fullCoverageReport(type: JacocoReport) {
    dependsOn 'createDebugCoverageReport'
    dependsOn 'testDebugUnitTest'
    reports {
        xml.enabled = true
        html.enabled = true
    }

    def fileFilter = ['**/R.class', '**/R$*.class', '**/BuildConfig.*', '**/Manifest*.*',
                      '**/*Test*.*', 'android/**/*.*',
                      '**/*_MembersInjector.class',
                      '**/Dagger*Component.class',
                      '**/Dagger*Component$Builder.class',
                      '**/*_*Factory.class',
                      '**/*ComponentImpl.class',
                      '**/*SubComponentBuilder.class']
    def debugTree = fileTree(dir: "${buildDir}/intermediates/classes/debug", excludes: fileFilter)
    def mainSrc = "${project.projectDir}/src/main/java"

    sourceDirectories = files([mainSrc])
    classDirectories = files([debugTree])
    executionData = fileTree(dir: "$buildDir", includes: [
            "jacoco/testDebugUnitTest.exec",
            "outputs/code-coverage/connected/*coverage.ec"
    ])
}
```

##### 静态代码检查
``` gradle
android {
    lintOptions {
        lintConfig rootProject.file('lint.xml')
    }
}
```

##### 添加测试公共类文件夹
``` gradle
android {
    sourceSets {
        androidTest.java.srcDirs += "src/test-common/java"
        test.java.srcDirs += "src/test-common/java"
    }
}
```

### 测试
#### 本地单元测试
##### WebService
###### MockWebService
com.squareup.okhttp3:mockwebserver:$versions.mockwebserver

* startService
``` java
    @Before
    public void createService() throws IOException {
        mockWebServer = new MockWebServer();
        service = new Retrofit.Builder()
                .baseUrl(mockWebServer.url("/"))
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(new LiveDataCallAdapterFactory())
                .build()
                .create(GithubService.class);
    }
```

* stopService
``` java
    @After
    public void stopService() throws IOException {
        mockWebServer.shutdown();
    }
```

* mockResponse
``` java
    private void enqueueResponse(String fileName, Map<String, String> headers) throws IOException {
        InputStream inputStream = getClass().getClassLoader()
                .getResourceAsStream("api-response/" + fileName);
        BufferedSource source = Okio.buffer(Okio.source(inputStream));
        MockResponse mockResponse = new MockResponse();
        for (Map.Entry<String, String> header : headers.entrySet()) {
            mockResponse.addHeader(header.getKey(), header.getValue());
        }
        mockWebServer.enqueue(mockResponse
                .setBody(source.readString(StandardCharsets.UTF_8)));
    }
```

* recordedRequest
``` java
    enqueueResponse("user-yigit.json");
    RecordedRequest request = mockWebServer.takeRequest();
```

##### Repository

##### WebService

#### 设备测试

##### UI

##### Database

#### 代码覆盖工具JaCoCo

#### Lint

[sample]:https://github.com/googlesamples/android-architecture-components/tree/master/GithubBrowserSample