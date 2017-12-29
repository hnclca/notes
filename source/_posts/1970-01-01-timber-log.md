---
title: timber.log
comments: false
toc: true
tags:
  - log
  - android
  - references
date: 1970-01-01 08:00:00
---

[Timber](https://github.com/JakeWharton/timber)是小型、扩展性的日志记录API，基于安卓Log日志类的工具类。
由Tree实例完成记录工作，使用Timber.plant安装tree实例，实例安装应该尽可能早，推荐在Application的onCreate()执行。
DebugTree实现类会自动使用被调用的类的类名作为日志标签。
Tree实现不会默认安装，因为正式版中，调试器不能用。

**依赖库**:
``` java
implementation 'com.jakewharton.timber:timber:4.6.0'
```

**使用**:
1.	在Application的onCreate方法中安装Tree实例；
2.	在App内需要记录的任何地方，调用Timber的静态方法。

<!-- more -->
#### Classes
##### Timber
**摘要**：
懒人专用记录器。

**库名**：
timber.log

**公有方法**：
``` java
// 安装、移除、查看Tree实例。
static void	plant(Timber.Tree tree)
static void	uproot(Timber.Tree tree)
static void	uprootAll()
static Timber.Tree	asTree()

// 设置下一次日志调用时的日志标签（一次性）
static Timber.Tree	tag(String tag)

// 日志记录方法。
static void	d(String message, Object... args)
static void	d(Throwable t, String message, Object... args)
static void	e(String message, Object... args)
static void	e(Throwable t, String message, Object... args)
static void	i(String message, Object... args)
static void	i(Throwable t, String message, Object... args)
static void	v(String message, Object... args)
static void	v(Throwable t, String message, Object... args)
static void	w(String message, Object... args)
static void	w(Throwable t, String message, Object... args)
static void	wtf(String message, Object... args)
static void	wtf(Throwable t, String message, Object... args)
```

##### Timber.Tree
**摘要**：
处理日志调用的门面类。

**库名**：
timber.log

**直接子类**：
*	Timber.DebugTree

**构造器**：
``` java
Tree()
```

**公有方法**：
``` java
void	d(String message, Object... args)
void	d(Throwable t, String message, Object... args)
void	e(String message, Object... args)
void	e(Throwable t, String message, Object... args)
void	i(String message, Object... args)
void	i(Throwable t, String message, Object... args)
void	v(String message, Object... args)
void	v(Throwable t, String message, Object... args)
void	w(String message, Object... args)
void	w(Throwable t, String message, Object... args)
void	wtf(String message, Object... args)
void	wtf(Throwable t, String message, Object... args)
```

**可继承方法**：
``` java
// 写入日志到目的地。
protected abstract void	log(int priority, String tag, String message, Throwable t)
```

##### Timber.DebugTree
**摘要**：
调试版Tree，自动从调用类中获取日志标签。

**库名**：
timber.log

**继承关系**：
*	Timber.Tree

**构造器**：
``` java
DebugTree()
```

**可继承方法**：
``` java
// 从堆栈信息中获取日志标签。
protected String	createStackElementTag(StackTraceElement element)
```
