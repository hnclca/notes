---
title: Architecture Components
comments: true
toc: true
date: 2017-12-18 15:57:48
tags:
	- library
	- android
---

安卓架构组件是一系列帮助开发者设计健壮性、易测试、可维护的apps的库的集合。
![image](/assets/images/2017/12/recommanded-architecture.png)

<!-- more -->

### 添加依赖库

#### 添加谷歌Maven仓库
``` gradle
allprojects {
    repositories {
        jcenter()
        google()
    }
}
```

#### 添加架构组件
##### 生命周期感知Lifecycle-aware
``` gradle
implementation "android.arch.lifecycle:runtime:1.0.3"
annotationProcessor "android.arch.lifecycle:compiler:1.0.0"

// 或者
implementation "android.arch.lifecycle:common-java8:1.0.0"
```

##### LiveData和ViewModel
``` gradle
// 该库已包含android.arch.lifecycle:runtime库
implementation "android.arch.lifecycle:extensions:1.0.0"

// 使用ReactiveStreams API
implementation "android.arch.lifecycle:reactivestreams:1.0.0"

// 在测试中控制后台线程
testImplementation "android.arch.core:core-testing:1.0.0"
```

##### 持久化Room
``` gradle
implementation "android.arch.persistence.room:runtime:1.0.0"
annotationProcessor "android.arch.persistence.room:compiler:1.0.0"

// 测试Room迁移
testImplementation "android.arch.persistence.room:testing:1.0.0"

// 支持RxJava
implementation "android.arch.persistence.room:rxjava2:1.0.0"
```

##### 分页Paging
``` gradle
implementation "android.arch.paging:runtime:1.0.0-alpha4-1"
```

### 包介绍
*	android.arch.core.executor.testing
计数任务与同步任务执行器

*	android.arch.core.util
Function函数接口

*	android.arch.lifecycle
生命周期感知组件Lifecycle、数据持有组件LiveData、数据管理组件ViewModel

*	android.arch.paging
分页数据源DataSource, 分页数据列表PagedList, 分页数据适配器PagedListAdapter

*	android.arch.persistence.db
RoomDatabase，替代SQLiteDatabase

*	android.arch.persistence.db.framework
SupportSQLiteOpenHelper.Factory实现类

*	android.arch.persistence.room
数据库对象映射库Room: RoomDatabase基类，注解标识Database, Entities, Dao抽象类（注解处理器实现）

*	android.arch.persistence.room.migration
Migration基类

*	android.arch.persistence.room.testing
MigrationTestHelper迁移测试辅助类

*	android.support.v7.recyclerview.extensions
DiffUtil相关扩展

### 问题
#### Schema export directory is not provided to the annotation processor
##### 错误信息
```
Error:(33, 17) 警告: Schema export directory is not provided to the annotation processor so we cannot export the schema. You can either provide `room.schemaLocation` annotation processor argument OR set exportSchema to false.
```

##### 解决方案
1. 禁用exportSchema
``` java
@Database(entities = { YourEntity.class }, version = 1, exportSchema = false)
public abstract class AppDatabase extends RoomDatabase {

   ...

}
```

2. 配置导出路径
``` gradle
android {
    // ... (compileSdkVersion, buildToolsVersion, etc)

    defaultConfig {

        // ... (applicationId, miSdkVersion, etc)

        javaCompileOptions {
            annotationProcessorOptions {
                arguments = ["room.schemaLocation": "$projectDir/schemas".toString()]
            }
        }
    }

    // ... (buildTypes, compileOptions, etc)
}
```
