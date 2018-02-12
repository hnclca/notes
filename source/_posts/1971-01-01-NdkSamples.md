---
title: NdkSamples
tags:
  - jni
  - open source
comments: false
toc: true
date: 1971-01-01 16:00:00
---

[TOC]

谷歌官方提供的[NDK示例](https://github.com/googlesamples/android-ndk)。

<!-- more -->

### hello-jni(clear)
Java调用C代码示例。
通过判断是否存在相关CPU架构定义，来识别当前设备的CPU架构。

### hello-jni(kotlin)(clear)
kotlin调用C代码。原生函数声明和原生库加载如下：

``` kotlin
external fun stringFromJNI(): String

companion object {

    // Used to load the 'native-lib' library on application startup.
    init {
        System.loadLibrary("native-lib")
    }
}
```

### hello-jniCallback(clear)
C代码通过JNI调用Java代码示例。
kotlin中Companion方法是非静态方法。

### hello-libs(clear)
展示如何管理第三方C/C\+\+库。
与hello-jni类似，调用了预编译库。

#### 静态库
库类型：STATIC
库输出目录：ARCHIVE_OUTPUT_DIRECTORY
库后缀名：.a

#### 动态库
库类型：SHARED
库输出目录：LIBRARY_OUTPUT_DIRECTORY
库后缀名：.so

### hello-neon
ARM NEON架构执行FIR Filter测试。
跳过。

### native-activity（clear）
使用Native Activity从C代码中读取加速度数据。无Java代码的原生页面。

#### NativeActivity
如果是无Java代码的应用，在application中指定hasCode为false。
``` XML
<activity android:name="android.app.NativeActivity"
    android:configChanges="orientation|keyboardHidden">
    <meta-data android:name="android.app.lib_name"
        android:value="native-activity"/>
</activity>
```

#### android_app_glue
安卓应用胶水库。将android.app的一些API暴露给C代码。
构建脚本代码。
```
# build native_app_glue as a static lib
set(${CMAKE_C_FLAGS}, "${CMAKE_C_FLAGS}")
add_library(native_app_glue STATIC
    ${ANDROID_NDK}/sources/android/native_app_glue/android_native_app_glue.c)

# now build app's shared lib
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11 -Wall -Werror")

# Export ANativeActivity_onCreate(),
# Refer to: https://github.com/android-ndk/ndk/issues/381.
set(CMAKE_SHARED_LINKER_FLAGS
    "${CMAKE_SHARED_LINKER_FLAGS} -u ANativeActivity_onCreate")

target_include_directories(native-activity PRIVATE
    ${ANDROID_NDK}/sources/android/native_app_glue)
```

### native-audio
使用OpenSL ES API播放和记录声音。

### native-codec
原生媒体解码器Codec API播放视频。

### native-media
使用OpenMAX AL播放视频。

### native-plasma
使用Native Activity用C代码给位图添加等离子特效。

### webp
展示如何在原生Activity中使用webp。

### gles3jni
原生代码中如何使用OpenGL ES 3.0。

### teapots
茶壶渲染集合。包括：古典茶壶、多个茶壶、30帧率编舞。

### audio-echo(API 21)
使用openSL ES在安卓快速音频路径中创建播放器和刻录机。

### bitmap-plasma
位图的等离子特效渲染。

### builder
??gradle 测试脚本？

### camera(API 24)
两个示例：TextureView预览NDK相机图片，基础的NDK相机示例（预览与拍照）。

### display-p3（API 26）
需要支持display P3模式的设备。

### endless-tunnel
无尽隧道示例游戏。

### hello-cdep
演示cdep原生库分发系统使用。

### hello-gl2
使用GLES 2.0画三角的示例。




### nn_sample（API 27）
神经网络API基本用法。

### other-builds
CMake外的其他编译器。

### san-angeles
使用GLES API渲染程序场景。

### sensor-graph
读取当前加速度值，使用OpenGL进行绘制。
