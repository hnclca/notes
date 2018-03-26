---
title: Agile Android
comments: true
toc: true
date: 2018-03-07 15:22:56
tags:
	- android
	- testing
---

[TOC]

<!-- more -->

### 介绍
#### 敏捷开发的益处
1. 开发前期捕获更多错误
2. 自信应对改变
3. 回归测试构建
4. 扩展代码库生命

#### 敏捷测试金字塔
* GUI测试
* API或后端接口测试
* 单元测试

### 单元测试
#### 安卓断言

* assertEquals
* assertTrue/False
* assertNull/NotNull
* assertSame/assertNotSame
* assertThat
* fail

#### 命令行

``` bash
$ ./gradlew test --continue
```

#### JUnit选项

* @Before
* @After
* @Test[(timeout=ms)]
* @BeforeClass
* @AfterClass

#### HTML输出
默认输出路径：project/app/build/test-results/debug

#### 组测试
根据测试执行时间进行分组。
使用模拟数据库或网络基于方法的单元测试为小型测试，需要模拟器或设备的Espresso测试被视为中或大型测试。

* @SmallTest
* @MediaTest
* @LargeTest

#### 参数测试
动态传入测试数据，而不是硬编码测试数据。

* @RunWith(Parameterized.class)标记参数测试类
* @Parameters创建测试参数集合
* 添加有参构造器
* 在测试方法中使用参数

### 第三方工具
#### Hamcrest断言
扩展JUnit4断言。

##### Package

* CoreMatchers
* Matchers
* Condition -- and/matched/matching/notMatched/then
* MatcherAssert -- assertThat

#### JaCoCo
找出未测试的指令和分支。

* 应用jacoco插件
* jacoco中指定jacoco版本 -- 可手动下载依赖
* debug构建中开启testCoverageEnabled
* 编写代码覆盖率任务

#### Mickito
模拟网络连接或文件系统、数据库读取。避免外部原因导致测试失败。

#### Robolectric
代替模拟器或测试执行activity测试。
属于单元测试。

#### Jenkins
持续集成工具，完成多步骤任务。

##### 安装
下载通用应用包，放到Tomcat/wabapps目录下，启用Tomcat即可。

##### 配置Jenkins
管理Jenkins模块的管理插件配置插件 -- Git和Gradle
管理Jenkins模块的配置系统设置系统环境变量 -- ANDROID_HOME(sdkpath)

##### 创建自动任务
创建自由风格任务，使用Git插件配置源代码，配置gradle构建脚本。
```
switches: --refresh-dependencies --profile
tasks: assemble
``

### 模拟
模拟对象及对象行为。

``` java
Foo foo = Mockito.mock(Foo.class);
Mockito.when(foo.bar()).thenReturn("true");
```

#### 共享配置
模拟SP读取，可以将设备测试转化为单元测试。

#### 时间
模拟接口类。
面向接口编程，TimeChange类依赖于Clock接口类，模拟Clock接口类，而不是实现类。

#### 系统属性
模拟安卓内置功能，如AudioManager。

#### 数据库
模拟SQLHelper或相似类及CRUD行为。

#### Jenkins
增加Gradle构建任务。
```
tasks: test --continue
```

### Espresso
用于设备测试。

``` java
@Rule
ActivityTestRule

// 视图匹配
onView(ViewMatcher)
	.perform(ViewAction)
    .check(ViewAssertion);

// 数据匹配，适用于AdapterView类视图
onData(ObjectMatcher)
	.DataOptions
    .perform(ViewAction)
    .check(ViewAssertion)
```

#### 视图匹配

* 用户属性 -- withId, withText, hasLinks...
* UI属性 -- isDisplayed, isEnabled, isSelected...
* 对象匹配 -- allOf, anyOf, is, not, endsWith...
* 视图层次 -- withParent, withChild, isRoot, hasDescendant...
* 输入 -- hasIMEAction, supportsInputMethods
* 类 -- isAssignableFrom, withClassName
* 根匹配 -- isFocusable, isTouchable, isDialog, isPlatformPopup...

#### 视图动作

* 点击/按压 -- click, pressBack, closeSoftKeyboard, openLink...
* 手势 -- scrollTo, swipeLeft/Right/Up/Down
* 文本 -- clearText, typeText, typeTextIntoFocusedView, replaceText

#### 视图断言

* 布局断言 -- noEllipsizedText, noMultilineButtons, noOverlaps
* 位置断言 -- isLeft/R/A/BOf, isLeft/R/T/BAlignedWith
* 其他 -- matches, doesNotExist, selectedDescendentsMatch

#### 数据选项

* 根匹配 -- inRoot
* AdapterView -- inAdapterView, onChildView, usingAdapterViewProtocol
* 位置匹配 -- atPosition

#### Jenkins

* gradle任务 -- connectedCheck
* 模拟器插件 -- 构建环境中配置

### 测试驱动开发
#### TDD阶段

1. 编写测试用例 -- 不通过
2. 实现功能 -- 通过
3. 重构移除不良代码 -- 通过

### 处理遗留代码

#### 引入测试步骤

1. 引入CI
2. 配置TDD环境
3. 添加小型单元测试，并配置到CI服务器
4. 添加代码覆盖率报告
5. 添加Espresso设备测试
6. 使用mock功能添加新功能单元测试
7. 添加新功能时，隔离遗留代码 -- 接口隔离
8. 移除未使用代码
9. 重构遗留代码，使测试代码覆盖率达到60-70%


#### SonarQube
代码质量分析工具。
SQLAE -- Software Quality Assessment based on Lifecycle Expectations。

##### 安装

官网地址：[]()
web地址：http://localhost:9000
账号：admin/admin -- 引导时让输入用户名会分配一个token，注意保存。

##### 配置数据库
不推荐MySQL，步骤见官网。

##### 配置项目
使用plugins导入时要至于apply plugin前。

``` gradle
// 根构建文件
dependencies {
	classpath "org.sonarsource.scanner.gradle:sonarqube-gradle-plugin:2.6.2"
}

// 子项目构建文件
apply plugin: "org.sonarqube"
sonarqube {
    properties {
        property 'sonar.projectName', 'Example of SonarQube Scanner for Gradle Usage'
    }
}
```

##### gradle命令
``` bash
$ ./gradlew sonarqube \
	-Dsonar.host.url=http://localhost:9000 \
    -Dsonar.login=2f1fa4e44f8a939a026d7b0e060e9a916835c67a
```

##### 配置Android Lint插件
在Administration中Market中搜索配置。

``` gradle
property 'sonar.profile.java', 'Android Lint'
```

### Jenkis

1. Git检出代码
2. 应用配置
3. 编译
4. 执行单元测试和设备测试
5. 打包并上传Nexus
6. 触发代码质量分析