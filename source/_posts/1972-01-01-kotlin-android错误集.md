---
comments: true
title: kotlin-android错误集
toc: true
date: 1972-01-01 08:00:00
tags:
	- kotlin
	- errors
---

### app: 'androidProcessor' dependencies won't be recognized as kapt annotation processors.
**错误信息**：
```
app: 'androidProcessor' dependencies won't be recognized as kapt annotation processors. Please change the configuration name to 'kapt' for these artifacts: 'com.android.databinding:compiler:3.0.0'.
```

**运行环境**：

*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.60

**问题原因**：

*	kotlin下要使用kapt，而不是apt
*	kotlin version与gradle plugin版本不匹配。

**解决方案**：

*	使用kapt替换annotationProcessor
``` gradle
kapt deps.databinding.compiler
```

*	kotlin版本更换为1.1.2-2
databinding的apt与Dagger，Room的不一样，是通过android中配置的。
不更换版本也可以，只是Gradle console会出现错误提示而已。
```
android {
	...
    dataBinding {
        enabled = true
    }
}
```

<!-- more -->

### Android Room : Each bind variable in the query must have a matching method
**错误信息**：
```
Error:(12, 1) 错误: Each bind variable in the query must have a matching method parameter. Cannot find method parameters for :login.
```

**运行环境**：

*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.2-2

**问题原因**：

*	kotlin插件问题，生成将参数命名为了p0
该问题1.1.60版本已修复。

**解决方案**：

*	更换@Query绑定的参数名称
``` kotlin
    @Query("SELECT * FROM user WHERE login = :p0")
    fun findByLogin(login: String) : LiveData<User>
```

### Fragment...cannot be provided without an @Provides-annotated method
**错误信息**：
```
Error:(5, 1) 错误: [dagger.android.AndroidInjector.inject(T)] java.util.Map<java.lang.Class<? extends android.support.v4.app.Fragment>,javax.inject.Provider<dagger.android.AndroidInjector.Factory<? extends android.support.v4.app.Fragment>>> cannot be provided without an @Provides-annotated method.
```

**运行环境**：

*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.2-2

**问题原因**：

*	为没有Fragment的MainActivity，实现了HasFragmentInjector/HasSupportFragmentInjector接口，增加了无modules的MainActivityModule类。

**解决方案**：

*	在AppComponent中去除MainActivityModule。

### cannot be provided without an @Provides-annotated method.
**错误信息**：
```
Error:(6, 1) 错误: [dagger.android.AndroidInjector.inject(T)] java.util.Map<java.lang.Class<? extends android.arch.lifecycle.ViewModel>,? extends javax.inject.Provider<android.arch.lifecycle.ViewModel>> cannot be provided without an @Provides-annotated method.
```

**运行环境**：
*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.60

**问题原因**：
通配符导致的问题。@IntoMap生成Map中是Provider，而不是? extend Provider，但dagger查找时是查找? extend Provider。

**解决方案**：
标记注入点时添加@JvmSuppressWildcards。
``` java
@Singleton
class GithubViewModelFactory @Inject
constructor(private val creators: Map<Class<out ViewModel>, @JvmSuppressWildcards Provider<ViewModel>>) : ViewModelProvider.Factory {
	...
}
```

### The @Rule 'instantTaskExecutorRule' must be public
**错误信息**：
```
org.junit.internal.runners.rules.ValidationError: The @Rule 'instantTaskExecutorRule' must be public.
```

**运行环境**：
*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.60

**问题原因**：
JUnit不能识别@Rule标识的属性。

**解决方案**：
注解属性的getter访问器。
``` kotlin
@get:Rule
public val mActivityRule: ActivityTestRule<MainActivity> = ActivityTestRule(javaClass<MainActivity>())
```

**参考资料**：
[kotlin-and-new-activitytestrule-the-rule-must-be-public](https://stackoverflow.com/questions/29945087/kotlin-and-new-activitytestrule-the-rule-must-be-public)

### No public static parameters method on class XXXTest
**错误信息**：
```
java.lang.Exception: No public static parameters method on class com.example.hnclcaa.githubapp.repository.NetworkBoundResourceTest
```

**运行环境**：
*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.60

**问题原因**：
JUnit不能识别companion object中的静态方法。

**解决方案**：
使用@JvmStatic注解。
``` kotlin
@RunWith(Parameterized::class)
class NetworkBoundResourceTest(val useRealExecutors: Boolean) {

    companion object  {
        @JvmStatic
        @Parameterized.Parameters
        fun param() = arrayListOf(true, false)
    }
}
```

### Class not found: "XXXTest"Empty test suite
**错误信息**：
```
Class not found: "com.example.hnclcaa.githubapp.ExampleInstrumentedTest"Empty test suite
```

**运行环境**：
*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.60

**问题原因**：
将AndroidTest类以本地测试方式执行。

**解决方案**：
第一次运行Test类时会选择本地还是设备测试，之后默认以第一次选择的方式运行Test类。此时需要通过Run -> Run命令切换运行方式。

### Expression in a class literal has a nullable type 'T'
**错误信息**：
```
Expression in a class literal has a nullable type 'T', use !! to make the type non-nullable
```

**运行环境**：
*	Android Studio 3.0
*	Android Gradle Plugin 3.0.0
*	kotlin version 1.1.60

**问题原因**：
泛型不匹配导致，一个是&lt;T&gt;，另一个是&lt;out T&gt;

**解决方案**：
检查范型。

**知识点**：
clazz::class返回类型是KClass&lt;out T&gt;，而clazz.javaClass.kotlin返回类型是KClass&lt;T&gt;。

### MockitoException:Cannot mock/spy class
**错误信息**：
```
org.mockito.exceptions.base.MockitoException:
Cannot mock/spy class com.example.hnclcaa.githubapp.ui.common.navigators.NavigatorController
Mockito cannot mock/spy because :
- final or anonymous class
```

**问题原因**：
mock的对象是不可继承类或匿名类。

**解决方案**：
kotlin默认类为不可继承类。添加open修饰符。

### MockitoException:Cannot mock/spy class
**错误信息**：
```
org.mockito.exceptions.base.MockitoException:
Cannot mock/spy class com.example.hnclcaa.githubapp.ui.common.navigators.NavigatorController
Mockito cannot mock/spy because :
- final or anonymous class
```

**问题原因**：
mock的对象是不可继承类或匿名类。

**解决方案**：
kotlin默认类为不可继承类。添加open修饰符。

### java.lang.NullPointerException on Mockito
**错误信息**：
```
Caused by: java.lang.NullPointerException: Attempt to invoke virtual method 'java.lang.Object android.arch.lifecycle.MutableLiveData.getValue()' on a null object reference
at com.example.hnclcaa.githubapp.ui.search.SearchViewModel.setQuery(SearchViewModel.kt:42)
at com.example.hnclcaa.githubapp.ui.search.SearchFragment.doResearch(SearchFragment.kt:144)
at com.example.hnclcaa.githubapp.ui.search.SearchFragment.access$doResearch(SearchFragment.kt:37)
at com.example.hnclcaa.githubapp.ui.search.SearchFragment$initSearchInputListener$2.onKey(SearchFragment.kt:132)
at android.view.View.dispatchKeyEvent(View.java:9230)
...
```

**问题原因**：
mock或verify的方法是不可继承或私有方法。

**解决方案**：
添加open修饰符。

### org.mockito.exceptions.misusing.MissingMethodInvocationException
**错误信息**：
```
org.mockito.exceptions.misusing.MissingMethodInvocationException:
when() requires an argument which has to be 'a method call on a mock'.
For example:
when(mock.getArticles()).thenReturn(articles);

Also, this error might show up because:
1. you stub either of: final/private/equals()/hashCode() methods.
Those methods *cannot* be stubbed/verified.
Mocking methods declared on non-public parent classes is not supported.
2. inside when() you don't call method on mock but on some other object.
```

**问题原因**：
when传入参数只能是方法以外的成员对象。

**解决方案**：
使用doReturn/when句式。
``` kotlin
doReturn(loadMoreStatus).`when`(viewModel).getLoadMoreStatus()
```
