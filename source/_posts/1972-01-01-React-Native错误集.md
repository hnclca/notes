---
layout: errors
title: React Native错误集
date: 2018-04-03 13:51:36
tags:
	- react native
	- errors
---

### java.lang.UnsatisfiedLinkError: could find DSO to load: libreactnativejni.so

#### 相关错误
```
/data/data/com.example.admin.mykotlinapp/lib-main/libgnustl_shared.so" is 32-bit instead of 64-bit
```

#### 运行环境

* Android Studio 3.0.1
* android gradle plugin 3.0.0
* gradle 4.1
* react-native 0.54.4

#### 错误原因
React Native提供的libreactnativejni.so文件是32位，Android要么加载32位的库要么加载64的库，不可以混合加载。

#### 解决方案
禁止使用那些64位的so文件。
在项目build.gradle中配置abi过滤掉64位的Abi架构。

``` gradle
android {
    ...
    defaultConfig {
        ...
        ndk{
            abiFilters "armeabi-v7a", "x86"
        }
        ...
    }
...
}
```

<!-- more -->

### java.lang.IllegalAccessError: Method 'void android.support.v4.net.ConnectivityManagerCompat.&lt;init&gt;()'

#### 错误信息
```
java.lang.IllegalAccessError: Method 'void android.support.v4.net.ConnectivityManagerCompat.<init>()' is inaccessible to class 'com.facebook.react.modules.netinfo.NetInfoModule' (declaration of 'com.facebook.react.modules.netinfo.NetInfoModule' appears in /data/app/com.example.admin.mykotlinapp-2/split_lib_dependencies_apk.apk
```

#### 运行环境

* Android Studio 3.0.1
* android gradle plugin 3.0.0
* gradle 4.1
* react-native + (实际加载的版本为0.20.1)

#### 错误原因
0.20.1旧版本Bug，已修复。
jcenter仓库上最新版本为0.20.1。node_modules/react-native/android里面的才是真正最新的react-native库。
build.gradle配置中一定要指明具体的版本，不要使用+，否则即使指定了本地Maven库（node_modules下react-native库），也会加载jcenter上的0.20.1旧版本。

#### 解决方案
删除用户目录下的.gradle/caches/modules-2/files-2.1/com.facebook.react文件夹。

``` gradle
// 根build.gradle
allprojects {
	repositories {
    	...
        // 根据实际情况修改相对路径
    	maven { url "$rootDir/../node_modules/react-native/android" }
    }
}

// 项目build.gradle
dependencies {
	implementation "com.facebook.react:react-native:0.54.4"
}
```

### Unable to load script form assets "index.android.bundle"...

#### 运行环境

* Android Studio 3.0.1
* android gradle plugin 3.0.0
* gradle 4.1
* react-native 0.54.4

#### 错误原因

* release版本：在assets目录未找到index.android.bundle文件
* debug版本：未运行server服务器或server服务器地址不正确

#### 解决方案
##### release版本

1. 手动创建src/main/assets文件夹
2. 执行yarn run bundle-android命令
``` json
{
  "scripts": {
    "start": "node node_modules/react-native/local-cli/cli.js start",
    "bundle-android": "react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/"
  }
}
```

##### debug版本

1. 执行yarn run start命令
2. 打开开发者菜单【设备菜单】-【设备调试服务主机和端口】设置IP和端口
	* 要求手机跟电脑在同一网络
	* 真机：摇晃设备
	* 模拟器：在RN页面，快捷键Ctrl+M，与QQ热键有冲突
3. 退出应用重打开RN页面，或在开发者菜单【重载】JS

### com.facebook.react.devsupport.DebugServerException: Could not connect to development server.

#### 错误原因
未运行Js服务器。

#### 解决方案
执行yarn run start命令。

### error: bundling failed: NotFoundError: Cannot find entry file index.js in any of the roots:

#### 运行环境

* Android Studio 3.0.1
* android gradle plugin 3.0.0
* gradle 4.1
* react-native 0.54.4

#### 错误原因
react native 0.49.1以后入口不区分android和ios，只有一个入口文件index.js。

#### 解决方案
创建index.js文件，在文件内导入index.android.js和index.ios.js。