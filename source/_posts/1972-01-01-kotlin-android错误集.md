---
title: kotlin-android错误集
comments: false
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