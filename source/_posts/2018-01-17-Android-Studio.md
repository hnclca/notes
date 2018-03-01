---
title: Android Studio
comments: true
toc: true
date: 2018-01-17 16:15:40
tags:
	- android
	- IDE
---

[android studio](https://developer.android.google.cn/studio/intro/index.html)功能简介

<!-- more -->

### 配置文件
#### studio(64).vmoptions
配置Java虚拟机，如堆内存和缓存大小。
可使用STUDIO_VM_OPTIONS变量替换文件。
使用Help &gt; Edit Custom VM options打开或创建自定义vmoptions配置文件。
修改配置后，重启生效。

##### Xmx
最大堆内存。
``` shell
// 查看JVM工具列表
$ jps -lvm
```

#### idea.properties
配置插件安装路径及支持的最大文件大小。
可使用STUDIO_PROPERTIES变量替换文件。
使用Help &gt; Edit Custom Properties打开或创建自定义vmoptions配置文件。
修改配置后，重启生效。

#### JDK
内置OpenJDK。通过(Project Structure &gt; JDK location &gt; ...embedded)

##### 模块build.gradle中配置
``` gradle
android {
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_6
        targetCompatibility JavaVersion.VERSION_1_6
    }
}
```

#### 代理
##### Setting
入口Appearance & Behavior &gt; System Settings &gt; HTTP Proxy。
提供自动和手动两种配置方式。

##### 模块build.gradle中配置
``` gradle
android {
    ...

    defaultConfig {
        ...
        systemProp.http.proxyHost=proxy.company.com
        systemProp.http.proxyPort=443
        systemProp.http.proxyUser=userid
        systemProp.http.proxyPassword=password
        systemProp.http.auth.ntlm.domain=domain
    }
    ...
}
```

##### 项目gradle.properties配置
``` gradle
# Project-wide Gradle settings.
...

systemProp.http.proxyHost=proxy.company.com
systemProp.http.proxyPort=443
systemProp.http.proxyUser=username
systemProp.http.proxyPassword=password
systemProp.http.auth.ntlm.domain=domain

systemProp.https.proxyHost=proxy.company.com
systemProp.https.proxyPort=443
systemProp.https.proxyUser=username
systemProp.https.proxyPassword=password
```

#### 低内存配置原则
1. 减小最大堆内存
2. 更新Gradle和适用于Gradle的Android插件(性能改进？)
3. 启用节能模式(File &gt; Power Save Mode)
4. 信用不必要的Lint检查(Setting &gt; Editor &gt; Inspections)
5. 物理设备取代虚拟设备调试
6. 仅将必要的Google Play服务作为依赖项
7. 打开Gradle的离线模式
8. 不要启用并行编译(Setting &gt; Build... &gt; Compiler &gt; ...parallel)

### 项目结构
#### 模块
每个项目包含一个或多个包含源代码文件和资源文件的模块。

*	Android应用模块
*	库模块
*	Google App引擎模块

##### 库模块注意事项
1. 资源合并冲突（资源ID）
2. 可以包含JAR库
3. 可以依赖外部库（uses-library）
4. 不能包含原始资源文件（assets）
5. 应用模块minSdkVersion必须大于或等于库定义的版本
6. 会创建R类（软件包名称）
7. 可包含ProGuard配置文件（consumerProguardFiles）

##### AAR文件结构
* AndroidManifest.xml
* classes.jar
* res
* R.txt
* assets
* libs/name.jar
* jni/abi name/name.so
* proguard.txt
* lint.jar

#### 项目文件
##### Android视图(默认)
Project面板中启用Android视图。

module
|--manifests
|--java
|--jniLibs
|--cpp(C/C++)
|--res
Gradle Scripts
External Build Files(C/C++)

##### Project视图(默认)
```
module
|--build
|--libs
|--src
	|--androidTest
    |--main
    	|--AndroidManifest.xml
        |--java
        |--jni
        |--gen
        |--res
        |--assets
    |--test
|--build.gradle
|--CMakeLists.txt(C/C++)
build.gradle
...
```

#### 项目结构
通过File &gt; Project Structure查看。
##### SDK Location
配置项目使用的JDK、SDK、NDK。

##### Project
配置Gradle及Gradle插件。

##### Developer Services
谷歌或第三方的Studio附加组件。

##### Modules
模块配置。比如目标和最低SDK版本、应用签名、库依赖。

#### 语言支持
##### kotlin(3.0)

##### C/C++(2.2)
需要下载NDK工具包、CMake（构建工具）、LLDB（调试器）。
源代码目录cpp，头文件后缀名.h，实现文件名.c。
ndk-build使用Android.mk构建脚本。
CMake使用CMakeLists.txt构建脚本。

###### CMakeLists.txt

* 指定构建工具最低版本
```
cmake_minimum_required(VERSION 3.4.1)
```

* 指定构建的原生库名、类型、源文件路径
类型有SHARED和STATIC（静态库）
```
add_library( # Specifies the name of the library.
             native-lib

             # Sets the library as a shared library.
             SHARED

             # Provides a relative path to your source file(s).
             src/main/cpp/native-lib.cpp )
```

* 指定头文件位置
```
# Specifies a path to native header files.
include_directories(src/main/cpp/include/)
```

* 添加预构建库
由于库预先构建，使用IMPORTED标识通知导入。
```
add_library( imported-lib
             SHARED
             IMPORTED )
```

* 指定预构建二进制库位置
```
set_target_properties( # Specifies the target library.
                       imported-lib

                       # Specifies the parameter you want to define.
                       PROPERTIES IMPORTED_LOCATION

                       # Provides the path to the library you want to import.
                       imported-lib/src/${ANDROID_ABI}/libimported-lib.so )
```

* 指定预构建库头文件路径
```
target_include_directories(hello-libs PRIVATE
	${distribution_DIR}/gmath/include
    ${distribution_DIR}/gperf/include)
```

* 指定添加的NDK API库
```
find_library( # Defines the name of the path variable that stores the
              # location of the NDK library.
              log-lib

              # Specifies the name of the NDK library that
              # CMake needs to locate.
              log )
```

* 指定库之间的关联关系
```
# Links your native library against one or more other native libraries.
target_link_libraries( # Specifies the target library.
                       native-lib

                       # Links the log library to the target library.
                       ${log-lib} )
```

* 启用汇编语言
```
enable_language(ASM_NASM)
```

复杂点的构建脚本
```
file(GLOB_RECURSE SRC_FILES FOLLOW_SYMLINKS
    ../../../native/PushUpPal/src/*.cpp
    ../../../native/PushUpPal/glue-code/jni/*.cpp
    ../../../deps/djinni/support-lib/*.cpp)

include_directories(native/PushUpPal/src
                    native/PushUpPal/glue-code/interfaces/generated
                    native/PushUpPal/glue-code/jni
                    deps/djinni/support-lib
                    deps/djinni/support-lib/jni
                    deps/OpenCV-android-sdk/sdk/native/jni/include)

add_library(native-pushuppal SHARED ${SRC_FILES})

add_library(lib-opencv SHARED IMPORTED)
set_target_properties(lib-opencv PROPERTIES IMPORTED_LOCATION
                      ${CMAKE_SOURCE_DIR}/src/main/jniLibs/${ANDROID_ABI}/
                          libopencv_java3.so)

target_link_libraries(native-pushuppal
                      lib-opencv)
```

###### Gradle配置
[CMake 参数](https://developer.android.google.cn/ndk/guides/cmake.html#variables)

* 指定构建脚本
``` gradle
android {
  ...
  // Encapsulates your external native build configurations.
  externalNativeBuild {

    // Encapsulates your CMake build configurations.
    cmake {

      // Provides a relative path to your CMake build script.
      path "CMakeLists.txt"
    }
  }
}
```

* 指定可选参数和标志
``` gradle
android {
  ...
  defaultConfig {
    ...
    // This block is different from the one you use to link Gradle
    // to your CMake or ndk-build script.
    externalNativeBuild {

      // For ndk-build, instead use ndkBuild {}
      cmake {

        // Passes optional arguments to CMake.
        arguments "-DANDROID_ARM_NEON=TRUE", "-DANDROID_TOOLCHAIN=clang"

        // Sets optional flags for the C compiler.
        cFlags "-D_EXAMPLE_C_FLAG1", "-D_EXAMPLE_C_FLAG2"

        // Sets a flag to enable format macro constants for the C++ compiler.
        cppFlags "-D__STDC_FORMAT_MACROS"
      }
    }
  }

  buildTypes {...}

  productFlavors {
    ...
    demo {
      ...
      externalNativeBuild {
        cmake {
          ...
          // Specifies which native libraries to build and package for this
          // product flavor. If you don't configure this property, Gradle
          // builds and packages all shared object libraries that you define
          // in your CMake or ndk-build project.
          targets "native-lib-demo"
        }
      }
    }

    paid {
      ...
      externalNativeBuild {
        cmake {
          ...
          targets "native-lib-paid"
        }
      }
    }
  }
}
```

### 编码
#### 文件模板
基于VTL(Velocity Template Language)脚本语言。

##### 模板变量
创建类时对话框输入参数对应的变量。这里以java类举例。

* NANE
类名

* IMPORT_BLOCK
行分隔的导入语句或""。

* VISIBLITY
public还是private

* SUPERCLASS
* INTERFACES
* ABSTRACT
* FINAL

##### 使用句式

* 变量引用 -- ${NAME}
* if表达式 -- #if (...) ... #end
* 解析文件头 -- #parse("File Header.java")
文件头定义在includes中。

#### 备选资源
##### 资源限定符
提供特定设备配置的备用资源目录。
存在多个限定符时，必须按指定顺序。否则将被忽略。
[配置限定符](https://developer.android.google.cn/guide/topics/resources/providing-resources.html#AlternativeResources)

##### 资源别名
提供单个备选资源（即在主资源文件名后添加限定符后缀，如icon_ca.png），无需放入备用资源目录，只需在备用资源目录的资源文件中引用。
布局别名，使用merge &gt; include元素引用别名文件。
**注意**：
并非所有资源都可使用别名，特别是xml目录中的动画、菜单、原始资源及其他未指定资源。

#### 矢量资源库
Vector Asset Studio支持将SVG、PSD的矢量资源转换为XML格式，取代光栅格式。
矢量图适用于图标内资源，因为首次加载占用较多内存，推荐最大尺寸为200x200dp。
启动图标更适合用光栅图像。
只能在主线程访问矢量图资源。
Android 4.4(API 20)及以下不支持矢量图，会生成相应的PNG图片。要求Gradle Plugin 1.5，PSD格式要求2.2。矢量动画(AnimatedVectorDrawable)支持Android 5.0(API 21)及以上版本。
支持库(23.2或更高)兼容Android 2.1(API 7)。使用app:srcCompat属性和VectorDrawableCompat类。要求Gradle Plugin 2.0。矢量动画兼容Android 3.0(API 11)。
``` gradle
android {
  defaultConfig {
    vectorDrawables.useSupportLibrary = true
  }
}

dependencies {
  compile 'com.android.support:appcompat-v7:23.2.0'
}
```

#### 图标资源库
创建不同密度的图标。简化图标设计和导入过程。包含调整图标和添加背景的工具。
启用图标、操作栏及标签图标、通知图标、剪贴画、图像（PNG&gt;JPG&gt;&gt;GIF）、文本字符串。

#### WebP图像互转
Android 4.0(API 14)支持有损压缩，Android 4.3(API 18)起支持无损压缩和透明度。
可将PNG、JPG、BMP、静态GIF转换为WebP格式。
右键点击图像或包含图像资源的文件夹，点击[Convert to WebP]或[Convert to PNG]
9Patch图片不可转换，该工具会自动跳过。

#### 布局编辑器
用于拖动创建布局。推荐ConstraintLayout布局。

#### 主题编辑器
可视化方式调整主题配色。可为特定设备配置。
使用Tools &gt; Android &gt; Theme Editor访问。
打开的样式XML文件中，点击文件窗口右上方的Open editor。

#### 翻译编辑器
打开strings.xml文件，点击右上角的Open editor。
管理字符串本地化，在预览界面中可选择指定语言进行测试。

#### 安卓App链接
[Android App Links](https://developer.android.google.cn/studio/write/app-link-indexing.html#handling)
配置打开app的网址规则。通过Tools &gt; App Links Assitant快捷配置。
要求Android 6.0(API 23)及以上版本。

##### 添加意图过滤器
指定网址匹配规则。提供path、pathPrefix、pathPattern三种方式。
``` xml
<intent-filter>
    <action android:name="android.intent.action.VIEW" />

    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />

    <data
        android:scheme="http"
        android:host="192.168.0.128"
        android:port="8080"
        android:pathPrefix="/githubapp" />
</intent-filter>
```

##### 处理接收到的意图
``` java
// ATTENTION: This was auto-generated to handle app links.
Intent appLinkIntent = getIntent();
String appLinkAction = appLinkIntent.getAction();
Uri appLinkData = appLinkIntent.getData();
```

##### 配置服务器链接文件
App链接助手生成数字资源链接文件，配置在应用清单、服务器应用根目录.well-known目录中。

###### 应用清单
```
// asset_statements
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "web",
    "site": "http://192.168.0.128:8080",
  }
}]

<meta-data
    android:name="asset_statements"
    android:resource="@string/asset_statements" />
<intent-filter android:autoVerify="true">
	...
</intent-filter>
```

###### 服务器应用.well-known
```
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example.hnclcaa.githubapp",
    "sha256_cert_fingerprints":
    ["2C:C3:8B:FA:23:D2:86:15:B4:40:B3:89:AB:84:7F:2D:1C:87:99:38:51:67:89:F2:8E:55:C2:C4:6C:98:0D:33"]
  }
}]
```

#### Lint
静态代码扫描工具。

##### 配置
###### 检查项目及项目级别
配置在应用根目录的lint.xml文件中。
```
<?xml version="1.0" encoding="UTF-8"?>
<lint>
    <!-- Disable the given check in this project -->
    <issue id="IconMissingDensityFolder" severity="ignore" />

    <!-- Ignore the ObsoleteLayoutParam issue in the specified files -->
    <issue id="ObsoleteLayoutParam">
        <ignore path="res/layout/activation.xml" />
        <ignore path="res/layout-xlarge/activation.xml" />
    </issue>

    <!-- Ignore the UselessLeaf issue in the specified file -->
    <issue id="UselessLeaf">
        <ignore path="res/layout/main.xml" />
    </issue>

    <!-- Change the severity of hardcoded strings to "error" -->
    <issue id="HardcodedText" severity="error" />
</lint>
```

###### 注解与属性
```
@SuppressLint("ParserError")
tools:ignore="NewApi,StringFormatInvalid"
tools:ignore="all"
```

###### gradle脚本
```
android {
  ...
  lintOptions {
    // Turns off checks for the issue IDs you specify.
    disable 'TypographyFractions','TypographyQuotes'
    // Turns on checks for the issue IDs you specify. These checks are in
    // addition to the default lint checks.
    enable 'RtlHardcoded','RtlCompat', 'RtlEnabled'
    // To enable checks for only a subset of issue IDs and ignore all others,
    // list the issue IDs with the 'check' property instead. This property overrides
    // any issue IDs you enable or disable using the properties above.
    check 'NewApi', 'InlinedApi'
    // If set to true, turns off analysis progress reporting by lint.
    quiet true
    // if set to true (default), stops the build if errors are found.
    abortOnError false
    // if true, only report errors.
    ignoreWarnings true
  }
}
...
```

##### 运行
###### 命令行
命令格式lint [flags] &lt;project directory&gt;
```
$ lint --list
$ lint --check MissingPrefix myproject
```

###### 组合gradle命令
对于gradle project项目，要求使用gradlew :lint执行命令。
有些命令在gradlew下不支持。比如--list, --check
```
$ gradlew :lint
$ gradlew :lintDebug
$ gradlew :lint --help
```

###### 菜单栏
Analyze &gt; Inspect Code
可以自定义扫描范围和检查项目及严重级别。

#### 注解支持库
Android Studio代码检查包含注解验证与Lint检查。但注解冲突只会生成警告不会中止编译。
##### 依赖库
在库模块中使用注解库，注解将作为AAR一部分以XML格式添加到annotations.zip文件中。
appcompat库中默认包含注解库，无需额外添加。
``` gradle
compile 'com.android.support:support-annotations:24.2.0'
```

##### 注解类型
###### Nullness
可空与不可空注解。
注意Android Studio Lint仅查找Android null注解。而Analyze &gt; Infer Nullity功能会自动插入@Nullable和@NonNull注解，这些注解来自IntelliJ，不被Lint支持。

* @Nullable
* @NonNull

###### 资源
所有资源使用int型资源Id进行标识，资源注解协助代码检查工具识别正确的资源类型。

* @StringRes
* @DrawableRes
* @ColorRes
* @ColorInt(颜色整型，如RRGGBB或AARRGGBB)
* @AnyRes

###### 线程注解
检查方法是在特定类型的线程中调用。如果某个类中的所有方法具有相同的线程要求，可直接在类上使用注解。

* @MainThread
* @UiThread
* @WokerThread
* @BinderThread
* @AnyThread

**注意**：
构建工具将@MainThread和@UiThread视为可互换。不过系统应用在不同线程上带有多个视图，ui线程可与注线程不同。故@UiThread用于标识与应用视图层次结构关联方法。@MainThread仅标注与应用生命周期关联的方法。

##### 值约束
验证传递的参数的值，是否在指定范围内。

* @IntRange
* @FloatRange
* @Size

``` java
public void setAlpha(@IntRange(from=0,to=255) int alpha) { … }
public void setAlpha(@FloatRange(from=0.0, to=1.0) float alpha) {...}
@Size(min=2)
@Size(max=2)
@Size(2)
@Size(multiple=2)
```

##### 权限
验证方法调用方的权限。检查一组有效权限中是否存在某个权限，使用anyOf属性。

* @RequiresPermission
* @RequiresPermission.Read
* @RequiresPermission.Write

###### 直接权限
``` java
@RequiresPermission(Manifest.permission.SET_WALLPAPER)
public abstract void setWallpaper(Bitmap bitmap) throws IOException;
@RequiresPermission(allOf = {
    Manifest.permission.READ_EXTERNAL_STORAGE,
    Manifest.permission.WRITE_EXTERNAL_STORAGE})
public static final void copyFile(String dest, String source) {
    ...
}

// intent权限，权限要求添加到定义intent操作名称的字符串字段上。
@RequiresPermission(android.Manifest.permission.BLUETOOTH)
public static final String ACTION_REQUEST_DISCOVERABLE =
            "android.bluetooth.adapter.action.REQUEST_DISCOVERABLE";

// 资源访问权限
@RequiresPermission.Read(@RequiresPermission(READ_HISTORY_BOOKMARKS))
@RequiresPermission.Write(@RequiresPermission(WRITE_HISTORY_BOOKMARKS))
public static final Uri BOOKMARKS_URI = Uri.parse("content://browser/bookmarks");
```

###### 间接权限
如果所需权限依赖于传入的参数，则只需要使用@RequiresPermission注解，无需指定具体的权限。方法调用时，构建工具会检查获取具有参数的权限，无效使用时会生成警告。
``` java
public abstract void startActivity(@RequiresPermission Intent intent, @Nullable Bundle) {...}
```

##### 返回值
验证实际使用的是方法执行的结果还是返回值。并给出使用建议。

* @CheckResult

```
public @CheckResult String trim(String s) { return s.trim(); }
  ...
  s.trim(); // this is probably an error
  s = s.trim(); // ok

@CheckResult(suggest="#enforcePermission(String,int,int,String)")
public abstract int checkPermission(@NonNull String permission, int pid, int uid);
```

##### CallSuper
验证替换方法是否会调用方法的超类实现。

* @CallSuper

``` java
@CallSuper
protected void onCreate(Bundle savedInstanceState) {
}
```

##### 类型定义
结合@Retention来创建枚举注解，来验证整型或字符型枚举常量的使用。确保返回值与参数值是指定的枚举类型常量。

* @IntDef
* @StringDef

``` java
import android.support.annotation.IntDef;
...
public abstract class ActionBar {
    ...
    // Define the list of accepted constants and declare the NavigationMode annotation
    @Retention(RetentionPolicy.SOURCE)
    @IntDef({NAVIGATION_MODE_STANDARD, NAVIGATION_MODE_LIST, NAVIGATION_MODE_TABS})
    public @interface NavigationMode {}

    // Declare the constants
    public static final int NAVIGATION_MODE_STANDARD = 0;
    public static final int NAVIGATION_MODE_LIST = 1;
    public static final int NAVIGATION_MODE_TABS = 2;

    // Decorate the target methods with the annotation
    @NavigationMode
    public abstract int getNavigationMode();

    // Attach the annotation
    public abstract void setNavigationMode(@NavigationMode int mode);
```

###### 结合flag
若枚举常量可与(|、&和^等)结合使用。可使用flag属性。
``` java
import android.support.annotation.IntDef;
...

@IntDef(flag=true, value={
        DISPLAY_USE_LOGO,
        DISPLAY_SHOW_HOME,
        DISPLAY_HOME_AS_UP,
        DISPLAY_SHOW_TITLE,
        DISPLAY_SHOW_CUSTOM
})
@Retention(RetentionPolicy.SOURCE)
public @interface DisplayOptions {}

...
```

##### 代码可访问性
验证方法、类或字段的可访问性。

* @Keep（阻止移除）
* @VisibleForTesting

#### Tools属性引用
用于设计时特性（布局应用的fragment或activity）或编译时行为（shrink mode）。
构建app时，构建工具会移除这些属性，不会影响apk大小与运行时行为。

##### 错误处理
###### tools:ingnore / tools:targetApi
适用范围：所有元素
使用者：Lint

###### tools:locale
适用范围：resource
使用者：Lint, Android Studio editor
``` xml
<resources xmlns:tools="http://schemas.android.com/tools"
    tools:locale="es">
```

##### 设计时视图

###### 替代android命名空间。
适用范围：View
使用者：layout editor
如显示文案、改变可视度。

###### tools:context
适用范围：根View
使用者：layout editor
绑定activity，便于应用主题与更准确的快速修复（如onClick）。

###### tools:itemCount
适用范围：RecyclerView
使用者：layout editor
指定渲染的项目数。

###### tools:layout
适用范围：fragment
使用者：layout editor
``` xml
<fragment android:name="com.example.master.ItemListFragment"
    tools:layout="@layout/list_content" />
```

###### tools:listitem / tools:listheader / tools:listfooter
适用范围：AdapterView及其子View
使用者：layout editor
**注意**:
该功能在2.2上不可用，在2.3上已修复。

###### tools:showIn
适用范围：被include引用的根View
使用者：layout editor
允许include的布局展示在父布局视图中。

###### tools:menu
适用范围：根View
使用者：layout editor
指定app bar展示的菜单。

###### tools:minValue / tools:maxValue
适用范围：NumberPicker
使用者：layout editor
指定数值选择器范围。

###### tools:openDrawer
适用范围：DrawerLayout
使用者：layout editor
允许在预览界面打开抽屉布局。

###### "@tools:sample/*" resources
适用范围：支持文字或图像的View
使用者：layout editor
提供注入到布局的占位符数据。

##### 资源压缩
启用严格引用检查，来声明保留或丢弃具体资源。
这些属性要求构建工具开启资源压缩功能。
``` gradle
android {
    ...
    buildTypes {
        release {
            shrinkResources true
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
                    'proguard-rules.pro'
        }
    }
}
```

###### tools:shrinkMode
适用范围：resources
使用者：启用资源压缩的构建工具。
safe或strict。区别在于safe会保留动态引用资源（如Resources.getIdentifier()）。

###### tools:keep
适用范围：resources
使用者：启用资源压缩的构建工具。
用于保留间接引用的资源。
在res/raw/keep.xml中声明保留资源。
``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:tools="http://schemas.android.com/tools"
    tools:keep="@layout/used_1,@layout/used_2,@layout/*_3" />
```

###### tools:discard
适用范围：resources
使用者：启用资源压缩的构建工具。
手动移除被引用但实际不影响应用运行或gradle插件错误推断出被引用的资源。
``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:tools="http://schemas.android.com/tools"
    tools:discard="@layout/unused_1" />
```

### 构建与运行
#### Run/Debug Configurations
配置运行或调试选项。比如安装的模块、安装文件、启动方式、部署目标、调试器等等。

#### gradle compiler命令行配置
Settings &gt; Build, Execution, Deployment &gt; Compiler

#### Instant Run
缩短应用更新的时间。
要求Gradle Plugin 2.0或更高、非NDK、最低API 15，最佳21或更高。

##### 运行方式
###### 热交换
最快交换方式。应用保持运行，下次调用更新方法时使用更改的代码。。不会重新初始化运行中的应用对象。默认情况下热交换会自动重新启动当前Activity。
Build, Execution, Deployment &gt; Instant Run中Restart activity on code changes可以停止自动重新启动。停止后，可通过Run &gt; Restart Activity菜单命令手动重启。

* 更改现有方法的实现代码。

###### 温和交换
更新资源时，必须重启当前Activity。应用保持运行，activity重启时屏幕会出现小闪烁。
* 更改或移除现有资源

###### 冷交换
要求API 21或更高。速度最慢，必须重启整个应用。低于API 21的设备会引发应用重新部署。

*  结构性的代码更改
	*  添加、移除或更改
		*  注释
		*  静态和实例字段
		*  静态和实例方法签名
	*  更改当前类所继承的父类
	*  更改实现界面的列表
	*  更改类的静态初始值设定项
	*  动态资源ID的布局元素重新排序

###### 重新部署
如果构建过程中自动更新应用清单的任何部分，如versionCode或versionName。会无法利用InstantRun的性能优势。

*	更改应用清单
*	更改应用清单引用的资源
*	更改Android小部件的UI元素(清除并重新运行)

##### 优化构建时间

###### DexOptions配置

* maxProcessCount
可并行启动的Dex进程的最大数量。若gradle后台进程已经运行，执行gradlew --stop停止此进程。

* javaMaxHeapSize
dex操作的最大堆内存。

``` gradle
android {
  ...
  dexOptions {
    maxProcessCount 4 // this is the default value
    javaMaxHeapSize "2g"
  }
}
```

###### dexing-in-process和增量Java编译
Gradle Plugin 2.1.0版本引入。
默认启用增量Java编译，即仅对发生变化或需要重新编译的源代码部分进行重新编译。
dexing-in-process在构建流程而不是单独的外部VM流程中执行dexing。
启用此功能要求gradle后台进程的最大堆内存至少为2048MB。如果在DexoOptions中配置了javaMaxHeapSize，则需要在此基础值上加1024MB。
``` gradle
// gradle.properties
org.gradle.jvmargs = -Xmx3072m
```

###### 从Windows Defender中排除项目
Windows Defender 恶意软件扫描可能导致Instant Run运行速度变慢。
[Windows Defender排除文件夹](https://answers.microsoft.com/en-us/protect/forum/protect_defender-protect_scanning/how-to-exclude-a-filefolder-from-windows-defender/f32ee18f-a012-4f02-8611-0737570e8eee)

###### 缩短使用Crashlytics时的构建时间
Fabric Gradle Plugin版本低于1.21.6时，会导致构建时间变长。升级或停用插件。

##### 不支持情况
###### 多设备部署
###### 启用MultiDex的Android 4.4(API 20)
###### 仪器测试和性能分析器
###### 第三方插件，如（Java Code Coverage Library和ProGuard，执行字节码增强的插件）
###### 多进程应用（仅冷交换会推送到非主进程）

#### Emulator
##### 文件结构
###### 系统文件夹(-sysdir)
sdk/system-images/android-apiLevel/variant/arch/

* -kernel: kernel-qemu, kernel-ranchu
二进制内核镜像。QEMU(quick emulator)1为goldfish, ranchu为QEMU2。

* -system: system.img
系统镜像的只读初始版本。包含对应API版本与平台的系统库和数据。

* -ramdisk: ramdisk.img
启动分区镜像。system.img的子集。在系统镜像挂载前由内核镜像加载。通常只包含一些二进制文件和初始化脚本。

* -initdata(-init-data): userdata.img
数据分区的初始版本。呈现在/data文件夹下，包含AVD的所有可写数据。使用-wipe-data可擦除修改后数据。

###### 数据文件夹(-datadir)
~/.android/avd/name.avd/

* -data: userdata-qemu.img
存储用户或特定会话数据分区镜像。比如已安装app数据、配置、数据库、文件、SDK路径等。
新创建的AVD或应用-wipe-data选项，模拟器会复制userdata.img来创建该镜像。

* -cache: cache.img
缓存分区镜像。呈现在/cache文件下。初始为空。存储时时下载文件，由下载管理器或系统读写。关闭后文件被删除。可使用-cache选项保存文件。

* -sdcard: sdcard.img
可选SD卡分区镜像。模拟SD卡行为。使用mksdcard工具创建。存储在开发电脑上，必须在启动时加载。
使用mtools可复制文件到该镜像。模拟器将SD卡视作字节池，不关心SD卡格式。
-wipe-data不会对SD卡数据产生影响。删除SD卡数据要删除该镜像并重建SD卡。

##### 命令行
[emulator-commandline](https://developer.android.google.cn/studio/run/emulator-commandline.html)
###### 格式
```
emulator -avd avd_name [ {-option [value]} ... ]
emulator @avd_name [ {-option [value]} ... ]
emulator -help-option
```

###### 使用
```
$ emulator --list-avds
$ emulator -help-datadir
```

###### 快速启动
* -no-snapshot-load
* -no-snapshot-save
* -no-snapshot

###### 设备硬件
mode: emulated、webcamX、none
* -camera-back mode
* -camera-front mode
* -webcam-list

```
$ emulator @Nexus_5X_API_23 -camera-back webcam0
```

###### 内存
* -memory size
* -sdcard filepath
* -wipe-data

###### 调试
* -debug tag
* -debug-tag
* -debug-no-tag
all代表全部。
```
$ emulator @Nexus_5X_API_23 -debug init,metrics
$ emulator -help-debug-tags
```

* -logcat logtags
logtags格式为：componentName:logLevel
```
$ emulator @Nexus_5X_API_23 -logcat *:e
$ adb logcat -help
```

* -show-kernel
输出内核调试信息。

* -verbose
打印模拟器初始化信息到终端。

##### 不支持功能
* WLAN
* 蓝牙
* NFC
* SD卡插入/弹出
* 耳机
* USB

##### 配置
###### skins
* hardware.ini
```
# SDK/platforms/android-23/skins/WXGA800/hardware.ini
# skin-specific hardware values
hw.lcd.density=160
vm.heapSize=48
hw.ramSize=1024
hw.keyboard.lid=no
```

* layout
SDK/platforms/android-23/skins/WXGA800/layout
SDK/skins/nexus_4/layout
```
parts {
    device {
        display {
            width   320
            height  480
            x       0
            y       0
        }
    }

    portrait {
        background {
            image background_port.png
        }

        buttons {
            power {
                image  button_vertical.png
                x 1229
                y 616
            }
        }
    }
    ...
}
```

### 构建配置
#### 文件结构
##### Gradle设置文件
指定构建应用时包含模块。
``` gradle
include ':app'
```

##### 顶层构建文件
模块共享的构建配置。使用buildscript{}代码块定义项目中所有模块共用的gradle存储区和依赖项。
``` gradle
buildscript {
	repositories { ... }
    dependencies { ... }
}

allprojects {
	repositories { ... }
}
```

##### 模块构建文件
模块专用构建设置。自定义构建类型、产品风味，以及替换main/应用清单或顶层构建文件配置。
``` gradle
apply plugin: 'com.android.application'

android {
	...
    defaultConfig { ... }
    buildTypes { ... }
    productFlavors { ... }
}

dependencies { ... }
```

##### 属性文件
位于项目根目录。用于指定构建工具包本身的配置。

* gradle.properties
配置Gradle。如后台进程最大堆内存。

* local.properties
配置构建系统的本地环境属性。如SDK安装路径。该文件自动生成，不应纳入版本控制系统。

##### 源集
main为所有构建变体共用的代码和资源。其他源集目录可选，配置构建变体时，不会自动创建。
优先级：构建变体 &gt; 构建类型 &gt; 产品风味 &gt; 主源集 &gt; 库依赖项

```
src/main
src/<buildType>
src/<productFlavor>
src/<productFlavorBuildType>
```

#### 整洁原则
##### 切割脚本
根据脚本职责分割脚本，模块共享脚本置于顶层，模块专用脚本置于模块层。

##### 整合依赖
所有的依赖库、版本放置于一个脚本文件中，甚至共享到服务器，选择引用。
``` gradle
apply from: ‘http://company/1.0/projectStructure.gradle’
```

##### 任务、任务类型、动态任务
编写代码，定义任务、任务类型或动态任务，减少重复代码。

#### 加速构建
[Optimize Your Build Speed](https://developer.android.google.cn/studio/build/optimize-your-build.html#optimize)
##### 原则
* Keep your tools up to date
* Create a build variant for development
``` gradle
	flavorDimensions "api", "mode"

    productFlavors {
        dev {
        	dimension "api"
            minSdkVersion 21
        }
        prod {
        	dimension "api"
            minSdkVersion 14
        }
        demo {
          dimension "mode"
          ...
        }

        full {
          dimension "mode"
          ...
        }
    }
	// 过滤变体，如dev的release版本
    variantFilter { variant ->
    def names = variant.flavors*.name
    // To check for a build type instead, use variant.buildType.name == "buildType"
    if (names.contains("minApi21") && names.contains("demo")) {
      // Gradle ignores any variants that satisfy the conditions above.
      setIgnore(true)
    }
  }
```

* Avoid compiling unnecessary resources
``` gradle
resConfigs "en", "xxhdpi"
```

* Disable Crashlytics for your debug builds
``` gradle
android {
  ...
  buildTypes {
    debug {
      ext.enableCrashlytics = false
//      ext.alwaysUpdateBuildId = false
    }
}
```
``` java
Crashlytics crashlyticsKit = new Crashlytics.Builder()
    .core(new CrashlyticsCore.Builder().disabled(BuildConfig.DEBUG).build())
    .build();
Fabric.with(this, crashlyticsKit);
```

* Use static build config values with your debug build
* Use static dependency versions
``` gradle
'com.android.tools.build:gradle:2.+ // error!
```

* Enable offline mode
Settings &gt; Build, Execution, Deployment &gt; Gradle &gt; Offline work

* Enable configuration on demand
Settings &gt; Build, Execution, Deployment &gt; Compiler &gt; Configure on demand

* Create library modules

* Create tasks for custom build logic
project-root/buildSrc/src/main/groovy/

* Convert images to WebP

* Disable PNG crunching
``` gradle
android {
    buildTypes {
        release {
            // Disables PNG crunching for the release build type.
            crunchPngs false
        }
    }
}
```

* Enable Instant Run
要求debug版本、Gradle Plugin 2.3.0、minSdkVersion 15、Device 5.0。
Settings &gt; Build, Execution, Deployment &gt; Instant Run

* Enable Build Cache
Gradle Plugin 2.3.0及以上默认开启。
在gradle.properties中可关闭。
```
android.enableBuildCache=false
android.buildCacheDir=<path-to-directory>
```
如果进行了如下配置，buildCache功能将自动被Android Studio禁用。

	* minSdkVersion 21以下版本启用了multiDexEnabled功能
	* 开启了混淆功能minifyEnabled
	* 关闭了预先Dex库功能preDexLibraries

* Disable annotation processors

##### 分析构建过程
分析报告输出目录： project-root/build/reports/profile/
报告格式为html。

``` bash
$ gradlew clean
$ gradlew --profile --recompile-scripts --offline --rerun-tasks assembleFlavorDebug
```

#### gradle应用
##### 设置应用ID
为不同的构建类型或产品类型创建不同的应用ID。清单中可用${applicationId}占位符。
``` gradle
android {
    defaultConfig {
        applicationId "com.example.myapp"
    }
    buildTypes {
        debug {
            applicationIdSuffix ".debug"
        }
    }
    productFlavors {
        free {
            applicationIdSuffix ".free"
        }
        pro {
            applicationIdSuffix ".pro"
        }
    }
}
```

##### 配置证书
在build.gradle脚本中配置证书密钥并不安全。
``` gradle
storePassword System.getenv("KSTOREPWD")
keyPassword System.getenv("KEYPWD")

// 命令行读取
storePassword System.console().readLine("\nKeystore password: ")
keyPassword System.console().readLine("\nKey password: ")

// 属性文件读取
def keystorePropertiesFile = rootProject.file("keystore.properties")
def keystoreProperties = new Properties()
keystoreProperties.load(new FileInputStream(keystorePropertiesFile))

keyAlias keystoreProperties['keyAlias']
keyPassword keystoreProperties['keyPassword']
storeFile file(keystoreProperties['storeFile'])
storePassword keystoreProperties['storePassword']
```

##### 增加新构建类型
仅有build/release两种构建类型，必须用matchingFallbacks指定依赖构建类型，同时要signingConfig指定签名文件，否则gradle任务中无此构建类型。
``` gradle
loggable {
    applicationIdSuffix ".loggable"

    matchingFallbacks = ['release']
    buildConfigField("boolean", "LOG_DEBUG", "true")
    signingConfig signingConfigs.release
}
```

##### 多版本apk构建
[Build Multiple APKs](https://developer.android.google.cn/studio/build/configure-apk-splits.html)
根据不同屏幕分辨率或ABI架构构建应用版本。
###### densities
默认生成通用版本。
``` gradle
android {
  ...
  splits {
    density {
      enable true
      exclude "ldpi", "xxhdpi", "xxxhdpi"
      compatibleScreens 'small', 'normal', 'large', 'xlarge'
    }
  }
}
```

###### ABI
除非配置，否则不生成通用版本。
``` gradle
android {
  ...
  splits {
    abi {
      enable true
      reset()
      include "x86", "armeabi-v7a", "mips"
      universalApk false
    }
  }
```

###### version
谷歌商店不允许app的不同版本共用一个版本号。
``` gradle
ext.abiCodes = ['armeabi-v7a':1, mips:2, x86:3]
// ext.densityCodes = ['mdpi': 1, 'hdpi': 2, 'xhdpi': 3]

import com.android.build.OutputFile
android.applicationVariants.all { variant ->
  variant.outputs.each { output ->
    def baseAbiVersionCode = project.ext.abiCodes.get(output.getFilter(OutputFile.ABI))
    if (baseAbiVersionCode != null) {
      output.versionCodeOverride =
              baseAbiVersionCode * 1000 + variant.versionCode
    }
  }
}
```

###### 命名
modulename-screendensityABI-buildvariant.apk

##### 清单合并
(Merged Manifest](https://developer.android.google.cn/studio/build/manifest-merge.html#_9)

###### 合并规则标记
* tools:node = "merge|remove|removeAll|replace|strict"
向整个XML元素应用规则。

* tools:remove|replace|strict = "attr,..."
仅向清单标记中特定属性应用规则。

* tools:selector = "com.example.lib1"
仅对某个特定的导入库应用规则。

* tools:overrideLibrary = "com.example.lib1"
仅对某些特定的导入库应用规则。

##### 清单占位符
###### applicationId
``` xml
<intent-filter ... >
    <action android:name="${applicationId}.TRANSMOGRIFY" />
    ...
</intent-filter>
```

###### 自定义
``` xml
<intent-filter ... >
    <data android:scheme="http" android:host="${hostName}" ... />
    ...
</intent-filter>
```
gradle中配置实际值
``` gradle
android {
    defaultConfig {
        manifestPlaceholders = [hostName:"www.example.com"]
    }
    ...
}
```

##### 共享自定义字段和资源值
声明自定义属性与值
``` gradle
android {
  ...
  buildTypes {
    debug {
      buildConfigField("String", "BUILD_TIME", "\"0\"")
      resValue("string", "build_time", "0")
    }
  }
}
```
应用自定义属性
``` java
Log.i(TAG, BuildConfig.BUILD_TIME);
Log.i(TAG, getString(R.string.build_time));
```

##### 依赖管理
[Google's Maven repository](https://developer.android.google.cn/studio/build/dependencies.html#google-maven)

###### 依赖类型
* 本地库模块
该模块名与setting.gradle中模块名一致。
``` gradle
compile project(':mylibrary')
```

* 本地二进制库
gradle可读取相对模块build.gradle的路径中的jar文件。
``` gradle
compile fileTree(dir: 'libs', include: ['*.jar'])
compile files('libs/foo.jar', 'libs/bar.jar')
```

* 远程二进制库
远程库依赖的前提是，必须指定查找的远程库。
``` gradle
compile 'com.example.android:app-magic:12.3'
compile group: 'com.example.android', name: 'app-magic', version: '12.3'
```

###### 依赖配置
指定库存在的时期、对应的构建版本。

* compile, apk, provided
依赖库的添加方式：添加到编译并打包到apk，仅打包到apk，仅编译

* buildin: debugCompile, testCompile, flavorCompile

* custom: freeDebugApk
``` gradle
configurations {
    // Initializes a placeholder for the freeDebugApk dependency configuration.
    freeDebugApk {}
}
dependencies {
    freeDebugApk fileTree(dir: 'libs', include: ['*.jar'])
    // 指定库构建变体，配置publishNonDefault, defaultPublishConfig改变默认构建版本。
    releaseCompile project(path: ':my-library-module', configuration: 'release')
}
```

##### 代码压缩
要求SDK tools 25.0.10及以上，Gradle Plugin 2.0.0及以上。

###### 工具及配置
SDK/tools/proguard
proguard仅在发布编译时运行，Instant Run执行时会停用。若要在调试构建启用压缩代码功能，请关闭混淆和优化功能，代码压缩仅移除未使用代码。
``` gradle
android {
    buildTypes {
        debug {
            minifyEnabled true
            useProguard false
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
                    'proguard-rules.pro'
        }
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
                    'proguard-rules.pro'
        }
    }
}
```

###### 混淆规则
* proguard-android.txt -- 默认规则
* proguard-android-optimize.txt -- 进一步优化规则
* proguard-rules.pro -- 自定义规则
* flavor2-rules.pro --产品风味规则

``` gradle
android {
    ...
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
                   'proguard-rules.pro'
        }
    }
    productFlavors {
        flavor1 {
        }
        flavor2 {
            proguardFile 'flavor2-rules.pro'
        }
    }
}
```

###### 输出文件
文件目录：&lt;module-name&gt;/build/outputs/mapping/release/

* dump.txt
类文件的内部结构。

* mapping.txt
提供原始与混淆过的类、方法和字段名称之间的转换。
proguard每次运行都会创建该文件，一定要每次混淆后保存副本。以便使用retrace工具追踪。
``` bash
retrace.bat|retrace.sh [-verbose] mapping.txt [<stacktrace_file>]
retrace.bat -verbose mapping.txt obfuscated_trace.txt
```

* seeds.txt
列出未进行混淆的类和成员。

* usage.txt
列出从apk移除的代码。

###### 误移除情况

* 只存在AndroidManifest.xml的引用
* 应用调用的方法来自于JNI
* 运行时（如反射或自检）操作代码


##### 资源压缩
要求SDK tools 25.0.10及以上，Gradle Plugin 2.0.0及以上。
资源压缩需要与代码压缩协同工作。当代码压缩器移除未使用代码后，资源压缩器才能确定应用仍使用的资源。否则会因为存在资源引用，无法移除实际未使用资源。
资源压缩器不会移除备用资源、values/文件夹中定义的资源。

###### 工具与配置
SDK/build-tools/26.0.2/lib/shrinkedAndroid.jar

``` gradle
android {
    ...
    buildTypes {
        release {
            shrinkResources true
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
                    'proguard-rules.pro'
        }
    }
}
```

###### 自定义保留与舍弃资源
使用tools属性工具在res/raw/keep.xml自定义压缩模式与规则。
``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources xmlns:tools="http://schemas.android.com/tools"
	tools:shrinkMode="strict"
    tools:keep="@layout/l_used*_c,@layout/l_used_a,@layout/l_used_b*"
    tools:discard="@layout/unused2" />
```

###### 备用资源移除
资源压缩器不会移除备用资源。故使用插件的resConfigs来移除备用资源。
``` gradle
android {
    defaultConfig {
        ...
        resConfigs "en", "fr"
    }
}
```

###### 输出文件
文件目录：&lt;module-name&gt;/build/outputs/mapping/release/

* resources.txt
资源引用情况及移除资源详情。通过查找该文件，可以查看资源的引用及保留原因。

##### MultiDex
Android 5.0(API 21)之前的Dalvik限制单个classes.dex的方法总数不能超过65536，使用Dex分包支持库可解决64K引用限制问题。
Android 5.0(API 21)及之后的ART虚拟机支持多个Dex加载，将多个dex整合为.oat可执行文件。因此无需配置Dex分包支持库。

###### 规避64K限制
减少应用代码调用的方法总数。

* 检查应用的直接和传递依赖项
* 使用Proguard移除未使用的代码

###### 配置
依赖库：com.android.support:multidex:1.0.1
minSdkVersion低于21时需要配置。
gradle配置项为multiDexEnabled。

* 无自定义Application
在清单中application中声明android.support.multidex.MultiDexApplication类。

* 自定义Application
``` java
public class MyApplication extends MultiDexApplication { ... }
```

* 自定义Application不可继承
``` java
public class MyApplication extends SomeOtherApplication {
  @Override
  protected void attachBaseContext(Context base) {
     super.attachBaseContext(context);
     Multidex.install(this);
  }
}
```

###### 声明主DEX文件中需要的类
multiDexKeepFile和multiDexKeepProguard指定的文件与模块build.gradle同一级别。

* multiDexKeepFile
声明类文件名multidex-config.txt，内容样式如下：
```
com/example/MyClass.class
com/example/MyOtherClass.class
```
在gradle中配置
``` gradle
android {
    buildTypes {
        release {
            multiDexKeepFile file 'multidex-config.txt'
            ...
        }
    }
}
```

* multiDexKeepProguard
声明类文件名multidex-config.pro，语法与proguard相同：
```
-keep class com.example.MyClass
-keep class com.example.MyClassToo
```
在gradle中配置
``` gradle
android {
    buildTypes {
        release {
            multiDexKeepProguard 'multidex-config.pro'
            ...
        }
    }
}
```

###### 开发构建中Dex分包优化
创建dev产品风味，设置minSdkVersion为21，禁用proguard，在开发构建中无需配置Dex分包支持库。
Android 5.0(API 21)起启用pre-dexing构建功能：将每个应用模块和每个依赖项构建为单独的Dex文件。ART支持多个Dex文件加载，无需合并及主Dex中类确定导致的长时间计算。
优点：进行快速的增量式构建。

###### 测试
AndroidJUnitRunner直接支持Dalvik可执行文件分包应用。前提是该应用application使用或继承MultiDexApplication，或调用了MultiDex.install(this)方法。
否则，可通过自定义AndroidJUnitRunner类实现。
``` java
public void onCreate(Bundle arguments) {
    MultiDex.install(getTargetContext());
    super.onCreate(arguments);
    ...
}
```
**注意**:
dex分包支持库不支持测试apk构建。

###### 局限性
* 辅助Dex文件较大时，可能导致应用ANR错误
* 运行平台低于Android 4.0(API 14)时，应用启动或加载特定类时会出现问题
* Android 5.0(API 21)以前Dex分包应用可能产生内存分配问题导致运行时崩溃

##### Apk分析器

1. 拖入apk到Editor区域
2. Project视图中双击apk
3. Build &gt; Analyze APK

**注意**：
不要分析增量Apk。区别增量Apk关键在于apk包下是否存在instant-run.zip。

### 调试
#### 开发者选项
Setting &gt; About phone/System(8.0) &gt; Build number 7 times

#### Logcat
##### 消息格式
```
date time PID-TID/package priority/tag: message
```

##### 日志级别
Assert &gt; Error &gt; Warn &gt; Info &gt; Debug &gt; Verbose
**注意**：
调试日志会在运行时去掉。错误、警告、信息日志会被保留。
日志标记字符数不能大于23个。

##### Screen Capture
系统截图组合键：电源键+音量键-
[在线设备效果图生成器](https://developer.android.google.cn/distribute/marketing-tools/device-art-generator.html)

##### Screen Recorder
最长三分钟的屏幕录像。

#### Analyze Stacktrace
##### open
Analyze &gt; Analyze stacktrace

##### auto detect
监听剪贴板。
Analyze stacktrace &gt; Automatically detect and analyze ...

#### Layout Inspector
检查运行时视图结构。
仅能检查可调试应用，root或模拟器可以调试所有应用。
Tools &gt; Android &gt; Layout Inspector

##### 原理
保存设备当前截图，存储为.li格式文件。设备视图变化后，不会自动更新。需要重新打开Layout Inspector捕获截图，存储新的.li文件。
.li文件输出路径：&lt;project-name&gt;/captures/

##### 视图结构
* View Tree
布局视图树

* Screenshot
显示带有可视边界视图的设备截图。

* Propertise Table
选中视图的布局属性面板。

#### Pixel Perfect
显示放大的应用视图，以检查像素位置或属性。辅助设计。

##### 启动
1. 连接设备
2. 打开Android Studio，构建并运行应用
3. Tools &gt; Android &gt; Android Device Monitor
4. Window &gt; Open Perspective
5. 双击Windows窗口的设备

##### 界面结构
* 视图树
* 完美像素放大镜
* 完美像素

### 测试
#### Gradle命令
##### Local Unit Test
报告输出路径：module/build/reports/tests/
结果输出路径：module/build/test-results/

``` bash
gradlew test
gradlew test<VariantName>UnitTest
// 指定执行测试的测试方法
gradlew testVariantNameUnitTest --tests *.sampleTestMethod
```

##### Instrumented Unit Test
报告输出路径：module/build/reports/androidTests/connected/
结果输出路径：module/build/outputs/androidTest-results/connected/
``` bash
gradlew connectedAndroidTest
gradlew cAT // 任务名简写
gradlew connected<VariantName>AndroidTest
gradlew mylibrary:connectedAndroidTest  // 指定模块测试
```

##### 多模块报告
报告输出路径：project/build/

1. 项目级build.gradle添加报告插件
``` gradle
apply plugin: "android-reporting"
```

2. 执行mergeAndroidReports任务
continue参数：跳过失败的测试直到完成所有测试用例。

``` bash
gradlew test mergeAndroidReports --continue
gradlew connectedAndroidTest mergeAndroidReports
```

#### adb命令
##### 格式
```
$ adb shell am instrument [flags] <test_package_name>/<runner_class>
$ adb shell am instrument -w \
> -e class com.android.demo.app.tests.Foo1,com.android.demo.app.tests.Foo2#bar3 \
> com.android.demo.app.tests/android.support.test.runner.AndroidJUnitRunner
```

##### 参数
* -w
强制设备等待。保持shell开启直到测试完成。因为测试结果输出到标准输出设备，故如果不指定该参数，无法查看测试结果。

* -r
指定测试结果以原始格式输出。通常用于性能测试。该参数设计为配合-e参数一起使用。

* -e &lt;test_options&gt;
以键值对方式提供测试参数。am instrument工具会传递这些参数给指定的仪器类的onCreate方法中，存储到Bundle中。可以多次出现。只能用于AndroidJUnitRunner或InstrumentationTestRunner及其子类。
[Instrument options](https://developer.android.google.cn/studio/test/command-line.html#AMOptionsSyntax)

#### Espresso Test Recorder
Run &gt; Record Espresso Test

##### 添加断言
只能添加三种断言。基于添加断言时的截图，获取到视图树结构。

* textis
* exist
* does not exist

##### UI交互
断言编辑时间外，与app的交互，会生成tap/type记录下来。

#### Monkey
发送伪随机用户事件流对app执行压力测试的工具。
应用崩溃、收到未处理错误或ANR时，Monkey会终止执行。

##### 命令格式
``` bash
$ adb shell monkey [options] <event-count>
$ adb shell monkey -p package.name -v 500
$ adb shell monkey --help
```

##### 参数
推荐参数：打开日志，一个或多个应用限制，非零延迟，至少30秒运行时间。

###### 基本配置
日志类型。

* -v

###### 操作限制
如果不指定操作的应用，Monkey会跳转到任意应用。
按应用名或意图类型指定操作范围，可出现多次。

* -p package.name
* -c Intent.category

###### 事件类型和频率
随机种子、事件延迟、事件类型百分比等

* -s seed
* --throttle milliseconds
* --pct-touch percent


######调试配置
跳过错误继续执行、遇到错误后杀死进程、监视系统原生代码、等待调试器等。

* --hprof
* --ignore-crashes
* --wait-dbg

#### MonkeyRunner
无需源代码控制安卓应用或模拟器的工具。
可使用python脚本安装或测试应用。发送键盘指令、界面截图等。
为了执行功能测试而设计。

##### 特性
* 多设备控制
* 功能测试
* 回归测试
* 自动化扩展 -- python, plugin

##### APIs
###### MonkeyRunner
工具类：提供设备连接、创建工具界面、提供内置帮助。
[MonkeyRunner](https://developer.android.google.cn/studio/test/monkeyrunner/MonkeyRunner.html)

###### MonkeyDevice
代表设备或模拟器。安装卸载应用、打开应用、发送键盘或触摸事件。
[MonkeyDevice](https://developer.android.google.cn/studio/test/monkeyrunner/MonkeyDevice.html)

``` python
device = MonkeyRunner.waitForConnection()
```

###### MonkeyImage
代表屏幕截图。截图、位图转换、截图比较、写入文件。
[MonkeyImage](https://developer.android.google.cn/studio/test/monkeyrunner/MonkeyImage.html)

``` python
image = MonkeyDevice.takeSnapshot()
```

##### 命令

###### 生成API文档
format: text, html

``` bash
monkeyrunner help.py <format> <outfile>
```

###### 指定Plugin
Plugin不能访问Android框架API。plugin可出现多次。
program_filename是python测试脚本。

``` bash
monkeyrunner -plugin <plugin_jar> <program_filename> <program_options>
```

### 分析
#### Android Profiler
android studio 3.0启用。替代Android Monitor工具。
View &gt; Tool Windows &gt; Android Profiler
启用后会持续收集性能分析数据，直到设备断开连接或点击关闭。
共享时间线，查看CPU、内存、网络情况。

##### 启用高级分析
Android 8.0及以上版本默认开启。
Android 7.1及以下版本需要通过注入监视逻辑代码到app中才能启用，Run &gt; Edit Configurations启用高级分析，重新构建并运行app。
**注意**：
native代码不可用。使用JNI应用中native代码分配的内存不能分析。

##### CPU分析
查看CPU的Activity和方法跟踪信息。
点击CPU时间线的任意位置可打开CPU分析。

###### 界面结构
* Event时间线
显示应用中在其生命周期不同状态间转换的Activity，并表明用户与设备的交互。

* CPU时间线
显示应用的实时CPU使用率（以占用总可用CPU时间百分比表示）及应用使用的总线程数。

* 线程Activity时间线
列出属于应用进程的每个线程，沿时间线用颜色块标识Activity。记录函数跟踪后，可选择一个线程在跟踪空格中查看其数据。
**注意**：
分析器会Android Studio和Android平台添加到应用的线程，如JDWP、Profile Saver、Studio:VMStats、Studio:Perfa 以及 Studio:Heartbeat，确切名称可能有所不同。
分析器线程执行的是原生代码，无法记录分析器线程的函数跟踪数据。

	* 绿色 -- 运行中或可运行
	* 黄色 -- 等待中
	* 灰色 -- 休眠中

* 记录配置
确定分析器记录函数跟踪的方式。
Android 8.0(API 26)以下的版本对于记录文件大小有限制，默认为8M。

	* Sampled
采样间隔为1000μs即一毫秒，小于这个时间的执行函数，将不会被记录。

	* Instrumented
记录每个函数调用的开始和结束时记录时间戳。短时间内执行大量函数，分析器可能迅速超出记录文件大小限制，无法记录更多跟踪数据。

	* Edit configurations
自定义记录方式，记录文件大小、采样时间间隔等。


* 记录按钮
开始和停止记录函数跟踪。

###### 函数跟踪日志
* 使用CPU分析器记录函数跟踪日志
使用记录按钮，开始和停止记录跟踪日志。
日志查看有以下四种展示方式。
函数的执行时间 = 自身代码执行时间 + 被调用方代码执行时间。

	* 调用图与火焰图
系统API调用为橙色，应用自有函数调用为绿色，第三方API调用为蓝色。
调用图，调用方在上，火焰图与之相反。

	* 自顶向下与自底向上
以树状结构显示函数调用关系。
自顶向下，父节点为调用方，子节点为被调用方。自底向上与之相反。

* Debug类设置函数跟踪代码
比如在Activity类onCreate开始跟踪，在onDestory中结束跟踪。
``` java
Debug.startMethodTracing("sample");
// Debug.startMethodTracingSampling() 指定采样时间
Debug.stopMethodTracing();
```
日志输出路径：/sdcard/Android/data/package.name/files/sample.trace

####### 查看工具：
* [Traceview](https://developer.android.google.cn/studio/profile/traceview.html)
traceview的命令行模式已经过期了。
打开方式：DDMS &gt; File &gt; Open File。
已知问题：Android 5.1以前的版本不会记录分析过程中退出的线程、VM会利用线程ID，如果一个线程退出另一个线程开启，两者线程ID可能相同。

* [dmtracedump](https://developer.android.google.cn/studio/command-line/dmtracedump.html)
支持两种方式：HTML格式与图片格式。
图片格式要先安装[GraphViz](https://www.graphviz.org/)，并将bin目录添加到环境变量中。

``` bash
dmtracedump -h dmtrace.trace > trace.html
dmtracedump -g trace.png dmtrace.trace
```

##### Memory分析
帮助识别导致应用卡顿、冻结甚至崩溃的内存泄漏和流失。显示一个应用内存使用量的实时图表。

###### 计算内存
与Android Monitor的统计方法有所差别。

* Java -- Java/Kotlin代码分配的对象内存
* Native -- C/C++代码分配的对象内存
* Graphics -- 图形缓冲队列所使用的内存
* Stack -- 应用中原生堆栈和Java堆栈使用的内存，与线程数量相关
* Code -- 处理代码和资源的内存
* other -- 不确定分类的内存
* Allocated -- 应用分配的Java/Kotlin对象数

###### 捕获堆转储
查看任何给定时间内哪些对象正在使用内存。可查看已分配内存的对象类型、每个类型分配内存大小、每个对象正在使用内存大小、每个对象的引用、对象分配到的调用堆栈（仅限Android 7.1及以下版本）。
堆转储不会显示每个已分配对象的堆栈跟踪。故要先记录内存分配，再启用堆转储。

须注意的引起内存泄漏情况：
* 长时间引用Activity、Context、View、Drawable和其他对象
* 可持有Activity实例的非静态内部类，如Runnable
* 对象保持时间超出所需时间的缓存

堆转储仅能在内存分析器中查看，调用Export heap dump as HPROF file才能保存到文件中。Android的HProf文件与Java SE的HProf文件格式有所不同，需要使用SDK/platform-tools/hprof-conv进行转换。

``` bash
hprof-conv heap-original.hprof heap-converted.hprof
```

内存泄漏触发技巧
* 不同Activity生命周期下反复执行设备纵横转向操作
* 不同Activity生命周期下执行应用切换

##### Network分析
显示实时网络活动，包括发送和接收的数据以及当前的连接数。
执行网络请求时，设备必须使用高功耗的移动或WLAN无线装置收发数据包。
查找频繁出现的短时网络活动峰值。可使用批量处理网络请求，减少网络请求次数，改善电池续航表现。

###### 界面结构
* Event时间线及无线装置功耗状态
* 网络流量折线图
* 收发文件列表
* 所选收发文件详情

**注意**：
仅支持HttpURLConnection和OkHttp网络连接库。

#### Profile or Debug Apk
要求Android Studio 3.0及以上。
File &gt; Profile or Debug Apk

##### 附加Java源代码
1. 双击.smali文件
2. 点击Attach Java Sources
3. 找到源代码目录，点击Open

##### 附加原生调试符号
1. 点击cpp目录下不包含调试符号的原生库文件
2. 点击编辑器窗口右上角的Add
3. 找到可调试原生库的目录，点击OK
4. 如果apk与可调试原生库用不同的工作站构建，使用编辑窗口的Path Mappings部分中的Local Paths，添加缺失调试符号的本地路径。
5. 点击Apply

**注意**:
apk和可调试的.so文件必须使用相同的工作站或构建服务器构建。

#### Battery Historian
##### 获取报告
如果是Android 7.0及以上系统，报告后缀为zip。
``` bash
$ adb kill-server
$ adb devices
$ adb shell dumpsys batterystats --reset
// 断开连接线，仅使用手机电池打开运行应用
$ adb devices
$ adb shell dumpsys batterystats > path/batterstats.txt
$ adb bugreport > path/bugreport.txt // 7.0及以上为zip后缀
```

##### 安装
###### docker
[Dcoker CE](https://www.docker.com/community-edition)
安装完成后执行如下命令测试：
``` bash
$ docker run hello-world
```

###### Battery Historian
由于gcr.io无法访问，这里通过[Docker Store](https://store.docker.com/)获取Battery Historian镜像。
``` bash
$ docker pull bhaavan/battery-historian
$ docker run -p 8888:9999 bhaavan/battery-historian
```

##### 运行
浏览器访问http://localhost:8888/。
这里8888是自行指定的本机运行端口。

#### OpenGL ES Tracer
ES: Embedded System
能捕获OpenGL ES命令和逐帧图像。

##### 开启

1. Tools &gt; Android &gt; Android Device Monitor
2. Window &gt; Open Perspective &gt; Tracer for OpenGL ES

##### 收集数据
要求Android 4.1(API 16)及以上设备。

1. 连接设备，开启调试模式
2. 点击右上角，类似V的图标，进行追踪配置，开始追踪
3. 与要追踪的应用交互
4. 点击对话框的停止追踪按钮

##### 导入数据
点击右上角，类似C的图标，载入格式为*.gltrace的追踪数据。

#### Hierarchy Viewer
跳过：因为该工具已停止开发。替代工具Layout Inspector目前并未提供布局性能分析信息。