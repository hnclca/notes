---
title: android sdk错误集
comments: false
toc: true
date: 1972-01-01 08:00:00
tags:
	- android
	- errors
---

[TOC]

### android:imeOptions="actionSearch"属性不生效
**问题现场**：
弹出软键盘回车键不是搜索图标。
**运行环境**：
emulator API 23
**解决方案**：
同时指定android:inputType属性
```
android:imeOptions="actionSearch"
android:inputType="textNoSuggestions"
```

<!-- more -->
