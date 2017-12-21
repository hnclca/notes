---
title: android.arch.paging
comments: false
toc: true
date: 1970-01-01 00:00:00
tags:
	- android
	- references
---
DataSource -- 分页数据源
PagedList -- 从数据源中加载分页内容的懒加载列表。
PagedListAdapter -- RecyclerView.Adapter包装类。
LivePagedListBuilder -- LiveData&lt;PagedList&gt;构建器。

<!-- more -->
#### Interfaces
##### DataSource.Factory&lt;Key, Value&gt;
**摘要**：
数据源的工厂类

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有方法**：
``` java
abstract DataSource<Key, Value>	create()
```

##### DataSource.InvalidatedCallback
**摘要**：
数据源无效的回调。用于通知旧数据源失效，需要新数据源来加载数据的消息。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有方法**：
``` java
abstract void	onInvalidated()
```

#### Classes
##### DataSource&lt;Key, Value&gt;
**摘要**：
用于加载分页快照数据到PagedList的基类。用于查询分页数据并添加到PagedList，PagedList的数据随着加载越来越多，但已加载的数据不会更新。原始数据发生改变时，新的PagedList/DataSource对将被创建来显示新数据。
null会显示为PagedList中的占位符，DataSource不可返回null数据项。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**直接子类**：
*	ItemKeyedDataSource&lt;Key, Value&gt;
用于根据唯一项目Id加载数据。
*	PageKeyedDataSource&lt;Key, Value&gt;
用于按页数加载数据。
*	PositionalDataSource&lt;T&gt;
用于在任意位置加载固定大小的数据项。

**公有方法**：
``` java
void	invalidate()
boolean	isInvalid()

void	addInvalidatedCallback(DataSource.InvalidatedCallback onInvalidatedCallback)
void	removeInvalidatedCallback(DataSource.InvalidatedCallback onInvalidatedCallback)
```

##### ItemKeyedDataSource&lt;Key, Value&gt;
**摘要**：
用于分页内容的递增数据加载器，需要根据已加载的数据内容请求下一页数据。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**继承关系**：
*	DataSource&lt;Key, Value&gt;

**构造器**：
``` java
ItemKeyedDataSource()
```

**公有方法**：
``` java
// 返回唯一性的项目ID。
abstract Key	getKey(Value item)

// 初始化PagedList数据。
abstract void	loadInitial(LoadInitialParams<Key> params, LoadInitialCallback<Value> callback)

// 两种数据添加方式。
abstract void	loadBefore(LoadParams<Key> params, LoadCallback<Value> callback)
abstract void	loadAfter(LoadParams<Key> params, LoadCallback<Value> callback)
```

##### ItemKeyedDataSource.LoadCallback&lt;Value&gt;
**摘要**：
数据源加载方法的回调，用于返回数据。一个回调仅可调用一次，否则抛出异常。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**直接子类**：
*	ItemKeyedDataSource.LoadInitialCallback&lt;Value&gt;

**公有方法**：
``` java
// 允许返回不同大小的列表，如果没有数据则返回空列表。
void	onResult(List<Value> data)
```

##### ItemKeyedDataSource.LoadInitialCallback&lt;Value&gt;
**摘要**：
用于返回初始化数据，位置和数量是可选的。一个回调仅可调用一次，否则抛出异常。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**继承关系**：
*	ItemKeyedDataSource.LoadCallback&lt;Value&gt;

**公有方法**：
``` java
// 允许返回不同大小的列表，如果没有数据则返回空列表。
void	onResult(List<Value> data, int position, int totalCount)
```

##### ItemKeyedDataSource.LoadParams&lt;Key&gt;
**摘要**：
用于加载的输入参数。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有属性**：
``` java
public final Key	key
public final int	requestedLoadSize
```

##### ItemKeyedDataSource.LoadInitialParams&lt;Key&gt;
**摘要**：
用于初始化加载的输入参数。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有方法**：
``` java
// 如果禁用占位符，返回数据时位置信息将被忽略。
public final boolean	placeholdersEnabled
public final Key	requestedInitialKey
public final int	requestedLoadSize
```

##### PageKeyedDataSource&lt;Key, Value&gt;
**摘要**：
用于分页内容的递增数据加载器，使用前一页/后一页方式请求数据。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**继承关系**：
*	DataSource&lt;Key, Value&gt;

**构造器**：
``` java
PageKeyedDataSource()
```

**公有方法**：
``` java
// 返回唯一性的项目ID。
abstract Key	getKey(Value item)

// 初始化PagedList数据。
abstract void	loadInitial(LoadInitialParams<Key> params, LoadInitialCallback<Key, Value> callback)

// 两种加载数据添加方式。
abstract void	loadBefore(LoadParams<Key> params, LoadCallback<Key, Value> callback)
abstract void	loadAfter(LoadParams<Key> params, LoadCallback<Key, Value> callback)
```

##### PageKeyedDataSource.LoadCallback&lt;Key, Value&gt;
**摘要**：
数据源加载方法的回调，用于返回数据。一个回调仅可调用一次，否则抛出异常。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有方法**：
``` java
void	onResult(List<Value> data, Key adjacentPageKey)
```

##### PageKeyedDataSource.LoadInitialCallback&lt;Key, Value&gt;
**摘要**：
数据源初始化加载的回调，用于返回数据。一个回调仅可调用一次，否则抛出异常。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有方法**：
``` java
void	onResult(List<Value> data, int position, int totalCount, Key previousPageKey, Key nextPageKey)
void	onResult(List<Value> data, Key previousPageKey, Key nextPageKey)
```

##### PageKeyedDataSource.LoadParams&lt;Key&gt;
**摘要**：
用于加载的输入参数。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有属性**：
``` java
public final Key	key
public final int	requestedLoadSize
```

##### PageKeyedDataSource.LoadInitialParams&lt;Key&gt;
**摘要**：
用于初始化加载的输入参数。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有属性**：
``` java
public final boolean	placeholdersEnabled
public final int	requestedLoadSize
```

##### PositionalDataSource&lt;T&gt;
**摘要**：
基于固定大小、可计数的位置加载器。支持任意页面位置的固定大小的数据加载。
Room可生成PositionalDataSource工厂类。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**继承关系**：
*	DataSource&lt;Key, Value&gt;

**构造器**：
``` java
PositionalDataSource()
```

**公有方法**：
``` java
// 在LoadInitialCallback后调用加载额外的页面数据。
// 结果列表必须是个多个页面尺寸，用于高效平铺。
abstract void	loadRange(PositionalDataSource.LoadRangeParams params, LoadRangeCallback<T> callback)

// 加载初始化数据，结果列表必须是个多个页面尺寸，用于高效平铺。
abstract void	loadInitial(PositionalDataSource.LoadInitialParams params, LoadInitialCallback<T> callback)

// 用于在loadInitial计算初始位置和初始大小。
static int	computeInitialLoadPosition(PositionalDataSource.LoadInitialParams params, int totalCount)
static int	computeInitialLoadSize(PositionalDataSource.LoadInitialParams params, int initialLoadPosition, int totalCount)
```

##### PositionalDataSource.LoadInitialCallback&lt;T&gt;
**摘要**：
加载初始化数据的回调，一个回调只能调用一次。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有方法**：
``` java
void	onResult(List<T> data, int position, int totalCount)
void	onResult(List<T> data, int position)
```

##### PositionalDataSource.LoadInitialParams
**摘要**：
初始化加载参数。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有属性**：
``` java
public final int	pageSize
public final boolean	placeholdersEnabled
public final int	requestedLoadSize
public final int	requestedStartPosition
```

##### PositionalDataSource.LoadRangeCallback&lt;T&gt;
**摘要**：
加载数据的回调，一个回调只能调用一次。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有方法**：
``` java
void	onResult(List<T> data)
```

##### PositionalDataSource.LoadRangeParams
**摘要**：
加载参数。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有属性**：
``` java
public final int	loadSize
public final int	startPosition
```

##### PagedList&lt;T&gt;
**摘要**：
从数据源中加载分页内容的懒加载列表。使用get(int)访问项目，loadAround(int)触发下一次加载。PagedListAdapter绑定PagedList到RecyclerView.
可用于无界、可循环流动或非常大但可计数的列表，通过PagedList.Config控制加载数量、何时加载。

占位符优点：
*	传递全满的列表给展现层，以支持滚动条，不管加载成功与否，都可快速滚动到任意位置。
*	避免在加载列表底部显示加载标识。

占位符缺点：
*	适配器需要计算null项数量，需要在数据绑定到RecyclerView.ViewHolder提供默认值。
*	数据项目显示尺寸不能大小不一，防止加载数据时淡入淡出动画效果交叉。
*	需要计算数据集大小，代价昂贵或不可能，这取决于数据的来源。

占位符默认启动，可通过如下方法禁用：
*	DataSource在初始化加载时不计算数据集大小；
*	PagedList.Config中setEnablePlaceholders(boolean)值为false;

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**继承关系**：
*	java.util.AbstractList

**可继承属性**：
``` java
protected final ArrayList<WeakReference<PagedList.Callback>>	mCallbacks
```

**公有方法**：
``` java
// 从DataSource解绑。
void	detach()
boolean	isDetached()

// 列表配置项
PagedList.Config	getConfig()

// 列表大小
int	size()
boolean	isImmutable()
// 仅当列表为不可变列表时，返回列表的不可变快照。
List<T>	snapshot()
// 返回数据在列表中的偏移位置。
// 对于PositionalDataSource，get(i)的数据项实际位置为i+getPositionOffset()
// 对于不使用位置的ItemKeyedDataSource和PageKeyedDataSource，返回0。
int	getPositionOffset()

// 加载邻近数据
void	loadAround(int index)
// 最接近loadAround(int)位置的key。
abstract Object	getLastKey()

// previousSnapshot更新时会触发callback回调。
void	addWeakCallback(List<T> previousSnapshot, PagedList.Callback callback)
void	removeWeakCallback(PagedList.Callback callback)
```

##### PagedList.Callback
**摘要**：
当数据加载到列表时触发回调。
用于监听分页数据，由主线程的定义的setMainThreadExecutor(Executor)线程池调用。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**构造器**：
``` java
PagedList.Callback()
```

**公有方法**：
count代表变动的数据量。
``` java
abstract void	onChanged(int position, int count)
abstract void	onInserted(int position, int count)
abstract void	onRemoved(int position, int count)
```

##### PagedList.BoundaryCallback&lt;T&gt;
**摘要**：
当数据达到可用数据顶部或底部时触发回调。
当本地存储为网络数据的缓存时，网络数据先保存到数据库，数据库再传给UI。当数据库的数据全部显示给UI后，该回调用于触发网络加载。
BoundaryCallback实例在多个PagedList中 setBoundaryCallback(PagedList.BoundaryCallback)后，该回调会触发多次，避免在加载过程中再次触发网络加载。
由于回调仅传递最前或最后的数据项，并没有传递数据量，因而在禁用占位符的DataSource会计算不完全。回调独立于DataSource格式Key，Key因为本地和网络存储的不同而不可知。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**构造器**：
``` java
PagedList.BoundaryCallback()
```

**公有方法**：
count代表变动的数据量。
``` java
void	onItemAtEndLoaded(T itemAtEnd)
void	onItemAtFrontLoaded(T itemAtFront)
void	onZeroItemsLoaded()
```

##### PagedList.Builder&lt;Key, Value&gt;
**摘要**：
PagedList的构造类。
DataSource, Config, 主线程和后台线程线程池都必须提供。
PagedList必须在构造时查询初始化数据，避免空的PagedList传递给UI。
如果期望得到 LiveData<PagedList<...>>，则LivePagedListBuilder会在后台线程中自动完成这些创建。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**构造器**：
``` java
PagedList.Builder(DataSource<Key, Value> dataSource, PagedList.Config config)
PagedList.Builder(DataSource<Key, Value> dataSource, int pageSize)
```

**公有方法**：
``` java
PagedList<Value>	build()
// 设置用于初始化的key。
Builder<Key, Value>	setInitialKey(Key initialKey)
Builder<Key, Value>	setMainThreadExecutor(Executor mainThreadExecutor)
Builder<Key, Value>	setBackgroundThreadExecutor(Executor backgroundThreadExecutor)
Builder<Key, Value>	setBoundaryCallback(BoundaryCallback boundaryCallback)
```

##### PagedList.Config
**摘要**：
配置PagedList加载数据的方式。
使用PagedList.Config.Builder构造实例，并自定义加载行为，如 setPageSize(int)一次加载的数据量。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有属性**：
``` java
public final boolean	enablePlaceholders
// 定义初始化加载时数据量，通常大于正常页面加载数。
public final int	initialLoadSizeHint
public final int	pageSize
public final int	prefetchDistance
```

##### PagedList.Config.Builder
**摘要**：
PagedList.Config构造器。
必须调用setPageSize(int)设置最小页面加载数量。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**公有属性**：
``` java
PagedList.Config.Builder()
```

**公有方法**：
``` java
PagedList.Config	build()
PagedList.Config.Builder	setEnablePlaceholders(boolean enablePlaceholders)
PagedList.Config.Builder	setInitialLoadSizeHint(int initialLoadSizeHint)
PagedList.Config.Builder	setPageSize(int pageSize)
PagedList.Config.Builder	setPrefetchDistance(int prefetchDistance)
```

##### PagedListAdapter&lt;T, VH extends RecyclerView.ViewHolder&gt;
**摘要**：
PagedListAdapterHelper用于实现PagedListAdapter的默认行为，如项目数计算，监听数据更新回调。
当使用LiveData<PagedList>时更简单，数据可用时调用setList(PagedList)。
PagedListAdapter监听PagedList加载回调，并使用DiffUtil在后台线程中计算更细粒度的数据更新。
更多数据加载时，处理列表的内部分页和新PagedList更新。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**继承关系**：
* android.support.v7.widget.RecyclerView.Adapter

**构造器**：
``` java
PagedListAdapter(DiffCallback<T> diffCallback)
PagedListAdapter(ListAdapterConfig<T> config)
```

**公有方法**：
``` java
PagedList<T>	getCurrentList()
void	onCurrentListChanged(PagedList<T> currentList)
void	setList(PagedList<T> pagedList)
```

##### PagedListAdapterHelper&lt;T&gt;
**摘要**：
实现PagedList到RecyclerView.Adapter的辅助类。
PagedListAdapter包装类直接代替该辅助类更简单些。该类用于继承了不支持分页的Adapter基类的复杂类。
更多数据加载时，处理列表的内部分页和新PagedList更新。
PagedListAdapterHelper绑定到LiveData&lt;PagedList&gt;时更简单，它监听PagedList加载回调，收到新数据后在后台线程使用DiffUtil计算更新。
提供类似列表API的方法getItem(int) and getItemCount()供adapter获取展示数据。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**构造器**：
``` java
// PagedListAdapterHelper(new ListAdapterHelper.AdapterCallback(adapter), new ListAdapterConfig.Builder().setDiffCallback(diffCallback).build())简化版本。
PagedListAdapterHelper(Adapter adapter, DiffCallback<T> diffCallback)
PagedListAdapterHelper(ListUpdateCallback listUpdateCallback, ListAdapterConfig<T> config)
```

**公有方法**：
``` java
void	setList(PagedList<T> pagedList)
PagedList<T>	getCurrentList()
T	getItem(int index)
int	getItemCount()
```

##### LivePagedListBuilder&lt;Key, Value&gt;
**摘要**：
LiveData<PagedList>构造器，指定DataSource.Factory和PagedList.Config。

**库名**：
android.arch.paging:runtime:1.0.0-alpha4-1

**构造器**：
``` java
LivePagedListBuilder(Factory<Key, Value> dataSourceFactory, PagedList.Config config)
LivePagedListBuilder(Factory<Key, Value> dataSourceFactory, int pageSize)
```

**公有方法**：
``` java
LiveData<PagedList<Value>>	build()
LivePagedListBuilder<Key, Value>	setBackgroundThreadExecutor(Executor backgroundThreadExecutor)
LivePagedListBuilder<Key, Value>	setBoundaryCallback(BoundaryCallback<Value> boundaryCallback)
LivePagedListBuilder<Key, Value>	setInitialLoadKey(Key key)
```

##### LivePagedListProvider&lt;Key, Value&gt;(Deprecated)
**摘要**：
已过期，请使用LivePagedListBuilder构建LiveData<PagedList>，它提供相同的构造功能，拥有更多自定义性，更简单的默认值。DataSource被分离到DataSource.Factory中，让其提供可自我否定的数据源序列。如果从Room获取数据，也可让Dao返回一个DataSource.Factory，以便使用LivePagedListBuilder创建LiveData<PagedList>。