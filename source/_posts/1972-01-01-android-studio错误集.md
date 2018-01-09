---
title: android studio错误集
comments: false
toc: true
date: 1972-01-01 08:00:00
tags:
	- android studio
	- errors
---

### Error:The SDK Build Tools revision (23.0.1) is too low for project ':app'. Minimum required is 25.0.0
**问题原因**：
android gradle plugin2.3.0及以上要求build tools大于等于25.0.0
**解决方案**：
升级SDK Build Tools到25及以上，或降低android gradle plugin版本

<!-- more -->

### java.io.IOException: The output jar is empty. Did you specify the proper '-keep' options?
**问题原因**：
SDK下build tools/lib中jar包为0Kb引发
**解决方案**：
查看指定版本对应的jar是否是0Kb，更换问题版本

### Duplicate files copied in APK META-INF/LICENSE
**解决方案**：
```
android {

    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/NOTICE.txt'
    }
}
```

### Plugin Error: required plugin “Android Support” is disabled
**解决方案**：
在Android Studio -> Setting -> Plugins菜单中启用 "Android Support" plugin。

### Error:com.android.builder.internal.aapt.AaptException: Failed to crunch file
**问题原因**：
项目路径过长或项目名称过长
**解决方案**：
项目路径上移，缩短项目路径或者项目名称

### &lt;interface declaration&gt;, &lt;parcelable declaration&gt;, AidlTokenType.import or AidlTokenType.package expected, got 'vertical'
**问题现场**：
LinearLayout控件中orientation属性vertical显示错误警告
**解决方案**：
检查android studio的File -> Setting -> Language & Frameworks 中是否有相应AIDL配置，删除即可

### 所有style都提示no resources found
**问题原因**：
strings.xml中格式化字符串格式错误
**解决方案**：
检查并修正格式化字符串

### 右键无git菜单
**问题现场**：
处于版本控制下的项目，在android studio中点击右键，无git菜单
**解决方案**：
检查android studio的File -> Setting -> Version Control中project是否处于版本控制下

### Error:Could not initialize class org.jetbrains.kotlin.kapt.idea.KaptModelBuilderService
**问题原因**：
gradle版本过低，在Android Studio 3.0中打开低版本as项目，使用的gradle版本为2.14.1，gradle插件版本为2.2.0
**解决方案**：
gradle版本修改为3.3版本

###  Caused by: java.util.zip.ZipException: invalid distance code
**问题现场**：
强行kill as进程，阻止自动更新gradle版本，将下载的gradle压缩包放到.gradle\wrapper\dists\gradle-4.1-all\bzyivzo6n839fup2jbap0tjew目录下，重启as后，出现该错误；
**解决方案**：
删除下载放置的gradle压缩包，执行as自动更新gradle版本解决；

###  Error:(119, 0) Cannot set the value of read-only property 'outputFile' for ...
**问题原因**：
gradle tools升级为3.0，outputFile变更为只读属性
**解决方案**：
1.  使用 all() 替换 each()
2.  使用 outputFileName 取代 只读output.outputFile
```
// If you use each() to iterate through the variant objects,
// you need to start using all(). That's because each() iterates
// through only the objects that already exist during configuration time—
// but those object don't exist at configuration time with the new model.
// However, all() adapts to the new model by picking up object as they are
// added during execution.
android.applicationVariants.all { variant ->
    // variant.outputs.each {
    variant.outputs.all {
        // output.outputFile = new File(outputFile.parent + "/${variant.buildType.name}", fileName)
        outputFileName = "${variant.name}-${variant.versionName}.apk"
    }
}
```

###  Error:Attribute meta-data#android.support.VERSION@value value=(26.0.0-alpha1)...
**问题现场**：
```
Error:
	Attribute meta-data#android.support.VERSION@value value=(26.0.0-alpha1) from [com.android.support:recyclerview-v7:26.0.0-alpha1] AndroidManifest.xml:24:9-38
	is also present at [com.android.support:appcompat-v7:25.3.1] AndroidManifest.xml:27:9-31 value=(25.3.1).
	Suggestion: add 'tools:replace="android:value"' to <meta-data> element at manifestMerger8182873823288498776.xml:22:5-24:41 to override.
```
**问题原因**：
存在不同版本的共享库文件
**解决方案**：
项目build.gradle中显式指定appcompat-v7依赖版本为26.0.0-alpha1

###  You should manually set the same version via DependencyResolution
**问题现场**：
```
Execution failed for task ':Hospital:preHuliDebugBuild'.
> Android dependency 'com.android.support:appcompat-v7' has different version for the compile (25.3.1) and runtime (26.0.0-alpha1) classpath. You should manually set the same version via DependencyResolution
```
**问题原因**：
项目build.gradle显式指定的依赖库版本与第三方库依赖的版本不一致引发
**解决方案**：
根build.gradle中，强制指定支持库版本
```
    configurations.all {
        resolutionStrategy.eachDependency { DependencyResolveDetails details ->
            def requested = details.requested
            if (requested.group == 'com.android.support') {
                if (!requested.name.startsWith("multidex")) {
                    //这里指定需要统一的依赖版本,这里统一为26.0.0-alpha1
                    details.useVersion '26.0.0-alpha1'
                }
            }
        }
    }
```

###  Error:(981) duplicate value for resource 'attr/title' with config ''.
**问题现场**：
```
Error:(981) duplicate value for resource 'attr/title' with config ''.
Error:(6, 5) error: resource previously defined here.
```
**问题原因**：
as由2.3. 3版本升级为3.0，资源打包工具由aapt变更为aapt2，自定义属性名与支持库已定义的属性名相同引发
**解决方案**：
修改自定义属性名，或去除该属性项。

###  Configuration 'compile' in project ':Hospital_IM' is deprecated. Use 'implementation' instead.
**问题现场**：
gradle tools升级为3.0
**解决方案**：
使用implementation代替compile即可
如果是Project(:library)依赖，如果appModule调用了library Module的依赖库的类，则将library Module中的implementation替换为api即可

###  Exception: OutofMemory.
**问题现场**：
设置java的版本为8
**解决方案**：
在根目录下的gradle.properties中配置jvm参数
```
org.gradle.jvmargs=-Xmx2048m -XX:+CMSClassUnloadingEnabled -XX:+CMSPermGenSweepingEnabled
```

### Error:All flavors must now belong to a named flavor dimension.
**问题现场**：
gradle tools升级为3.0，增加了必填配置项flavorDimensions
**解决方案**：
配置风味维度，如完整版、演示版
只有同一个维度下的product文件夹中可存在相同类
```
// Specifies two flavor dimensions.
flavorDimensions "tier", "minApi"

productFlavors {
     free {
      // Assigns this product flavor to the "tier" flavor dimension. Specifying
      // this property is optional if you are using only one dimension.
      dimension "tier"
      ...
    }

    paid {
      dimension "tier"
      ...
    }

    minApi23 {
        dimension "minApi"
        ...
    }

    minApi18 {
        dimension "minApi"
        ...
    }
}
```

### Failed to execute aapt
**问题现场**：
执行Junit Test
```
Error:Gradle: failed to create directory 'E:\wujing\as3\git\MyKotlinApp\app\build\generated\source\r\debug\com\example\admin\mykotlinapp'.
Error:Gradle: java.util.concurrent.ExecutionException: java.util.concurrent.ExecutionException: com.android.tools.aapt2.Aapt2Exception: AAPT2 error: check logs for details
Error:Gradle: java.util.concurrent.ExecutionException: com.android.tools.aapt2.Aapt2Exception: AAPT2 error: check logs for details
Error:Gradle: com.android.tools.aapt2.Aapt2Exception: AAPT2 error: check logs for details
Error:Gradle: Execution failed for task ':app:processDebugResources'.
> Failed to execute aapt
```
**解决方案**：
禁用aapt2
```
// gradle.properties
android.enableAapt2=false
```

##### 引入jar包后，还是找不类
**解决方案**：
在Project视图下，选择要引入的jar包，打开右键菜单，点击“add as Library”命令。
