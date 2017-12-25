---
title: android.support.v7.recyclerview.extensions
comments: false
toc: true
tags:
  - android
  - references
date: 1970-01-01 08:00:00
---

DiffUtil相关的扩展。

<!-- more -->
#### Classes
##### DiffCallback&lt;T&gt;
**摘要**：
通知PagedListAdapterHelper如何使用DiffUtil在后台线程计算列表更新。
AdapterHelp会传输不同的列表到该回调，为了调用DiffUtil.Callback进行列表差异计算。
注意：这个库可能在Paging库最终发布前移动。

**库名**：
未知

**构造器**：
``` java
DiffCallback()
```

**公有方法**：
``` java
abstract boolean	areContentsTheSame(T oldItem, T newItem)
abstract boolean	areItemsTheSame(T oldItem, T newItem)
Object	getChangePayload(T oldItem, T newItem)
```

##### ListAdapter&lt;T, VH extends RecyclerView.ViewHolder&gt;
**摘要**：
在后台线程计算列表差异的RecyclerView.Adapter基类。
ListAdapterHelper便捷包装类，实现Item项的访问和计算的默认行为。
使用LiveData&lt;List&gt;(不是必须要求)时，使用setList(List)设置列表。
若希望拥有更多控制权或提供特定的基类，请使用提供了差异化事件自定义映射功能的ListAdapterHelper。

**库名**：
未知

**继承关系**：
* 	android.support.v7.widget.RecyclerView.Adapter&lt;VH extends android.support.v7.widget.RecyclerView.ViewHolder&gt;

**可继承构造器**：
``` java
ListAdapter(DiffCallback<T> diffCallback)
ListAdapter(ListAdapterConfig<T> config)
```

**公有方法**：
``` java
void	setList(List<T> list)
```

**示例代码**：
``` java
 @Dao
 interface UserDao {
     @Query("SELECT * FROM user ORDER BY lastName ASC")
     public abstract LiveData<List<User>> usersByLastName();
 }

 class MyViewModel extends ViewModel {
     public final LiveData<List<User>> usersList;
     public MyViewModel(UserDao userDao) {
         usersList = userDao.usersByLastName();
     }
 }

 class MyActivity extends AppCompatActivity {
     @Override
     public void onCreate(Bundle savedState) {
         super.onCreate(savedState);
         MyViewModel viewModel = ViewModelProviders.of(this).get(MyViewModel.class);
         RecyclerView recyclerView = findViewById(R.id.user_list);
         UserAdapter<User> adapter = new UserAdapter();
         viewModel.usersList.observe(this, list -> adapter.setList(list));
         recyclerView.setAdapter(adapter);
     }
 }

 class UserAdapter extends ListAdapter<User, UserViewHolder> {
     public UserAdapter() {
         super(User.DIFF_CALLBACK);
     }
     @Override
     public void onBindViewHolder(UserViewHolder holder, int position) {
         holder.bindTo(getItem(position));
     }
     public static final DiffCallback<User> DIFF_CALLBACK = new DiffCallback<User>() {
         @Override
         public boolean areItemsTheSame(
                 @NonNull User oldUser, @NonNull User newUser) {
             // User properties may have changed if reloaded from the DB, but ID is fixed
             return oldUser.getId() == newUser.getId();
         }
         @Override
         public boolean areContentsTheSame(
                 @NonNull User oldUser, @NonNull User newUser) {
             // NOTE: if you use equals, your object must properly override Object#equals()
             // Incorrectly returning false here will result in too many animations.
             return oldUser.equals(newUser);
         }
     }
 }
```


##### ListAdapterConfig&lt;T&gt;
**摘要**：
ListAdapter, ListAdapterHelper的配置类，类似后台线程列表差异化适配逻辑。
至少定义Item项的差异化行为，使用DiffCallback计算差异并传递到RecyclerView适配器。

**库名**：
未知

**公有方法**：
``` java
Executor	getMainThreadExecutor()
Executor	getBackgroundThreadExecutor()
DiffCallback<T>	getDiffCallback()
```

##### ListAdapterConfig.Builder&lt;T&gt;
**摘要**：
ListAdapterConfig构建类。
至少要调用setDiffCallback(DiffCallback)指定DiffCallback。

**库名**：
未知

**构造器**：
``` java
ListAdapterConfig.Builder()
```

**公有方法**：
``` java
ListAdapterConfig<T>	build()
Builder<T>	setMainThreadExecutor(Executor executor)
Builder<T>	setBackgroundThreadExecutor(Executor executor)
Builder<T>	setDiffCallback(DiffCallback<T> diffCallback)
```

##### ListAdapterHelper&lt;T&gt;
**摘要**：
RecyclerView.Adapter辅助类，用于列表改变时使用DiffUtil在后台线程计算列表变化并通知适配器。
ListAdapter包装通常可以直接代替ListAdapterHelper使用，辅助类主要用于无法继承ListAdapter的复杂类。
LiveData展示数据更加方便，它收到新列表后使用DiffUtil在后台线程计算列表内容差异。
提供类似API(getItem(int)和getItemCount())供适配器获取展示数据。

**库名**：
未知。

**构造器**：
``` java
ListAdapterHelper(ListUpdateCallback listUpdateCallback, ListAdapterConfig<T> config)
```

**公有方法**：
``` java
T	getItem(int index)
int	getItemCount()
void	setList(List<T> newList)
```

**示例代码**：
``` java
 @Dao
 interface UserDao {
     @Query("SELECT * FROM user ORDER BY lastName ASC")
     public abstract LiveData<List<User>> usersByLastName();
 }

 class MyViewModel extends ViewModel {
     public final LiveData<List<User>> usersList;
     public MyViewModel(UserDao userDao) {
         usersList = userDao.usersByLastName();
     }
 }

 class MyActivity extends AppCompatActivity {
     @Override
     public void onCreate(Bundle savedState) {
         super.onCreate(savedState);
         MyViewModel viewModel = ViewModelProviders.of(this).get(MyViewModel.class);
         RecyclerView recyclerView = findViewById(R.id.user_list);
         UserAdapter<User> adapter = new UserAdapter();
         viewModel.usersList.observe(this, list -> adapter.setList(list));
         recyclerView.setAdapter(adapter);
     }
 }

 class UserAdapter extends RecyclerView.Adapter<UserViewHolder> {
     private final ListAdapterHelper<User> mHelper;
     public UserAdapter(ListAdapterHelper.Builder<User> builder) {
         mHelper = new ListAdapterHelper(this, User.DIFF_CALLBACK);
     }
     @Override
     public int getItemCount() {
         return mHelper.getItemCount();
     }
     public void setList(List<User> list) {
         mHelper.setList(list);
     }
     @Override
     public void onBindViewHolder(UserViewHolder holder, int position) {
         User user = mHelper.getItem(position);
         holder.bindTo(user);
     }
     public static final DiffCallback<User> DIFF_CALLBACK = new DiffCallback<User>() {
         @Override
         public boolean areItemsTheSame(
                 @NonNull User oldUser, @NonNull User newUser) {
             // User properties may have changed if reloaded from the DB, but ID is fixed
             return oldUser.getId() == newUser.getId();
         }
         @Override
         public boolean areContentsTheSame(
                 @NonNull User oldUser, @NonNull User newUser) {
             // NOTE: if you use equals, your object must properly override Object#equals()
             // Incorrectly returning false here will result in too many animations.
             return oldUser.equals(newUser);
         }
     }
 }
```
