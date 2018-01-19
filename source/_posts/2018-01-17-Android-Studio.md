---
title: Android Studio
comments: false
toc: true
date: 2018-01-17 16:15:40
tags:
	- IDE
---

[TOC]

[android studio](https://developer.android.google.cn/studio/intro/index.html)介绍。

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

#### 实时模板

#### Lint
代码扫描。

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

