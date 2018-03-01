---
title: android ndk错误集
comments: true
toc: true
date: 1972-01-01 08:00:00
tags:
	- jni
	- errors
---

### nativeLibraryDirectories=[/data/app/com.lukouapp-1/lib/arm64, /vendor/lib64, /system/lib64]]] couldn’t find “libxxxx.so”
**问题现场**：
语音搜索功能错误，提示“组件未安装，错误码21001”
**运行环境**：
64位Android平台
**问题原因**：
jniLibs目录下不存在64位so文件，存在64位文件夹（arm64-v8a、mips64、X86_64）
**解决方案**：
Gradle配置编译打包时过滤掉所有64位jniLib文件夹。
``` gradle
    // 添加到module下的build.gradle的defaultConfig配置中
    ndk {
        abiFilters "armeabi", "armeabi-v7a", "x86", "mips"
    }
    // 在project目录下的gradle.properties配置
    android.useDeprecatedNdk=true
```

<!-- more -->

### loadLibrary XX error:java.lang.UnsatisfiedLinkError: dlopen failed: \**/lib/arm/libXX.so: has text relocations
**问题现场**：
语音搜索功能错误，提示“组件未安装，错误码21001”
**运行环境**：
64位Android平台
**问题原因**：
编译打包so文件配置的目标平台过低
**解决方案**：
重新打包或更新so文件

### Java.lang.UnsatisfiedLinkError: Native method not found
**问题原因**：
存在armbi-v7文件夹，缺失对应的so文件
**解决方案**：
仅有armbi平台的so库，放入armbi-v7的库之后解决；

### Error:(41) undefined reference to `__android_log_print'
**问题原因**：
编译的原生库未链接到android库
**解决方案**：
仅有armbi平台的so库，放入armbi-v7的库之后解决；

### java.lang.NoSuchMethodError: No static method
**问题原因**：
kotlin类似Java静态方法的Companion方法实际是非静态方法。
**解决方案**：
使用GetMethodID而不是GetStaticMethodID。

### JNI DETECTED ERROR IN APPLICATION: JNI CallVoidMethodV called with pending exception '
**问题原因**：
在后台线程调用了需要在前台线程运行的方法。如LiveData.setValue。
**解决方案**：
移除LiveData.setValue方法。

### undefined reference to `ASensorManager_getDefaultSensor
**问题原因**：
未导入链接库android

### CMake Error: CMake can not determine linker language for target: native-activity
**问题原因**：
找不到源文件时产生。