---
title: android sdk错误集
comments: false
toc: true
date: 1972-01-01 08:00:00
tags:
	- android
	- errors
---

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

### RecyclerView空白不显示数据
**问题现场**：
RecyclerView的列表空白

**问题原因**：
未设置app:layoutManager属性

**解决方案**：
``` XML
app:layoutManager="LinearLayoutManager"
```

### Unable to open trace file '/sdcard/*.trace': Permission denied
**问题现场**：
调用Debug类记录函数调用跟踪日志。

**问题原因**：
API 23的emulator对外存根目录无写权限。

**解决方案**：
修改日志文件路径为外存Android/data/应用对应目录。
``` java
val file = getExternalFilesDir(null)
Debug.startMethodTracing(file!!.toString() + "/dmtrace.trace")
```