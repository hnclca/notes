---
title: anddroid.arch.core.executor.testing
comments: false
toc: true
tags:
  - android
  - references
date: 1970-01-01 08:00:00
---

CountingTaskExecutorRule -- 使用计数任务执行器替换架构组件中的后台执行器
InstantTaskExecutorRule -- 使用同步任务执行器替换架构组件中的后台执行器

<!-- more -->
#### Classes
##### CountingTaskExecutorRule
**摘要**：
JUnit Test Rule使用计数任务执行器替换架构组件中的后台执行器。

**库名**：
android.arch.core:core-testing:1.0.0

**继承关系**：
*	org.junit.rules.TestWatcher

**构造器**：
``` java
CountingTaskExecutorRule()
```

**公有方法**：
``` java
// 等待直到所有活动任务结束
void	drainTasks(int time, TimeUnit timeUnit)
boolean	isIdle()
```

**可继承方法**：
``` java
// 在等待任务数为0时调用。
void	onIdle()
```


##### InstantTaskExecutorRule
**摘要**：
JUnit Test Rule使用同步任务执行器替换架构组件中的后台执行器。

**库名**：
android.arch.core:core-testing:1.0.0

**继承关系**：
*	org.junit.rules.TestWatcher

**构造器**：
``` java
InstantTaskExecutorRule()
```