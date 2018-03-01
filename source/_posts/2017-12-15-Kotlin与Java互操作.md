---
title: Kotlin与Java互操作
comments: true
toc: true
date: 2017-12-15 11:09:14
tags:
	- kotlin
	- java
---

### Java in Kotlin

#### 不要使用kotlin的硬关键字作为标识符
如果有使用的，需要使用反引号(`)转义
``` kotlin
val callable = Mockito.mock(Callable::class.java)
Mockito.`when`(callable.call()).thenReturn(/* … */)
```

#### Lambda参数置于最后
Java语言中可使用SAM转换，将仅包含一个方法的接口实现转换为lambda表达式形式，在kotlin中如果最后一个参数是lambda表达式，表达式可不用在参数中体现。
``` kotlin
public static <T> Flowable<T> create(
    FlowableOnSubscribe<T> source,
    BackpressureStrategy mode) { /* … */ }

Flowable.create({ /* … */ }, BackpressureStrategy.LATEST)
// 如果交换参数顺序
Flowable.create(BackpressureStrategy.LATEST) { /* … */ }
```

<!-- more -->
#### 属性前缀
属性访问方法，必须使用标准前缀，如```get, is, set```

#### 操作符重载
kotlin允许某些操作符重载，确保拥有重载标识符的方法在kotlin简短语法下使用有意义。
``` kotlin
public final class IntBox {
  private final int value;
  public IntBox(int value) {
    this.value = value;
  }
  public IntBox plus(IntBox other) {
    return new IntBox(value + other.value);
  }
}
val one = IntBox(1)
val two = IntBox(2)
val three = one + two // Invokes one.plus(two)
```

#### 可空性注解
非基本类型的参数、返回值、公有类的属性都应该有可空性注解，否则将视为平台类型。
JSR305包注解因为opt-in标识与Java 9模型系统冲突，所以不能作为合理的默认值。

### Kotlin in Java
#### 文件名
当文件中存在顶层属性或方法时，使用```@file:JvmName("Foo")```文件注解，标识它们的所属类。
Foo.kt中的顶层成员默认属于FooKt类成员。
使用```@file:JvmMultifileClass```将多个文件的顶层成员合并到一个类中。

#### Lambda参数
##### 函数类型
返回值为Unit函数类型在Java中使用时，需要显式返回Unit.INSTANCE语句。
``` kotlin
fun sayHi(callback: (String) -> Unit) = /* … */

// Kotlin caller:
greeter.sayHi { Log.d("Greeting", "Hello, $it!") }

// Java caller:
greeter.sayHi(name -> {
    Log.d("Greeting", "Hello, " + name + "!");
    return Unit.INSTANCE;
});
```

##### Kotlin接口SAM转换
java中可将只有一个抽象方法的接口，SAM转换为lambda表达式使用，在kotlin中只能用匿名类方法使用。
``` kotlin
interface GreeterCallback {
    fun greetName(name: String): Unit
}

fun sayHi(callback: GreeterCallback) = /* … */

// Kotlin caller:
greeter.sayHi(object : GreeterCallback {
    override fun greetName(name: String) {
        Log.d("Greeting", "Hello, $name!")
    }
})

// Java caller:
greeter.sayHi(name -> Log.d("Greeting", "Hello, " + name + "!"))
```

##### java接口
``` java
// Defined in Java:
interface GreeterCallback {
    void greetName(String name);
}
fun sayHi(greeter: GreeterCallback) = /* … */

// Kotlin caller:
greeter.sayHi(GreeterCallback { Log.d("Greeting", "Hello, $it!") })

// Java caller:
greeter.sayHi(name -> Log.d("Greeter", "Hello, " + name + "!"));java
```

#### 避免Nothing泛型
泛型参数Nothing会被Java视为原始类型，原始类型很少在Java中使用，要避免。

#### 文档异常
抛出异常的方法需要用文档块标签```@Throws```注明受检异常和运行时异常。
kotlin编译器不对会受检异常进行提醒。

#### 防御式拷贝
在从公共API中返回共享或非独只读集合时，使用不可变容器包装或执行防御式拷贝。kotlin强制保持它们的只读属性，在java中无此规则，若不采取包装或防御式拷贝，不变量会变为可变量。

#### 伴生类函数
伴生对象中的公有函数要加上```@JvmStatic```才能作为类静态方法在java中使用，否则只能作为类的Companion属性的实例方法使用。
```
class KotlinClass {
    companion object {
        @JvmStatic fun doWork() {
            /* … */
        }
    }
}

public final class JavaClass {
    public static void main(String... args) {
    	KotlinClass.doWork();
        // KotlinClass.Companion.doWork();
    }
}

```

#### 伴生类属性
伴生对象中的非常量属性要加上```@JvmField```才能作为类静态属性在java中使用，否则只能将属性访问器作为类的Companion属性的实例方法才能访问。
``` kotlin
class KotlinClass {
    companion object {
        const val INTEGER_ONE = 1
        @JvmField val BIG_INTEGER_ONE = BigInteger.ONE
    }
}

public final class JavaClass {
    public static void main(String... args) {
        System.out.println(KotlinClass.INTEGER_ONE);
        System.out.println(KotlinClass.BIG_INTEGER_ONE);
        // System.out.println(KotlinClass.Companion.getBIG_INTEGER_ONE());
    }
}
```

#### 命名习惯
kotlin不同于java的调用约定，会改变方法命名方式。使用```@JvmName```设计命名使得该方法遵守两种语言环境下的命名习惯。
通常发生在扩展属性与扩展方法上，因为接收类型的定位不一致。
``` kotlin
sealed class Optional<T : Any>
data class Some<T : Any>(val value: T): Optional<T>()
object None : Optional<Nothing>()

@JvmName("ofNullable")
fun <T> T?.asOptional() = if (this == null) None else Some(this)

// FROM KOTLIN:
fun main(vararg args: String) {
    val nullableString: String? = "foo"
    val optionalString = nullableString.asOptional()
}

// FROM JAVA:
public static void main(String... args) {
    String nullableString = "Foo";
    Optional<String> optionalString =
          Optionals.ofNullable(nullableString);
}
```

#### 有默认值的方法重载
使用```@JvmOverloads```注解有默认值的方法，否则只能在java中使用拥有全部参数的那个方法。
当使用```@JvmOverloads```注解时，确保生成的所有方法都有意义，否则通过如下方法进行重构，直到满意为止：
1.	改变默认值的顺序
2.	手动重载有默认值的方法
``` kotlin
class Greeting {
    @JvmOverloads
    fun sayHello(prefix: String = "Mr.", name: String) {
        println("Hello, $prefix $name")
    }
}

public class JavaClass {
    public static void main(String... args) {
        Greeting greeting = new Greeting();
        greeting.sayHello("Bob");
    }
}
```