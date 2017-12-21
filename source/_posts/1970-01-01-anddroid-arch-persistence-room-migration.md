---
title: anddroid.arch.persistence.room.migration
comments: false
toc: true
tags:
  - android
  - references
date: 1970-01-01 08:00:00
---

Migration -- 迁移方案基类

<!-- more -->
#### Classes
**摘要**：
数据库迁移方案基类。
每个迁移方案限定在两个版本间，由startVersion and endVersion指定。
允许跳跃迁移，即允许指定3到5迁移方案，而不一定强制要求从3到4，再4到5。
如果Room找不到当前版本到最新版间的迁移方案，会清除再重建数据库，因此即使两个版本间无改变，同样需要提供一个迁移方案给构造器。

**库名**：
android.arch.persistence.room:runtime:1.0.0

**构造器**：
``` java
Migration(int startVersion, int endVersion)
```

**公有属性**：
``` java
public final int	startVersion
public final int	endVersion
```

**公有方法**：
``` java
abstract void	migrate(SupportSQLiteDatabase database)
```