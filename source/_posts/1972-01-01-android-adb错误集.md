---
title: android-adb错误集
comments: true
toc: true
date: 1972-01-01 08:00:00
tags:
	- adb
	- errors
---
### XXXXX	no permissions (verify udev rules)
**问题现场**：
ubuntu系统上执行adb devices
**运行环境**：
64位ubuntu系统
**问题原因**：
udev访问需要有root权限
**解决方案**：
1. 查询手机当前手机设备的供应商ID(idVendor)
``` shell
    // 对比插上手机与未插手机有差别的那个，这里是小米手机
    $ lsusb
    ...
    Bus 003 Device 006: ID 18d1:9025 Google Inc. 
    ...
```
2. 配置udev规则并配置访问权限
``` shell
    // 创建或打开android udev rules文件
    $ sudo vim /etc/udev/rules.d/51-android.rules
    // 添加当前手机设备信息，注意这里只能使用“号不能使用‘
    SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", GROUP="plugdev" 
    // 配置全局可读访问权限
    $ sudo chmod a+r /etc/udev/rules.d/51-android.rules
```
3. 插拔手机，再次连接手机，调用adb devices即可解决。

<!-- more -->

**参考资料**
[在硬件设备上运行应用](https://developer.android.google.cn/studio/run/device.html)

