---
title: Android studio启用Kotlin
comments: false
toc: true
date: 2017-11-27 17:24:39
tags:
	- kotlin
	- android studio
---

### IDE
android studio 3.0，已自动安装kotlin插件

### 编译插件
kotlin-android-extensions用于view绑定
``` gradle
classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
classpath "org.jetbrains.kotlin:kotlin-android-extensions:$rootProject.ext.kotlin_version"
```

### 模块应用插件
``` gradle
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
```

### kotlin标准库
``` gradle
kotlin_stdlib: "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version",
// 针对 JDK 7 或 JDK 8的扩展版本
kotlin_stdlib_jre7: "org.jetbrains.kotlin:kotlin-stdlib-jre7:$kotlin_version",
kotlin_stdlib_jre8: "org.jetbrains.kotlin:kotlin-stdlib-jre8:$kotlin_version",
```

### 其他依赖库
##### kotlin扩展库
``` gradle
compile "org.jetbrains.kotlin:kotlin-reflect"
testCompile "org.jetbrains.kotlin:kotlin-test"
testCompile "org.jetbrains.kotlin:kotlin-test-junit"
```

##### anko-coroutines
基于kotlinx.coroutines库
``` gradle
anko_common: "org.jetbrains.anko:anko-common:$anko_version",
anko_coroutines: "org.jetbrains.anko:anko-coroutines:$anko_version",
```

### 源代码目录
默认目录是src/main/java，可配置
``` gradle
android {
	……

	sourceSets {
		main.java.srcDirs += 'src/main/kotlin'
	}
}
```

