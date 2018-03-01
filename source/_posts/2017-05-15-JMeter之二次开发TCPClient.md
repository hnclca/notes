---
title: JMeter之二次开发TCPClient
comments: true
toc: true
date: 2017-05-15 17:32:32
tags:
	-	testing
	-	tools
---

### 开发环境
1、JDK 1.8
2、Eclipse for Java 3.9以上版本
3、Ant

### 配置JMeter源代码开发环境（基于JMeter3.2）
1、下载源代码包，并解压;
2、进入源代码目录，删除.classpath文件，重命名eclipse.classpath文件为.classpath;
3、打开CMD，在源代码路径下执行ant download_jars, ant install;
4、启动Eclipse, 点击File->Open Projects from File System, 导入源代码;
5、运行Run Configurations, Main选项卡中指定Main Class为org.apache.jmeter.NewDriver，Arguments选项卡中指定working directory为项目下的build目录；
6、运行项目，若JMeter正常启动，说明源代码环境配置成功；

<!-- more -->

### 自定义TCPClient
##### 学习内置的TCPClientImpl(src/protocol/tcp分类)源代码；
1、核心类TCPSample
其负责GUI界面元素值的读取，动态实例化TCPClient具体类，Socket的创建与关闭，SampleResult即测试结果的格式化输出；
2、协议类TCPClient
基类TCPClient，抽象类AbstractTCPClient，内置实现类TCPClientImpl（简单文本）、BinaryTCPClientImpl（不定长二进制数据）和LengthPrefixedBinaryTCPClientImpl（定长二进制数组）；
3、界面类TCPSamplerGui
TCPSampler的Gui界面类；
4、TCP配置参数界面TCPConfigGui
这里可以对TCP配置参数界面进行修改，增删界面控件，读取控件值；
##### 新建CustomTCPClient类，继承AbstractTCPClient类，实现抽象方法；
1、void write(OutputStream os, String s) throws IOException;
将参数界面中要发送的文本，按照协议格式，写入到os中，即完成请求数据的发送；
2、String read(InputStream is) throws ReadException;
读取is中数据，返回编码后的String，即完成响应数据的接收；
3、void write(OutputStream os, InputStream is) throws IOException;
write的重载方法，使用其他输入流向服务端socket发送数据；

### 中文数据的编码问题
1、setCharset指定编码；
2、字符串与字节数组互转时指定编码；

### 全双工通信导致的测试失败问题
1、在write方法中增加随机码发送到服务端，服务端返回该接口数据时带上随机码（需要服务器配合）；
2、在read方法中增加while循环，验证收到的随机码是否与发送的一致，一致则返回String，不一致则继续等待；
3、若担心陷入持续等待，可指定超时时间；

### 增加Logger日志
1、初始化Logger，参考内置实现类；
2、调用log.XXX输出关键信息；
3、日志默认输出级别为INFO，不包含debug信息，修改bin/log4j2.xml，添加需要显示调试类的过滤规则；
``` xml
<Logger name="com.example" level="debug" />  
```
4、重新启动JMeter就可以查看调试信息了