---
title: anddroid.arch.persistence.room.testing
comments: false
toc: true
tags:
  - android
  - references
date: 1970-01-01 08:00:00
---

MigrationTestHelper -- 数据库迁移测试辅助类

<!-- more -->
#### Classes
**摘要**：
用于仪器测试，辅助创建旧版的数据库。
拷贝由注解处理器根据room.schemaLocation创建的schema json文件到测试资源目录，并传递该路径到构造器中。
该类会从该路径读取schema json文件。
``` gradle
 android {
   defaultConfig {
     javaCompileOptions {
       annotationProcessorOptions {
         arguments = ["room.schemaLocation": "$projectDir/schemas".toString()]
       }
     }
   }
   sourceSets {
     androidTest.assets.srcDirs += files("$projectDir/schemas".toString())
   }
 }
```

**库名**：
android.arch.persistence.room:testing:1.0.0

**继承关系**：
*	org.junit.rules.TestWatcher

**构造器**：
``` java
MigrationTestHelper(Instrumentation instrumentation, String assetsFolder)
MigrationTestHelper(Instrumentation instrumentation, String assetsFolder, SupportSQLiteOpenHelper.Factory openFactory)
```

**公有方法**：
``` java
// 创建指定版本的数据库。
SupportSQLiteDatabase	createDatabase(String name, int version)

// 注册数据库，使其在测试结束后自动关闭。
void	closeWhenFinished(SupportSQLiteDatabase db)
void	closeWhenFinished(RoomDatabase db)

// 在指定数据库上执行迁移。迁移完成后，验证迁移结果匹配期望schema。
// 删除数据表的处理取决于validateDroppedTables参数。如果设置为true，数据库出现未知表时，验证失败。
SupportSQLiteDatabase	runMigrationsAndValidate(String name, int version, boolean validateDroppedTables, Migration... migrations)
```
