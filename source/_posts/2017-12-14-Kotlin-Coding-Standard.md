---
title: Kotlin Coding Standard
comments: true
toc: true
date: 2017-12-14 13:39:20
tags:
	- kotlin
	- coding standard
---

### 源文件
使用UTF-8编码

#### 命名
如果源文件只包含一个顶层类，则文件名等于类名 + ".kt"，否则使用概括多个顶层定义的有意义的命名作为文件名，文件名采用驼峰式，".kt"扩展名结尾。

``` kotlin
// Foo.kt
class Foo { }

// Bar.kt
class Bar { }
fun Runnable.toBar(): Bar = // …

// Map.kt
fun <T, O> Set<T>.map(func: (T) -> O): List<O> = // …
fun <T, O> List<T>.map(func: (T) -> O): List<O> = // …
```

<!-- more -->

#### 特殊字符
##### 空白字符
只有单个空格，即0x20被允许使用，这意味着：
*	除空格外的所有空白字符都需要转义才能使用；
*	制表符不能用于缩进；

##### 转义字符
优先使用如下转义字符，而不是Unicode转义。
``` kotlin
\b, \n, \r, \t, \', \", \\, and \$
```

##### 非ASCII字符
基于易读性考虑，可打印字符不允许使用Unicode转义。

``` kotlin
// Best: 非常清晰，无需注释.
val unitAbbrev = "μs"

// Poor: 无理由对可打印字符进行Unicode转义.
val unitAbbrev = "\u03bcs" // μs

// Poor: 完全不知道字符含义.
val unitAbbrev = "\u03bcs"

// Good: 对不可打印字符进行Unicode转义，必要时可加上注释.
return "\ufeff" + content
```

#### 结构
区块组成顺序，用空白行间隔。
1.	版权或许可证书头（可选）
1.	文件级注释
1.	包语句
1.	导入语句
1.	顶层声明

##### 版权或许可证书
必须至于最前面，不要使用文档注释符，或单行注释符。
``` kotlin
/*
 * Copyright 2017 Google, Inc.
 *
 * ...
 */
```

``` kotlin
/**
 * Copyright 2017 Google, Inc.
 *
 * ... WRONG
 */
// Copyright 2017 Google, Inc.
//
// ... WRONG
```

##### 文件级注释
``` kotlin
@file:JvmName("Foo")

package org.jetbrains.demo
```

##### 包语句
包语句不受限于行字符数控制，且不能折行。

##### 导入语句
*	类、方法、属性分类组合在一起，按ASCII字符顺序排列；
*	通配符不被允许；
*	跟包语句一样，不受限于行字符数控制，且不能折行。

##### 顶层声明
文件内可声明一个或多个顶层类、顶层方法、顶层属性、类型别名。
*	文件的内容应该集中于一个主题，比如一个单独的公有类，或多个接收类型执行相同操作的扩展方法的集合。不想关的定义应该分开到各自的文件中，单个文件中的公有定义应尽可少；
*	数量与顺序没有明显的限制；
*	文件一般从上往下读取，抽象度高声明先于抽象度高低声明。不同的文件安排内容的顺序不同，类似的，一个文件可能包含100个属性或100个方法或一个单独类；
*	最好使用有解释性的逻辑顺序，而不是按时间顺序进行排序；

##### 类成员顺序
遵守跟顶层声明的规则。

### 格式
#### 大括号
对于when分支或没有其他分支的if语句，可在一行写下时，可省略大括号。
``` kotlin
if (string.isEmpty()) return

when (value) {
    0 -> return
    // …
}
```
对于其他的if, for, when分支和do, while语句, 即使空执行体或只有一行语句也要带上大括号。
``` kotlin
if (string.isEmpty())
    return  // WRONG!

if (string.isEmpty()) {
    return  // Okay
}
```

##### 块
块结构的大括号遵守如下K&R风格：
1.	左大括号前不断行
2.	左大括号后断行
3.	右大括号前断行
4.	作为语句或方法、构造器、命名类的方法体的终结符的右大括号后断行

``` kotlin
return object : MyClass() {
    override fun foo() {
        if (condition()) {
            try {
                something()
            } catch (e: ProblemException) {
                recover()
            }
        } else if (otherCondition()) {
            somethingElse()
        } else {
            lastThing()
        }
    }
}

// empty block
try {
    doSomething()
} catch (e: Exception) {
} // Okay
```
可单行表示的枚举类例外
``` kotlin
enum class Answer { YES, NO, MAYBE }
```

##### 表达式
仅当if/else表达式单行表示时，可省略大括号
``` kotlin
val value = if (string.isEmpty()) 0 else 1  // Okay
val value = if (string.isEmpty())  // WRONG!
                0
            else
                1
val value = if (string.isEmpty()) { // Okay
    0
} else {
    1
}
```


#### 一行一个语句
每个语句后断行，无须分号。
``` kotlin
override fun toString(): String = "Hey"
```

#### 折行
每行限制100个字符，以下情况例外：
*	不能遵守行限制情况，如过长的URL；
*	包语句与导入语句；
*	可能剪切粘贴到shell中的命令语句；

##### 何处折行
折行的目标在于清晰代码结构，而不是追求最少代码行数。
1.	非赋值操作符在符号之前，同样适用于类似操作符的点运算符(.)、引用绑定(::)；
2.	赋值操作符跟在符号之后；
3.	方法名或构造器名后跟着左圆括号；
4.	逗号跟在符号之后；
5.	lambda箭头(->)跟在参数列表之后；


#### 缩进
##### 块缩进
当有新的块结构产生时，缩进增加4个空格，块结构结束，回归之前的缩进。缩进水平适用于块中代码与注释。

##### 连续缩进
当出现折行，折行后语句使用连续缩进，即相对第一行缩进8个空格；
``` kotlin
fun <T> Iterable<T>.joinToString(
        separator: CharSequence = ", ",
        prefix: CharSequence = "",
        postfix: CharSequence = ""
): String {
    // …
}

private val defaultCharset: Charset? =
        EncodingRegistry.getInstance().getDefaultCharsetForPropertiesFiles(file)

// 属性的get/set访问器不使用连续缩进
var directory: File? = null
    set(value) {
        // …
    }
```

#### 空白符
##### 纵向
*	连续的属性、构造器、方法、嵌套类之间；
	*	连续属性间的空行是可选的。因为这里的空行主要用于逻辑分组或联系属性及其幕后属性；
	*	枚举类的常量如果不带块结构，也可省去空行；
*	语句之间，用于逻辑分组；
*	块结构的第一行之前或最后一行之后的空行是可选的；
*	顶层声明之间；

##### 横向
除了字面量、注释、文档注释、单个ASCII空格，语言或格式规则外，空白符也出现在如下地方：
*	保留字与左圆括号间；
``` kotlin
// WRONG!
for(i in 0..1) {
}
// Okay
for (i in 0..1) {
}
```

*	保留字与大括号之间；
``` kotlin
// WRONG!
}else {
}
// Okay
} else {
}
```

*	左大括号之前；
``` kotlin
// WRONG!
if (list.isEmpty()){
}
// Okay
if (list.isEmpty()) {
}
```

*	运算操作符左右，点运算符、引用绑定符除外；
``` kotlin
// WRONG!
val two = 1+1
// Okay
val two = 1 + 1
// 同样适用于lambda箭头
// WRONG!
ints.map { value->value.toString() }
// Okay
ints.map { value -> value.toString() }
```

*	代表继承关系时，冒号(:)前才有空白符；
``` kotlin
// WRONG!
class Foo: Runnable
// Okay
class Foo : Runnable
// WRONG
fun <T: Comparable> max(a: T, b: T)
// Okay
fun <T : Comparable> max(a: T, b: T)
// WRONG
fun <T> max(a: T, b: T) where T: Comparable<T>
// Okay
fun <T> max(a: T, b: T) where T : Comparable<T>
```

*	逗号(,)之后
``` kotlin
// WRONG!
val oneAndTwo = listOf(1,2)
// Okay
val oneAndTwo = listOf(1, 2)
```

*	单行注释符，双斜线前后；
``` kotlin
// WRONG!
var debugging = false//disabled by default
// Okay
var debugging = false // disabled by default
```

#### 特殊结构
##### 枚举
``` kotlin
enum class Answer { YES, NO, MAYBE }

enum class Answer {
    YES,
    NO,

    MAYBE {
        override fun toString() = """¯\_(ツ)_/¯"""
    }
}
```

##### 注解
``` kotlin
// 有参注解，占据一行；
@Retention(SOURCE)
@Target(FUNCTION, PROPERTY_SETTER, FIELD)
annotation class Global
Annotations without arguments can be placed on a single line.

// 无参多个注解，共在一行；
@JvmField @Volatile
var disposable: Disposable? = null

// 无参单个注解，与代码共在一行；
@Volatile var disposable: Disposable? = null

@Test fun selectAll() {
    // …
}
```

##### 隐式返回或属性类型
如果是单个表达式符合智能推断规则，可省去类型声明；
如果是库中的公共API，则保持显式类型声明；
``` kotlin
override fun toString(): String = "Hey"
// becomes
override fun toString() = "Hey"
private val ICON: Icon = IconLoader.getIcon("/icons/kotlin.png")
// becomes
private val ICON = IconLoader.getIcon("/icons/kotlin.png")
```

### 命名
仅限字母和数字，小部分例外情况允许下划线，所以的标识符匹配正则表达式```\w+```
特殊的前缀或后缀，例如```name_, mName, s_name, and kName```，仅允许出现在幕后属性中

#### 包
包名全部为小写字母，连续的单词直接放连在一起，不用下划线间隔
``` kotlin
// Okay
package com.example.deepspace
// WRONG!
package com.example.deepSpace
// WRONG!
package com.example.deep_space
```

#### 类型
帕斯卡风格(PascalCase)
##### 类
名词或名词短语，比如```Character or ImmutableList```

##### 接口
可能是名词或名词短语，如```List```，有时也会是形容词或形容词短语， 比如```Readable```

##### 测试类
被测试类的类名加后缀Test，比如```HashTest or HashIntegrationTest```

#### 方法
动词或动词短语，比如``` sendMessage or stop```
下划线仅允许出现在测试方法中，以分隔逻辑组件
``` kotlin
@Test fun pop_emptyStack() {
    // …
}
```

#### 常量
名词或名词短语，字母全部大写，用下划线分隔
##### 何为常量
常量要求，val只读属性，无自定义访问器，其内容全部不可变，其方法不存在可检测到的副作用。这包括不可变类型、不可变类型构成的不可变集合、可标记为```const```字面量和字符串。
如果任一个实例的观察状态可改变，则不是常量，而不仅仅是不改变对象。
常量主要定义在```object```中或作为顶层声明，满足常量要求的类中属性也可以。
字面量值必须使用```constant```修饰符。
``` kotlin
const val NUMBER = 5
val NAMES = listOf("Alice", "Bob")
val AGES = mapOf("Alice" to 35, "Bob" to 32)
val COMMA_JOINER = Joiner.on(',') // Joiner is immutable
val EMPTY_ARRAY = arrayOf<SomeMutableType>()
```

#### 非常量名
驼峰式风格（camelCase），同样适用于实例属性、局部属性和参数名称。
名词或名词短语
``` kotlin
val variable = "var"
val nonConstScalar = "non-const"
val mutableCollection: MutableSet<String> = HashSet()
val mutableElements = listOf(mutableInstance)
val mutableValues = mapOf("Alice" to mutableInstance, "Bob" to mutableInstance2)
val logger = Logger.getLogger(MyClass::class.java.name)
val nonEmptyArray = arrayOf("these", "can", "change")
```

##### 幕后属性
如果需要幕后属性，其名称一般是下划线加上对应属性名称
``` kotlin
private var _table: Map<String, Int>? = null

val table: Map<String, Int>
    get() {
        if (_table == null) {
            _table = HashMap()
        }
        return _table ?: throw AssertionError()
    }
```

#### 类型变量名
*	单个大写字母，后面可跟随单个数字，如```E, T, X, T2```
*	当限于某个类使用时，名称为该类名加后缀T，如```RequestT, FooBarT```

#### 驼峰式
英语短语转换为驼峰式步骤：
1.	转换为纯ASCII字符，如```Müller’s algorithm```转换为```Muellers algorithm```
2.	用空格和标点符号（主要是连字符）切割短语为单词组
	*	任何单词习惯用法就是驼峰式时，也要切割开，如```AdWords```切割为```ad words```
3.	将所有单词都转换为小写，包括缩写单词，大写首字母
4.	连接所有单词组成标识符

|Prose form	| Correct | Incorrect|
|:-:|:-:|:-:|
|“XML Http Request”	| XmlHttpRequest|XMLHTTPRequest|
|“new customer ID”|newCustomerId|newCustomerID|
|“inner stopwatch”|innerStopwatch|innerStopWatch|
|“supports IPv6 on iOS”|supportsIpv6OnIos|supportsIPv6OnIOS|
|“YouTube importer”|YouTubeImporter||

对于一些含糊联用的单词，如```nonempty and non-empty```都是正确的, ```checkNonempty and checkNonEmpty```都是正确的。

### 文档
#### 格式
##### 多行注释
``` kotlin
/**
 * Multiple lines of KDoc text are written here,
 * wrapped normally…
 */
fun method(arg: String) {
    // …
}
```

##### 单行
``` kotlin
/** An especially short bit of KDoc. */
```
基本使用多行注释，一般仅在注释内容能在一行容纳时使用，注意如果存在块标签时，不可使用。

##### 段落分隔
只包含一个星号符(*)的行，用来分隔总结片段与块标签。

##### 块标签
块标签顺序如下：
``` kotlin
@constructor, @receiver, @param, @property, @return, @throws, @see
```
块标签必须有描述内容，如果块标签内容超出行限制，使用连续缩进进行折行。

#### 总结段落
每个文档块开始于一个简短的总结片段。它十分重要，是唯一会出现在某些上下文如类和方法索引中的文本。
它一般是个名词短语或动词短语，它不是一个完整的语句，不以```A `Foo` is a…”, or “This method returns…```开头，也不是一个完整的祈使语句，如```Save the record.```，但它首字母大写，同时拥有标点符号，就像一个完整的语句。

#### 使用
至少，每个公有类，公有类的公有或非私有成员，都要加上文档注释。
以下情况除外：
*	自解释性函数，比如getter方法
	*	如果某个属性会让人不理解时，如```canonialName```,还是要加上文档注释
*	override的方法无需添加文档注释