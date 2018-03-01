---
title: Data Binding
comments: true
toc: true
date: 2018-01-02 14:24:14
tags:
	- data binding
	- android
---

数据绑定库用于编写声明式布局，减少必要的实现布局与程序逻辑绑定的胶水代码。
数据绑定库是支持库，支持Android 2.1（API 7）及以上平台。
最低要求Android Gradle Plugin 1.5.0-alpha1。
最低要求Android Studio 1.3。

<!-- more -->

### 编译环境
如果依赖库使用了数据绑定，则app模块中也必须配置数据绑定功能。
#### gradle脚本配置
``` gradle
android {
	...
	dataBinding {
    	enabled = true
    }
}
```

#### 数据绑定编译器V2
Android Gradle Plugin 3.1.0 Canary 6提供了可选编译器V2。启用该编译器需要在gradle.properties中配置。
```
android.databinding.enableV2=true
```
##### V2与V1区别
V2与V1不相兼容。
1.	ViewBinding类由安卓gradle插件生成，而不是java编译器。避免java编译过程中因不相关原因失败导致许多假性错误。
2.	V2改善了多模块项目中数据绑定性能，因为生成的绑定类和映射信息被保留了，不像V1在编译app时需要重新生成（为了共享和访问生成的final BR和R文件）。
3.	V1中允许提供绑定适配器替换依赖中的绑定适配器，V2中仅在自身模块、应用和依赖中生效。
4.	不同的模块不能在清单中使用相同的包名，因为数据绑定会使用包名产生绑定映射类。

### 简单数据绑定
#### 声明式布局文件
使用layout使用为根标签，它拥有两个子标签：data用来导入包(默认仅导入java.lang.*)和声明数据变量，view相当于传统的布局根标签，使用@{}语法应用绑定表达式。
``` java
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"/>
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.lastName}"/>
   </LinearLayout>
</layout>
```

#### 数据对象
POJO对象或JavaBeans对象。
公有字段和拥有公有访问器的私有字段对于数据绑定框架来说是等价的。
``` java
public class User {
   public final String firstName;
   public final String lastName;
   public User(String firstName, String lastName) {
       this.firstName = firstName;
       this.lastName = lastName;
   }
}
```

#### 绑定数据
数据绑定框架默认会根据布局文件名生成数据绑定类，Pascal命名风格加Binding后缀。该类会持有layout属性（数据变量和视图），并且知道如何使用绑定表达式进行赋值。
``` java
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   MainActivityBinding binding = MainActivityBinding.inflate(getLayoutInflater());
   User user = new User("Test", "User");
   binding.setUser(user);
}
```

#### 事件处理
数据绑定支持表达式处理视图事件。一般情况下事件属性名称类似于监听器方法名称。比如View.setOnLongClickListener有个方法为onLongClick()，对应事件属性名为android:onLongClick。这里有两种方式处理事件。

##### 方法引用
要求表达式引用方法签名参数与监听器方法签名参数一致。如果表达式识别为方法引用，数据绑定框架包装该方法引用和拥有者对象到监听器中，设置监听器到目标视图。如果表达式识别为null，数据绑定框架不会创建监听器，而是设置空监听器。
在编译时处理方法绑定，如果方法不存在或签名不匹配，会收到编译错误。
监听器在数据绑定时创建，而不是事件触发时。
``` java
public class MyHandlers {
    public void onClickFriend(View view) { ... }
}

<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="handlers" type="com.example.MyHandlers"/>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"
           android:onClick="@{handlers::onClickFriend}"/>
   </LinearLayout>
</layout>
```

##### 监听器绑定
使用lambda表达式。数据绑定始终创建监听器，并设置给目标视图。允许任意数量的监听器参数，返回值类型必须匹配监听器方法（除非期望返回值为void）。只有作为根元素的监听器可以表示为lambda表达式。
监听器事件触发时创建。
Android Gradle Plugin2.0及以上版本启用。
``` java
public class Presenter {
    public void onSaveClick(Task task){}
}

<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
  <data>
      <variable name="task" type="com.android.example.Task" />
      <variable name="presenter" type="com.android.example.Presenter" />
  </data>
  <LinearLayout android:layout_width="match_parent" android:layout_height="match_parent">
      <Button android:layout_width="wrap_content" android:layout_height="wrap_content"
      android:onClick="@{() -> presenter.onSaveClick(task)}" />
  </LinearLayout>
</layout>
```

如果表达式因为null对象不能执行，需要返回时，数据绑定框架会返回类型默认值，如引用类型返回null，int类型返回0。
``` java
android:onClick="@{(v) -> v.isVisible() ? doSomething() : void}"
```

##### 避免复杂监听器
监听器表达式应保证可读性和可维护性，它们仅承担从UI层传递数据到回调方法的工作。
复杂的业务逻辑在回调方法中实现。
已经提供了一些专用的点击事件处理器。

|class|Listener Setter|Attribute|
|:-:|:-:|:-:|
|SearchView|setOnSearchClickListener(View.OnClickListener)|	android:onSearchClick|
|ZoomControls|setOnZoomInClickListener(View.OnClickListener)|	android:onZoomIn|
|ZoomControls|setOnZoomOutClickListener(View.OnClickListener)|	android:onZoomOut|

### 布局细节
#### 导入语句
默认导入java.lang.*包。若用到其他包的类，需要使用import语句。
使用别名解决同名不同包的类导入问题。
Android Studio不能处理布局中的导入语句，所以自动导入功能不可用。
支持静态字段和方法导入。
``` java
<import type="android.view.View"/>
<import type="com.example.real.estate.View"
        alias="Vista"/>
```

#### 变量
使用data标签的variable标签描述数据变量。
变量类型在编译时检查，如果变量实现了Observable或是observable collection类型，会进行提示。如果变量的类型或接口没有实现观察者相关接口，变量的变化将不会被观察到。
同一界面不同配置的不同布局文件中的数据变量会被合并，注意不要产生冲突问题。
生成的绑定类会拥有每一个变量的设置器和访问器，这些变量会获得与Java一样的默认值。
绑定类包含一个特殊的变量值context，值由根视图的getContext()方法获取。如果指定了同名的变量，将会重写context变量。
``` java
<data>
    <import type="android.graphics.drawable.Drawable"/>
    <variable name="user"  type="com.example.User"/>
    <variable name="image" type="Drawable"/>
    <variable name="note"  type="String"/>
</data>
```

#### 自定义绑定类名
绑定类的默认类名是根据布局文件生成的，所属包为应用包下的databinding模块包中。比如contact_item.xml，应用包名为com.example.my.app，则绑定类名为ContactItemBinding，所属包名为com.example.my.app.databinding。
使用class字段可自定义绑定类名及所在类名。可以是完整类名(指定包名)，也可以单个类名(包名不变)或使用.操作符加类名(引用包名)。
``` java
<data class=".ContactItem">
    ...
</data>
```

#### Include标签
支持数据绑定变量传入Include标签，但不支持merge标签下的Include标签。
``` java
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:bind="http://schemas.android.com/apk/res-auto">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <include layout="@layout/name"
           bind:user="@{user}"/>
       <include layout="@layout/contact"
           bind:user="@{user}"/>
   </LinearLayout>
</layout>
```

### 表达式
#### 通用特性
*	数学运算(+, -, /, \*, %)
*	字符串合并(+)
*	逻辑运算(&&, ||)
*	位运算(&, |, ^)
*	一元运算符(+, -, !, ~)
*	位移运算符(>>, >>>, <<)
*	比较运算符(==, >, <, >=, <=)
*	类型判断(instanceof)
*	分组运算符(())
*	三元运算符(>:)
*	字面量(字符、字符串、数字、null、true、false)
*	方法调用
*	类型转换
*	字段访问
*	数组访问

#### 不支持运算符
*	this
*	super
*	new
*	显式泛型调用

#### 空合并运算符
??运算符，不为空返回左侧表达式结果，为空返回右侧结果。
``` java
android:text="@{user.displayName ?? user.lastName}"
```

#### 属性引用
适用于公有字段，拥有公有访问器的私有字段和可观察字段。
``` java
android:text="@{user.lastName}"
```

#### 避免空指针
生成的数据绑定代码会执行空检查，避免空指针。比如，如果user为空，则user.name会分配默认值null。

#### 集合
使用[]可以便捷访问数组、列表、稀疏列表和图。
``` java
<data>
    <import type="android.util.SparseArray"/>
    <import type="java.util.Map"/>
    <import type="java.util.List"/>
    <variable name="list" type="List&lt;String&gt;"/>
    <variable name="sparse" type="SparseArray&lt;String&gt;"/>
    <variable name="map" type="Map&lt;String, String&gt;"/>
    <variable name="index" type="int"/>
    <variable name="key" type="String"/>
</data>
…
android:text="@{list[index]}"
…
android:text="@{sparse[index]}"
…
android:text="@{map[key]}"
```

#### 字符串字面量
``` java
android:text='@{map["firstName"]}'
android:text="@{map[`firstName`}"
android:text="@{map['firstName']}"
```

#### 资源文件
##### 字符串
``` java
android:text="@{@string/nameFormat(firstName, lastName)}"
```

##### 复数
``` java
  Have an orange
  Have %d oranges

android:text="@{@plurals/orange(orangeCount, orangeCount)}"
```

##### 其他
|Type|Normal Reference|Expression Reference|
|:-:|:-:|:-:|
|String[]|@array|@stringArray|
|int[]|@array|@intArray|
|TypedArray|@array|@typedArray|
|Animator|@animator|@animator|
|StateListAnimator|@animator|@stateListAnimator|
|color int|@color|@color|
|ColorStateList|@color|@colorStateList|

### 数据对象细节
POJO或JavaBean对象修改不会引发UI更新。这里有三种数据变化通知机制。
#### 可观察对象
实现Observable接口，来给绑定对象添加一个监听器，用来监听对象属性变化。
该接口拥有添加和移除监听器的方法，但通知取决于开发者。为了便于开发，提供了BaseObservable，可以实现监听器注册机制。还是由开发决定何时通知数据改变。
使用@Bindable绑定访问器，在设置器中调用通知方法。
``` java
private static class User extends BaseObservable {
   private String firstName;
   private String lastName;
   @Bindable
   public String getFirstName() {
       return this.firstName;
   }
   @Bindable
   public String getLastName() {
       return this.lastName;
   }
   public void setFirstName(String firstName) {
       this.firstName = firstName;
       notifyPropertyChanged(BR.firstName);
   }
   public void setLastName(String lastName) {
       this.lastName = lastName;
       notifyPropertyChanged(BR.lastName);
   }
}
```
@Bindable注解会在编译期间在BR类中生成访问入口。BR会在模块/应用包中生成。如果无法继承BaseObservable类，可使用PropertyChangeRegistry类高效地存储和通知监听器。

#### 可观察字段
如果想节省时间或只有少量可观察属性，可使用ObservableField和它的兄弟类(ObservableBoolean, ObservableByte, ObservableChar, ObservableShort, ObservableInt, ObservableLong, ObservableFloat, ObservableDouble,  ObservableParcelable)。ObservableFields是自包含可观察对象，拥有单个字段。
基础类型访问时可避免装箱开箱操作。

``` java
private static class User {
   public final ObservableField<String> firstName =
       new ObservableField<>();
   public final ObservableField<String> lastName =
       new ObservableField<>();
   public final ObservableInt age = new ObservableInt();
}

user.firstName.set("Google");
int age = user.age.get();
```

#### 可观察集合
可观察集合允许键名访问数据对象。

##### ObservableArrayMap
``` java
ObservableArrayMap<String, Object> user = new ObservableArrayMap<>();
user.put("firstName", "Google");
user.put("lastName", "Inc.");
user.put("age", 17);

<data>
    <import type="android.databinding.ObservableMap"/>
    <variable name="user" type="ObservableMap&lt;String, Object&gt;"/>
</data>
…
<TextView
   android:text='@{user["lastName"]}'
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
<TextView
   android:text='@{String.valueOf(1 + (Integer)user["age"])}'
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

##### ObservableArrayList
``` java
ObservableArrayList<Object> user = new ObservableArrayList<>();
user.add("Google");
user.add("Inc.");
user.add(17);

<data>
    <import type="android.databinding.ObservableList"/>
    <import type="com.example.my.app.Fields"/>
    <variable name="user" type="ObservableList&lt;Object&gt;"/>
</data>
…
<TextView
   android:text='@{user[Fields.LAST_NAME]}'
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
<TextView
   android:text='@{String.valueOf(1 + (Integer)user[Fields.AGE])}'
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

### 生成绑定
生成的绑定类连接布局中的变量与视图。类名和包名都可以自定义。所有的绑定类都继承ViewDataBinding。
#### 创建方法
绑定类在布局填充后即刻执行，保证视图树不被视图与表达式绑定干扰。
##### 绑定类负责填充和绑定
``` java
MyLayoutBinding binding = MyLayoutBinding.inflate(layoutInflater);
MyLayoutBinding binding = MyLayoutBinding.inflate(layoutInflater, viewGroup, false);
```

##### 绑定类仅绑定
``` java
MyLayoutBinding binding = MyLayoutBinding.bind(viewRoot);
```

##### 绑定类不能预知
``` java
ViewDataBinding binding = DataBindingUtil.inflate(LayoutInflater, layoutId,
    parent, attachToParent);
ViewDataBinding binding = DataBindingUtil.bindTo(viewRoot, layoutId);
```

#### 有ID的视图
有ID的视图会在绑定类中生成公有不变字段，可用ID获取对应视图，比findViewById更高效。ID不是数据绑定的必须要求，但某些实例需要用代码访问视图。
``` java
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"
   android:id="@+id/firstName"/>
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.lastName}"
  android:id="@+id/lastName"/>
   </LinearLayout>
</layout>

public final TextView firstName;
public final TextView lastName;
```

#### 变量
每个变量都在绑定类中生成访问器和设置器。
``` java
<data>
    <import type="android.graphics.drawable.Drawable"/>
    <variable name="user"  type="com.example.User"/>
    <variable name="image" type="Drawable"/>
    <variable name="note"  type="String"/>
</data>

public abstract com.example.User getUser();
public abstract void setUser(com.example.User user);
public abstract Drawable getImage();
public abstract void setImage(Drawable image);
public abstract String getNote();
public abstract void setNote(String note);
```

#### 视图存根
因为ViewStub不同于普通视图，它初始时是不可见的，显式填充后，会使用填充后的布局进行替换。因为初始不可见，绑定的数据或集合也是不可见的，而填充后的视图是不变的。这里用ViewStubProxy代替ViewStub，允许开发者访问ViewStub，也可访问填充后的视图。
当视图填充后，数据要求绑定到填充后的新视图上。因此ViewStubProxy必须监听ViewStub's ViewStub.OnInflateListener来建立新的绑定关系。因为视图仅允填充一次，ViewStubProxy允许开发者设置OnInflateListener，该监听器会在绑定关系建立后调用。

#### 高级绑定
##### 动态变量
有时，特定的绑定不能提前预知。比如RecyclerView.Adapter负责多个布局时就不知具体的绑定类，它要在onBindViewHolder(VH, int)方法内分配绑定值。
``` java
public void onBindViewHolder(BindingHolder holder, int position) {
   final T item = mItems.get(position);
   holder.getBinding().setVariable(BR.item, item);
   holder.getBinding().executePendingBindings();
}
```

##### 即时绑定
当变量或观察对象变化后，在下一帧到来前并不会更新。如果遇到需要即时更新情况，调用executePendingBindings()强制更新。

##### 后台线程
只要不是集合，就可以在后台更改数据模型。数据绑定执行表达式时会本地化每个变量/字段以避免并发问题。

### 属性设置
当绑定数据变化时，生成的绑定类会调用绑定表达式关联的属性设置方法。数据绑定框架允许调用自定义的设置方法。

#### 自动设置方法
对于属性，绑定类会查找属性的setter方法。无关属性的命名空间，仅仅是属性名称。比如一个绑定表达式关联android:text属性，会查找setText(String)方法。如果表达式返回int类型，则会查找setText(int)方法。
注意要让表达式返回正确的类型，必要时进行类型转换。当不存在指定属性时，数据绑定也能生效。使用数据绑定可以方便地"创建"属性。比如DrawerLayout不存在任何属性，但拥有大量的setter方法，也可以使用自动设置方法。
``` java
<android.support.v4.widget.DrawerLayout
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:scrimColor="@{@color/scrim}"
    app:drawerListener="@{fragment.drawerListener}"/>
```

##### 重命名设置方法
一些属性的设置方法与属性名并不匹配。对于这些方法，可以使用@BindingMethods注解绑定到属性名。这要求控件类中必须包含@BindingMethod注解的重命名方法。比如android:tint属性已经与setImageTintList(ColorStateList)关联，而不是setTint。
开发者无需重命名设置方法，安卓框架属性已经实现了重命名方法。
``` java
@BindingMethods({
       @BindingMethod(type = "android.widget.ImageView",
                      attribute = "android:tint",
                      method = "setImageTintList"),
})
```

##### 自定义设置方法
###### 没有提供setter方法的属性
一些属性需要自定义绑定逻辑。比如android:paddingLeft没有相应的设置方法，只有setPadding(left, top, right, bottom)方法。可通过@BindingAdapter注解静态方法实现自定义设置方法绑定。
``` java
@BindingAdapter("android:paddingLeft")
public static void setPaddingLeft(View view, int padding) {
   view.setPadding(padding,
                   view.getPaddingTop(),
                   view.getPaddingRight(),
                   view.getPaddingBottom());
}
```

###### 接收多个属性参数
绑定适配器有助于类型自定义。比如自定义加载器可以离线加载图片。
开发者创建的绑定适配器可以重写默认的数据绑定适配器。
可让适配器接收多个属性参数。
``` java
@BindingAdapter({"bind:imageUrl", "bind:error"})
public static void loadImage(ImageView view, String url, Drawable error) {
   Picasso.with(view.getContext()).load(url).error(error).into(view);
}

<ImageView app:imageUrl="@{venue.imageUrl}"
app:error="@{@drawable/venueError}"/>
```
自定义命名空间匹配时会被忽略，也可编写安卓命名空间的适配器。
绑定适配器有时可能带有旧值。接收旧值的方法，必须旧值在前，新值在后。
``` java
@BindingAdapter("android:paddingLeft")
public static void setPaddingLeft(View view, int oldPadding, int newPadding) {
   if (oldPadding != newPadding) {
       view.setPadding(newPadding,
                       view.getPaddingTop(),
                       view.getPaddingRight(),
                       view.getPaddingBottom());
   }
}
```

###### 事件处理方法要求
事件处理器只能是只有一个抽象方法的接口或抽象类。比如：
``` java
@BindingAdapter("android:onLayoutChange")
public static void setOnLayoutChangeListener(View view, View.OnLayoutChangeListener oldValue,
       View.OnLayoutChangeListener newValue) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
        if (oldValue != null) {
            view.removeOnLayoutChangeListener(oldValue);
        }
        if (newValue != null) {
            view.addOnLayoutChangeListener(newValue);
        }
    }
}
```

###### 拥有两个及以上方法的监听器
当一个监听器拥有多个方法时，必须分割成多个监听器。比如View.OnAttachStateChangeListener拥有两个方法onViewAttachedToWindow() and onViewDetachedFromWindow()。因此必须创建两个接口来区分属性并处理事件。
由于改变其中一个会对两个都造成影响，所以必须提供三个绑定适配方法。
``` java
@TargetApi(VERSION_CODES.HONEYCOMB_MR1)
public interface OnViewDetachedFromWindow {
    void onViewDetachedFromWindow(View v);
}

@TargetApi(VERSION_CODES.HONEYCOMB_MR1)
public interface OnViewAttachedToWindow {
    void onViewAttachedToWindow(View v);
}

@BindingAdapter("android:onViewAttachedToWindow")
public static void setListener(View view, OnViewAttachedToWindow attached) {
    setListener(view, null, attached);
}

@BindingAdapter("android:onViewDetachedFromWindow")
public static void setListener(View view, OnViewDetachedFromWindow detached) {
    setListener(view, detached, null);
}

@BindingAdapter({"android:onViewDetachedFromWindow", "android:onViewAttachedToWindow"})
public static void setListener(View view, final OnViewDetachedFromWindow detach,
        final OnViewAttachedToWindow attach) {
    if (VERSION.SDK_INT >= VERSION_CODES.HONEYCOMB_MR1) {
        final OnAttachStateChangeListener newListener;
        if (detach == null && attach == null) {
            newListener = null;
        } else {
            newListener = new OnAttachStateChangeListener() {
                @Override
                public void onViewAttachedToWindow(View v) {
                    if (attach != null) {
                        attach.onViewAttachedToWindow(v);
                    }
                }

                @Override
                public void onViewDetachedFromWindow(View v) {
                    if (detach != null) {
                        detach.onViewDetachedFromWindow(v);
                    }
                }
            };
        }
        final OnAttachStateChangeListener oldListener = ListenerUtil.trackListener(view,
                newListener, R.id.onAttachStateChangeListener);
        if (oldListener != null) {
            view.removeOnAttachStateChangeListener(oldListener);
        }
        if (newListener != null) {
            view.addOnAttachStateChangeListener(newListener);
        }
    }
}
```
ListenerUtil用来跟踪前一个监听器，便于移除操作。

### 转换器
#### 对象转换器
当绑定表达式返回数据时，数据绑定框架会查找自动、重命名、自定义设置方法。如果只存在一个可用方法时，数据类型会自动转换，否则需要在表达式中手动转换。
``` java
<TextView
   android:text='@{userMap["lastName"]}'
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

#### 自定义
有时特定类型之间需要自动转换。比如设置背景时，期望类型为Drawable类型，但表达式返回的是int类型。这时就需要@BindConversion进行转换。
``` java
<View
   android:background="@{isError ? @color/red : @color/white}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>

@BindingConversion
public static ColorDrawable convertColorToDrawable(int color) {
   return new ColorDrawable(color);
}
```
**注意**：
转换发生在setter层，因此不允许混合类型。
``` java
<!- ERROR -!>
<View
   android:background="@{isError ? @drawable/error : @color/white}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

#### Android Studio支持
Android studio为绑定表达式提供了如下支持：
*	语法高亮
*	错误标识
*	XML代码自动补全
*	引用，包含导航（比如导航到定义）和快捷文档
注意：数组和泛型（比如Observable），有时会在没有错误时显示错误。

预览面板可以显示绑定表达式提供的默认值。如果想在设计阶段显示默认值，也可使用tools属性取代绑定表达式的默认值。
``` java
<TextView android:layout_width="wrap_content"
   android:layout_height="wrap_content"
   android:text="@{user.firstName, default=PLACEHOLDER}"/>
```