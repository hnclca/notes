# Kotlin standard library

## kotlin
核心方法和类型，用于所有支持平台。
### 类型
#### Annotation(interface)
所有注解隐式实现的基础接口。

#### Any(open)
kotlin所有类的根类，所有类默认继承Any类。

#### Array
代表一个数组(面向Java平台时特指java array)。生成数组的方法有：arrayOf, arrayOfNulls, emptyArray。

#### AssertionError(JS)(open)
继承Error类。
JVM平台下使用类型别名(typealias)映射java库。

#### Boolean
继承Comparable接口
代表逻辑值真或假。在JVM平台，非空的此类型表示为基本类型boolean。

#### BooleanArray
逻辑值数组，面向JVM平台，该类实例表示为boolean[]。

#### Byte
继承Number, Comparable接口
代表8位有符号整数。在JVM平台，非空的此类型表示为基本类型byte。

#### ByteArray
字节数组，面向JVM平台，该类实例表示为byte[]。

#### Char
继承Comparable接口
代表16位的Unicode字符。在JVM平台，非空的此类型表示为基本类型char。

#### CharArray
字符数组，面向JVM平台，该类实例表示为char[]。

#### CharSequence(interface)
代表字符的可读序列。

#### ClassCastException(JS)(open)
继承RuntimeException类。

#### Comparable(interface)
继承该接口实现实例的比较功能。

#### Comparator(JS)(interface)
比较器。

#### ConcurrentModificationException(JS)(open)
继承RuntimeException。

#### DeprecationLevel(enum)
包含各类已过期水平。

#### Double
继承Number, Comparable接口
代表64位符合IEEE 754标准的双精度浮点数。在JVM平台，非空的此类型表示为基本类型double。

#### DoubleArray
双精度浮点数数组，面向JVM平台，该类实例表示为double[]。

#### Enum(abstract)
继承Comparable接口
所有枚举类的共同基类。

#### Error(JS)(open)
继承Throwable类。

#### Exception(JS)(open)
继承Throwable类。

#### Float
继承Number, Comparable接口
代表32位符合IEEE 754标准的单精度浮点数。在JVM平台，非空的此类型表示为基本类型float。

#### FloatArray
单精度浮点数数组，面向JVM平台，该类实例表示为float[]。

#### Function(interface)
代表一个函数类型，比如lambda, 匿名函数或函数引用。

#### IllegalArgumentException(JS)(open)
继承RuntimeException类。

#### IllegalStateException(JS)(open)
继承RuntimeException类。

#### IndexOutOfBoundsException(JS)(open)
继承RuntimeException类。

#### Int
继承Number, Comparable接口
代表32位有符号整数。在JVM平台，非空该类型表示为基本类型int。

#### IntArray
整数数组，面向JVM平台，该类实例表示为int[]。

#### KotlinVersion(1.1)
继承Comparable接口。
表示kotlin标准库版本信息。

#### Lazy(interface)
代表值使用懒加载。

#### LazyThreadSafetyMode(enum)
指定懒加载实例如何进行多线程同步。

#### Long
继承Number, Comparable接口。
表示64位有符号整数。在JVM平台，非空类型表示为基本类型long。

#### LongArray
长整数数组。面向JVM平台时，该类实例表示为long[]。

#### NoSuchElementException(JS)(open)
继承Exception类。

#### NoWhenBranchMatchedException(open)
继承RuntimeException类。

#### Nothing
Nothing没有实例，代表不存在的值。比如一个方法返回Nothing类型，即没有返回（一直抛出一个例外）。

#### NullPointException(JS)(open)
继承RuntimeException类。

#### Number(abstract)
所有数值类型的基类。

#### NumberFormatException(JS)(open)
继承RuntimeException类。

#### Pair(data)
继承Serializable接口
代表两个值的泛型对。

#### RuntimeException(JS)(open)
继承Exception类。

#### Short
继承Number类, Comparable接口。
代表16位有符号整数。在JVM平台，非空类型表示为基本类型short。

#### ShortArray
短整数数组。面向JVM平台，该类实例表示为short[]。

#### String
继承Comparable、CharSequence接口。
代表字符串，所有的字符串字面量都是该类实例的实现。

#### Throwable(open)
所有Error和Exception类的基类。只有该类的实例才能被抛出或捕获。

#### Triple(data)
继承Serializable接口。
代表三个值的组合。

#### UninitalizedPropertyAccessException(open)
继承RuntimeException类。

#### Unit(object)
该类型只有一个值。该类型对应Java中的void。

#### UnsupportedOperationException(JS)(open)
继承RuntimeException类。

### Annotations
#### Deprecated
已过时标记，包括类、函数、属性、变量或参数。

#### DslMaker(1.1)
应用于类，特指该类定义了一个领域结构化语言。

#### ExtensionFunctionType
标记扩展函数。

#### ParameterName(1.1)
标记函数类型的类型参数，并持有用户类型定义时所指定的相应参数名称。

#### PublishedApi(1.1)
应用于具有内部可见性的类或类成员，使其公开，可在公开的内联函数中使用。

#### ReplaceWith
标记用来替换已过时函数、属性或类的代码片段。通过该标记工具自动应用这些替换。

#### SinceKotlin
标记定义第一次出现的kotlin版本。在低于指定的kotlin API版本使用时会报错。

#### Suppress
标记忽略编译器给出的警告。

#### Synchronized(JS)
标记同步。
JVM平台实现在kotlin.jvm库中。

#### UnsafeVariable
标记忽略变量冲突错误。

#### Volatile(JS)
标记易变性。
JVM平台实现在kotlin.jvm库中。

### Exceptions
#### NotImplementedError
继承Error类。
该错误提示方法体没有实现。

### Type Aliases
TypeAliases.kt
``` kotlin
@SinceKotlin("1.1") public typealias Error = java.lang.Error
@SinceKotlin("1.1") public typealias Exception = java.lang.Exception
@SinceKotlin("1.1") public typealias RuntimeException = java.lang.RuntimeException
@SinceKotlin("1.1") public typealias IllegalArgumentException = java.lang.IllegalArgumentException
@SinceKotlin("1.1") public typealias IllegalStateException = java.lang.IllegalStateException
@SinceKotlin("1.1") public typealias IndexOutOfBoundsException = java.lang.IndexOutOfBoundsException
@SinceKotlin("1.1") public typealias UnsupportedOperationException = java.lang.UnsupportedOperationException

@SinceKotlin("1.1") public typealias NumberFormatException = java.lang.NumberFormatException
@SinceKotlin("1.1") public typealias NullPointerException = java.lang.NullPointerException
@SinceKotlin("1.1") public typealias ClassCastException = java.lang.ClassCastException
@SinceKotlin("1.1") public typealias AssertionError = java.lang.AssertionError

@SinceKotlin("1.1") public typealias NoSuchElementException = java.util.NoSuchElementException

@SinceKotlin("1.1") public typealias Comparator<T> = java.util.Comparator<T>
```

### Extensions For External Classes
#### java.math.BigDecimal(JVM)

#### java.math.BigInteger(JVM)

### Properties
#### isInitialized(1.2)
如果延迟初始化属性已赋值返回true，否则返回false。
``` kotlin
val KProperty0<*>.isInitialized: Boolean
```

#### stackTrace(JVM)
返回有关这个异常的堆栈跟踪信息的堆栈跟踪元素数组
``` kotlin
val Throwable.stackTrace: Array<StackTraceElement>
```

### Functions
#### Comparator(JS)
比较器。
``` kotlin
fun <T> Comparator(
    	comparison: (a: T, b: T) -> Int
): Comparator<T>
```

#### TODO
一直抛出```NotImplementedError```显示操作没有实现。
``` kotlin
fun TODO(): Nothing
fun TODO(reason: String): Nothing
```

#### addSuppressed(JVM)
```Throwable```的扩展函数，添加指定异常到忽略的异常列表中。
``` kotlin
fun Throwable.addSuppressed(exception: Throwable)
```

#### also(1.1)
范型类扩展函数，调用指定函数块，返回值为自身。函数块参数为自身，返回值为Unit。
``` kotlin
fun <T> T.also(block: (T) -> Unit): T
```

#### apply
范型类扩展函数，调用指定函数块，返回值为自身。函数块接收者为自身，无参，返回值为Unit。
``` kotlin
fun <T> T.apply(block: T.() -> Unit): T
```

#### arrayOf
返回包含指定元素的数组实例。
``` kotlin
fun <T> arrayOf(vararg elements: T): Array<T>
```

#### arrayOfNulls
返回指定大小，初始值为null的数组。
``` kotlin
fun <T> arrayOfNulls(size: Int): Array<T?>
```

#### assert(JVM)
值为假时抛出```AssertionError```，使用-ea启用运行时断言。
``` kotlin
fun assert(value: Boolean)
fun assert(value: Boolean, lazyMessage: () -> Any)
```

#### xxArrayOf
返回包含指定类型元素的数组。
``` kotlin
fun booleanArrayOf(vararg elements: Boolean): BooleanArray
fun byteArrayOf(vararg elements: Byte): ByteArray
fun charArrayOf(vararg elements: Char): CharArray
fun doubleArrayOf(vararg elements: Double): DoubleArray
fun floatArrayOf(vararg elements: Float): FloatArray
fun intArrayOf(vararg elements: Int): IntArray
fun longArrayOf(vararg elements: Long): LongArray
fun shortArrayOf(vararg elements: Short): ShortArray
```

#### check
值为假时抛出```IllegalStateException```
``` kotlin
fun check(value: Boolean)
fun check(value: Boolean, lazyMessage: () -> Any)
```

#### checkNotNull
值为null时抛出```IllegalStateException```，否则返回非空值。
``` kotlin
fun <T : Any> checkNotNull(value: T?): T
fun <T : Any> checkNotNull(
    	value: T?,
    	lazyMessage: () -> Any
): T
```

#### emptyArray(1.1)
返回一个指定类型的空数组。
``` kotlin
fun <T> emptyArray(): Array<T>
```

#### enumValueOf(1.1)
根据指定名称返回枚举实例。
``` kotlin
fun <T : Enum<T>> enumValueOf(name: String): T
```

#### enumValues
返回包含指定枚举实例的数组。
``` kotlin
fun <T : Enum<T>> enumValues(): Array<T>
```

#### error
抛出带有指定消息的```IllegalStateException```
``` kotlin
fun error(message: Any): Nothing
```

#### getValue
指定类型的只读属性委托给Lazy实例的扩展函数。
``` kotlin
operator fun <T> Lazy<T>.getValue(
    	thisRef: Any?,
    	property: KProperty<*>
): T
```

#### isFinite/isInfinite
判断浮点数时是否是有穷/无穷浮点数。
``` kotlin
fun Double.isFinite(): Boolean
fun Float.isFinite(): Boolean

fun Double.isInfinite(): Boolean
fun Float.isInfinite(): Boolean
```

#### isNaN
判断浮点数时是否是非数。
``` kotlin
fun Double.isNaN(): Boolean
fun Float.isNaN(): Boolean
```

#### lazy
根据给定初始化函数及其他参数创建Lazy实例。
``` kotlin
fun <T> lazy(initializer: () -> T): Lazy<T>
fun <T> lazy(lock: Any?, initializer: () -> T): Lazy<T>

fun <T> lazy(
    	mode: LazyThreadSafetyMode,
    	initializer: () -> T
): Lazy<T>
```

#### lazyOf
使用指定初始值创建Lazy实例。
``` kotlin
fun <T> lazyOf(value: T): Lazy<T>
```

#### let
泛型扩展函数，调用指定函数块返回结果，函数参数为自身，返回值为结果类型。
``` kotlin
fun <T, R> T.let(block: (T) -> R): R
```

#### plus
字符串操作符+重载函数，如果接收者或other为null，则代表字符串值为"null"。
``` kotlin
operator fun String?.plus(other: Any?): String
```

#### printStackTrace(JVM)
打印异常的堆栈跟踪信息到标准输出、指定输出流中。
``` kotlin
fun Throwable.printStackTrace()
fun Throwable.printStackTrace(writer: PrintWriter)
fun Throwable.printStackTrace(stream: PrintStream)
```

#### repeat
重复指定次数执行action函数方法。
``` kotlin
fun repeat(times: Int, action: (Int) -> Unit)
```

#### require
值为假时抛出```IllegalArgumentException```
``` kotlin
fun require(value: Boolean)
fun require(value: Boolean, lazyMessage: () -> Any)
```

#### requireNotNull
值为null时抛出```IllegalArgumentException```，否则返回非空值。
``` kotlin
fun <T : Any> requireNotNull(value: T?): T
fun <T : Any> requireNotNull(
	    value: T?,
	    lazyMessage: () -> Any
): T
```

#### run
调用指定函数块，返回结果。函数块无参，返回结果类型。
``` kotlin
fun <R> run(block: () -> R): R
fun <T, R> T.run(block: T.() -> R): R
```

#### synchronized
使用给定的锁执行给定的函数块。
``` kotlin
fun <R> synchronized(lock: Any, block: () -> R): R
```

#### takeIf(1.1)
如果满足给定条件返回值，否则返回null。
``` kotlin
fun <T> T.takeIf(predicate: (T) -> Boolean): T?
```

#### takeUnless(1.1)
如果不满足给定条件返回值，否则返回null。
``` kotlin
fun <T> T.takeUnless(predicate: (T) -> Boolean): T?
```

#### to
声明中缀表示法，创建Pair类型的元组。
``` kotlin
infix fun <A, B> A.to(that: B): Pair<A, B>
```

#### toBigDecimal(JVM)(1.2)
转换为大数值类型。
``` kotlin
fun Int.toBigDecimal(): BigDecimal
fun Int.toBigDecimal(mathContext: MathContext): BigDecimal
fun Long.toBigDecimal(): BigDecimal
fun Long.toBigDecimal(mathContext: MathContext): BigDecimal
fun Float.toBigDecimal(): BigDecimal
fun Float.toBigDecimal(mathContext: MathContext): BigDecimal
fun Double.toBigDecimal(): BigDecimal
fun Double.toBigDecimal(mathContext: MathContext): BigDecimal
```

#### toBigInteger(JVM)(1.2)
转换为大整数类型。
``` kotlin
fun Int.toBigInteger(): BigInteger
fun Long.toBigInteger(): BigInteger
```

#### toBits(1.2)
返回单/双精度的位表示形式
``` kotlin
fun Double.toBits(): Long
fun Float.toBits(): Int
```

#### toList
转换元组，三元组为列表。
``` kotlin
fun <T> Pair<T, T>.toList(): List<T>
fun <T> Triple<T, T, T>.toList(): List<T>
```

#### toRawBits(1.2)
在NaN值的处理上区别toBits，保持NaN的精确？
``` kotlin
fun Double.toRawBits(): Long
fun Float.toRawBits(): Int
```

#### toString
返回对象的字符串表达形式，接收者可以是null，返回的字符串为"null"
``` kotlin
fun Any?.toString(): String
```

#### use(JVM)(1.2)(JRE7)
在该资源上执行指定的块函数，不管异常是否产生，都能正确关闭。
``` kotlin
fun <T : AutoCloseable?, R> T.use(block: (T) -> R): R
```

#### with
调用给定的接收者和函数块返回结果，函数块为接收者的成员，无参，返回值为结果类型。
``` kotlin
fun <T, R> with(receiver: T, block: T.() -> R): R
```

### Companion Object Functions
#### fromBits(1.2)
根据给定的位表示形式返回单/双精度浮点数。
``` kotlin
fun Double.Companion.fromBits(bits: Long): Double

fun Float.Companion.fromBits(bits: Int): Float
```

## kotlin.annotation
kotlin注解支持库

## kotlin.browser(JS)
浏览器环境中访问顶层属性(文档、窗口等)

## kotlin.collections
集合类型，包括迭代类、集合类、列表、集、映射，和相关的顶层函数和扩展函数

## kotlin.comparisons
创建比较器实例的辅助函数

## kotlin.concurrent(JVM)
并发编程的工具类

## kotlin.coroutines.experimental(1.1)
协程支持库，包含懒加载序列支持

## kotlin.coroutines.experimental.intrinsics(1.1)
基于协程库的低级构建块

## kotlin.dom(JS)
用于浏览器DOM操作的工具类

## kotlin.experimental(1.1)
实验性API

## kotlin.io
用于文件和流操作的IO API

## kotlin.js(JS)
仅限js平台的方法和注解

## kotlin.jvm(JVM)
仅限java平台的方法和注解

## kotlin.math(1.2)
数学方法及常量

## kotlin.properties
委托属性的标准实现及实现自定义委托的辅助函数

## kotlin.ranges
区间、级数和相关的顶层函数及扩展函数

## kotlin.reflect
kotlin反射的运行时API

## kotlin.reflect.full(JVM)(1.1)
kotlin-reflect库的kotlin反射扩展库

## kotlin.reflect.jvm(JVM)
用于来自kotlin-reflect库的kotlin反射与java反射进行互操作的运行时API

## kotlin.sequences
实例化序列的顶层函数及序列的扩展函数
序列：未经求值集合。

## kotlin.streams(JVM)(1.2)(JRE8)
Java 8 streams的工具方法

## kotlin.system(JVM)
系统相关的工具方法

## kotlin.text
文本和正则表达式的方法

## org.khronos.webgi(JS)
WebGL API的包装类

## org.w3c.dom(JS)
DOM API的包装类

## org.w3c.dom.css(JS)
DOM CSS API的包装类

## org.w3c.dom.events(JS)
DOM events API的包装类

## org.w3c.dom.parsing(JS)
DOM parsing API的包装类

## org.w3c.dom.svg(JS)
DOM SVG API的包装类

## org.w3c.dom.url(JS)
DOM URL API的包装类

## org.w3c.fetch(JS)
W3C fetch API的包装类

## org.w3c.files(JS)
W3C file API的包装类

## org.w3c.notifications(JS)
Web Notifications API的包装类

## org.w3c.performance(JS)
Navigation Timing API的包装类

## org.w3c.workers(JS)
Web Workers API的包装类

## org.w3c.xhr(JS)
XMLHttpRequest API的包装类