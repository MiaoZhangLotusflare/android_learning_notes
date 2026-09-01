# Kotlin 空安全

## 空安全的重要性

在 Java 或 C++ 等传统语言中，空指针异常是由于尝试访问为初始化的对象或已释放的对象所引起的。这种异常不仅会导致程序崩溃，还可能引入难以调试的问题。为了避免这类错误，Kotlin 引入了一套完整的空安全系统，使得开发者能够更清晰地表达意图，并减少运行时错误的发生几率。

NullPointerException(NPE)，空指针异常，发生在运行时，引用一个空对象的方法时会抛出。Java 中常见的规避手段就是在对象使用前进行判空。而 Kotlin 中的空安全主要在编译期用类型把 null 拦住；运行时并不处理掉 null，只在少数边界再检查一次。

```kotlin
var name: String = "Alice"
name = null        // 编译期报错，编不成 .class
fun len(s: String?) {
    s.length       // 编译期报错：没处理空
}
```

编译器在 FIR 里检查：

* String 上不能出现 null

* String? 不能直接当 String 用

编译器没有把 null 处理没。JVM 上 null 还在。

```kotlin
val s: String? = null     // 合法：null 被允许，会进字节码
s?.length                 // 运行时就是一次 null 判断
s as? String              // 失败也是运行时变成 null
```

另外还有运行时检查，编译器只是把它们插进字节码：

* 非空参数被 Java 传入 null -> checkNotNull -> NPE

* s!! -> 运行时发现是 null 再崩

Kotlin 的空安全首先是编译期的类型检查：用 T/T？规定哪里允许 null。不合规的代码编不过。

null 本身还在；?.、as? 是运行时判断；对 Java 和 !! 还会插入运行时检查。

空安全只是靠编译期类型检查，而不是靠运行时到处防 NPE。

**非法的 null 用法在编译期拦住**

Kotlin 在类型系统层面区分可空和非空类型，编译器在编译期强制检查，NPE 只会在特定场景出现（显式抛、unsafe 操作、Java 互调）。

* 显式抛
  
  你（或库）故意抛 NullPointerException，或者调用了会抛它的函数。
  
  ```kotlin
  throw NullPointerException("oops")
  requireNotNull(user)          // user 为 null 时抛 NPE（带信息）
  checkNotNull(user)
  error("...")                  // 这个是 IllegalStateException，不是 NPE
  ```
  
  这不是空安全实效，是你选择了【空了就崩】。

* unsafe 操作
  
  类型系统已经标成【可能为 null】，却用不安全操作强行当非空。最典型就是 !!，还有少数强制转型。
  
  ```kotlin
  val s: String? = null
  s!!.length              // 你写了 !! → NPE
  
  val x: Any? = null
  x as String             // 不安全的 as（不是 as?）→ 崩
  ```
  
  ?. 、as? 是安全的，为 null 不会 NPE。
  
  !!、as T（目标非空）是 unsafe：编译器放行，运行时可能炸。
  
  lateinit 用前没赋值，抛的是 UninitializedPropertyAccessException，常和这类【我保证已经有值】放在一起说，但严格说不是 NPE。

* Java 互调
  
  Kotlin 管得住自己的代码，管不住没注解的 Java。null 从 Java 漏出来，或你把 Kotlin 的非空声明骗过了。
  
  ```kotlin
  // Java: String getName();   没写 @Nullable / @NotNull
  val name = javaObj.name     // 平台类型 String!
  name.length                 // 编译过；实际是 null → NPE
  ```
  
  反过来：Java 调用 Kotlin 的 fun greet(name: String) 并传入 null，会在函数入口被 checkNotNull 拦住，同样是 NPE，只是崩在边界上。
  
  这是空安全最常见的破功处：类型系统在跨语言边界变软。

## Kotlin 中的空类型

在 Kotlin 中，每个类型都有一个明确的空状态：非空类型和可空类型。这是通过类型后面添加问号 ？ 来表示的。

* 非空类型：例如 Int、String 等，表示这个类型的变量不能被赋值为 null。

* 可空类型：例如 Int？、String? 等，表示这个类型的变量可以被赋值为 null。

## 安全调用运算符

Kotlin 提供安全调用操作符?. ，它允许开发者在尝试调用方法或访问属性之前检查一个对象是否为 null。如果对象不是 null，则继续执行后面的调用；如果是 null，则不会还行任何操作，并返回 null。

```kotlin
val nullableString: String? = null
val length: Int? = nullableString?.length // 如果 nullableString 为 null，则 length 也为 null
```

## 非空断言运算符

当确定一个可空类型变量实际上并不为 null 时，可以使用非空断言运算符 !!。然而，使用此运算符时要格外小心，因为它可能会导致空指针异常。

```kotlin
val nullableString: String? = "Hello"
val firstChar = nullableString!!.first() // // 如果 nullableString 为 null，则会抛出 NullPointerException
```

## Elvis 操作符

Kotlin 中的 Elvis 操作符 ?: 可以用来提供一个默认值。当左侧的操作数为 null 时，右侧的操作数就会被返回。

```kotlin
val nullableString: String? = null

val length = nullableString?.length ?: -1 // 如果 nullableString 为 null，则 length 为 -1
```

## 智能转换

Kotlin 的编译期能够进行智能转换（smart cast），这意味着如果一个变量被检查过是否为 null，那么在检查之后，编译器会认为这个变量是非空的，直到它的值再次改变或者作用域结束。

```kotlin
var maybeNull: String? = "foo"
if (maybeNull != null){
    println(maybeNull.length) // 在这个 if 语句块内，maybeNull 被智能转换成了非空类型
}
```

## 空合并函数

除了 ?: 外，Kotlin 还支持 let、apply、run、with 和 also 等范围函数，这些函数在处理可空类型时非常有用。特别是 let 函数，它可以接受一个 lambda 表达式作为参数，只有当对象非空时才会执行该 lambda。

```kotlin
val nullableString:String? = "Hello"
nullableString.let { str ->
    println(str.toUpperCase()) //只有当 nullableString 非 null 时才执行
}
```

## 空检查与类型断言

在某些情况下，可能需要显式地检查一个值是否为 null，并对其进行类型断言。类型断言 as 和安全类型断言 as? 在处理可能为 null 的对象时非常有用。

```kotlin
val anyValue: Any? = "Hello"
val string: String? = anyValue as? String //安全类型断言，如果 anyValue 不是 String，则返回 null
```

## 不是绝对的空指针安全

Kotlin 中并不是绝对的空指针安全，最常见的就是在 kotlin 去调 Java 代码，必须下面这个例子：

```kotlin
// Java
public class Test {
    public static String getMsg() {
        return null;
    }
}


// Kotlin
fun main() {
    println(Test.getMsg().length)
}
```

运行后直接报错 NullPointerException

当与 Java 代码进行互操作时，Null 安全性确实被破坏了，当然想规避这个问题也很简单，加个 ? 即可，示例如下：

```kotlin
fun main() {
    println(Test.getMsg()?.length)
}
```

```
Tips: 也可以为 Java 代码 @NotNull 注解来解决
```

## Kotlin 是如何实现空安全的

```kotlin
fun test_1(str: String) = str.length
fun test_2(str: String?) = str?.length
fun test_3(str: String?) = str!!.length
fun test_4(str: Any?) { str as String }
fun test_5(str: Any?) { str as? String }
```

test_1：直接对参数判空，如果为空抛出参数为空的异常。

test_2：判断是否为空，不为空，调用对应的方法，否则返回一个 null。

test_3：直接判断是否为空，空的话直接抛出 NPE 异常。

test_4：判空，如果空抛出类型转换异常，否则执行后续代码。

test_5：对参数做类型判断，是 String 就把同一个引用当成 String，不是则表达式结果为 null。结果类型是 String?。实际上就是 instanceof + 原引用或 null。

## 平台类型

平台类型 Platform Type：Java 返回的值在 Kotlin 中显示为 String!，既不是 String 也不是 String?，需要手动处理。

String! 叫平台类型：Java 没说清能不能为 null，Kotlin 就不把它收成 String 或 String?，两边的检查都放松，要你自己决定按哪种采用。

#### String! 是什么

！只出现在 IDE/编译器指示里，不能写在源码里：

```kotlin
val a: String! = javaObj!.name // 语法错误，没有这种写法
val name = javaObj.name   // 推断出来才是 String!
```

来源一般是没带空注解的 Java：

```kotlin
public String getName() {
    return name； // 可能 null，Java 类型写不出
}
```

Kotlin 看见的是：

| 类型      | 含义             |
| ------- | -------------- |
| String  | 不能为 null，编译器强制 |
| String? | 可以为 null，必须先处理 |
| String! | 未知，编译器不强制      |

所以它既不是非空，也不是可空，是第三种：平台来的，信息不足的类型。

手动处理的指的是：编译器两种用法都放行，对错由你负责。

```kotlin
val name = javaObject.name   // String!

name.length     // 当 String: 编译过；实际 null -> NPE
name?.lenght.  // 当 String? : 安全
```

手动处理：你自己选一边，不要停在 String! 上。

```kotlin
// 按可空接住（更稳，Java 经常可能 null）
val name: String? = javaObj.name
val len = name?.length ?: 0

// 按非空接住（你确定 Java 契约不会返回 null）
val name: String = javaObj.name
val len = name.length
```

一旦写成 String？ 或 String，后面又回到普通空安全：可空必须 ?.，非空不能再赋 null。

危险的是一直用诊断出来的 String!，既不标类型，也不当可空处理。表面上像 Kotlin，其实和 Java 一样随时可能 NPE。

#### 为什么会有这种类型

kotlin 不敢擅自决定：

* 全当成 String -> 很多 Java 返回 null，到处崩

* 全当成 String? -> 所有 Java API 都要 ?.，和 Java 互调会烦到没法用。

于是折中：标成 T!（意思就是 Kotlin 不知道应该是什么类型，所以就是 String!），信任你比编译器更懂这条 Java API。

Java 补了注解，平台类型就会消失：

```java
@Nullable public String getName() { ... }   // Kotlin 看成 String?
@NotNull  public String getName() { ... }   // Kotlin 看成 String
```

有注解 = 编译器重新接管；没注解=你手动接管。

Java 基本类型（byte、short、int、long、float、double、char、boolean 8个，不能为 null）-> 普通非空 Kotlin 类型，不是 T！。

Java 包装类型（Byte、Short、Integer、Long、Float、Double、Character、Boolean）（可以为 null） -> 才是平台类型。

自定义类型和 String 规则相同，都是引用。

```kotlin
fun greet(name: String) {} // 非空
fun nick(name: String?) {} // 可空
```

编成字节码后，大约变成：

```java
public static final void greet(@NotNull String name) {...}
public static final void nick(@Nullable String name) {...}
```

Java 没有 T?，只能看到 String。靠注解把 Kotlin 的意思传过去：

```kotlin
fun greet(name:String) {}
```

```java
// Java 调用
greet(null); // IDE / 检查器：不能传 null（看见了 @NotNull）
```

反过来，Java 字节写的方法如果也加 @Nullable / @NotNull，Kotlin 就不再把它当成 String!，而收成 String? / String。

一边是 Kotlin 写出去注解，一边是 Kotlin 读进来注解，都是为了互操作，不是 JVM 在执行空安全。

Kotlin 编成 JVM 字节码时，可空类型会带上 @Nullable，非可空类型带上 @NotNull，方便 Java 侧的 IDE 和编译检查。JVM 本身并不执行这些注解；运行时防 Java 传 null，靠的是另外插入的 checkNotNull。

## 非空断言什么时候用

只有同时满足这两点才用!!：能证明这里不是 null，并且空了就该立刻崩，而不是继续跑。

s!! 的意思是：把 T? 强行当成 T；是 null 就 NPE。

#### 可以用

* 编译器看不出，但刚保证过非空

```kotlin
map["id"] = "42"
val id = map["id"]!!
```

更干净：map.getValue("id") 或 requireNotNull(map["id"])。

* Java/系统 API 标成可空，契约却保证有值

```kotlin
val view = findViewById<TextView>(R.id.title)!!
```

现在更常见用 ViewBinding，少写 !!。

* 测试代码

为 null 就让测试失败，可以接受。

* 非法状态必须立刻炸

这时更推荐带说明的：

```kotlin
val user = requireNotNull(currentUser) { "user must be logged in" }
```

比 currentUser!! 好查。

#### 不要用

* 网络、数据库、用户输入：可能没有值是正常情况，用 ?. / ?: / return

* 一长串消红线：user!!.profile!!.name!!

* 只是懒得处理 String?

```kotlin
// 差
val name = user?.name

// 好
val name = user?.name ?: return
val name = user?.name ?: "unknown"
```

能证明非空、空了就该崩 -> 才用 !!（更好用 requireNotNull）。值可能合法地不存在 -> 用 ?. / ?:。

## requireNotNull 和 checkNotNull

两个都是：不是 null 就原路返回非空值；是 null 就抛异常。

差别在于为什么空了，抛哪种异常。

```kotlin
val name: String? = null

val a = requireNotNull(name) // 抛 IllegalArgumentException
val b = checkNotNull(name) // 抛 IllegalStateException
val c = name!! // 抛 NullPointerException
```

通过之后，结果都是 String，后面可以直接用：

```kotlin
fun title(raw: String?):String {
    val s = requireNotNull(raw) { "title must not be null" }
    return s.uppercase()
}
```

都可以带说明，比 !! 好查。

#### 区别：

|      | requireNotNull           | checkNotNull          |
| ---- | ------------------------ | --------------------- |
| 对应   | require(...)             | check(...)            |
| 异常   | IllegalArgumentException | IllegalStateException |
| 含义   | 传入的值不合法                  | 对象/程序当前状态不合法          |
| 典型位置 | 函数入口、校验参数                | 业务做到一半，内部状态该有却没有      |

空的函数参数/入参 -> requireNotNull；空的是字段、缓存、流程走到这步该有的东西 -> checkNotNull。

```kotlin
require(id.isNotBlank()) {"id blank"} // 假 -> IllegalArgumentException
check(isLoggedIn) {"not logged in"} // 假 -> IllegalStateException
```

require/check 检查的是布尔条件。

reqiureNotNull/checkNotNull 检查的是【是不是 null】，思想一样。

## 总结

Kotlin 的空安全机制是其一大亮点，它通过一系列的设计帮助开发者避免了空指针异常带来的麻烦。通过非空类型、可空类型、安全调用操作符、非空断言运算符、Elvis 操作符以及智能转换等功能，Kotlin 为开发者提供了一套全面的工具来确保代码的安全性和可靠性。掌握这些概念和技巧，可以使 Kotlin 程序员编写出更健壮、更易于维护的代码。

Kotlin 中对空安全背后的处理套路如下：

1. 非空类型的属性编译器添加 @NotNull 注解，可空类型添加 @Nullable 注解；

2. 非空类型直接对参数进行判空，如果为空直接抛出异常；

3. 可空类型，如果是 ?. 判空，不空才执行后续代码，否则返回 null；如果是 !!，空的话直接抛出 NPE 异常。

4. as 操作符会判空，空的话直接抛出异常，不为空才执行后续操作，没做类型判断 ! 运行时可能会报错。

5. as? 实际上就是 instanceof + 原引用或 null，这里有一点注意：as? 处理后的类型可能为空！！！所以调用 as? 转换后的对象还需要添加安全调用操作符（?.）。

## 参考内容

* [深入浅出：Kotlin 中的空安全机制-阿里云开发者社区](https://developer.aliyun.com/article/1602844)
* [Kotlin刨根问底(一)：你真的了解Kotlin中的空安全吗？ - 掘金](https://juejin.cn/post/6844904048987127822)
