# Kotlin const val 与 val

> const val 是编译器常量（在编译时确定值），而 val 是运行时只读变量（在运行时确定值）。
> 
> kotlin 编译器会将 const val 视为一个字面量常量。在编译过程中，编译器会直接把属性访问替换为实际的常量值，并将该值写入使用它的字节码指令中。
> 
> - 只能修饰 String 或基本数据类型。
> - 不能修饰自定义类或复杂对象。
> - 只能在顶层、object 声明或者 companion object 内部定义，不能在普通类的方法局部作用域中声明。
> 
> 即使一个普通的 val 在编译时已经能够确定其值，编译器通常也不会进行内联。它会被视为一个真正的属性，拥有实际的内存存储位置，并通过字段访问或 Getter 方法获取值。
> 
> const val 与 val 在性能上的差异本质上是一种权衡：
> 
> - val 的内存占用：会在每个对象实例中为该字段分配运行时存储空间。
> - const val 的内存占用：不需要在对象实例中为字段分配运行时存储空间，但它们仍然会占用类文件和常量池中的空间。由于常量值会被复制到每个调用点，如果将较长的字符串或较大的值定义为 const val 并被大量使用，可能会轻微增加编译后 .class 或 .dex 文件的体积。
> 
> const val 的值会在编译期间被直接复制到调用方的字节码中，因此在多模块项目或公共库中，它会带来一个非常大的架构风险。库模块的值是 constant val，app 模块编译时会将值写入它本身的字节码中，如果后面修改值，但没有对 app 进行完整重新编译，那 app 模块会继续使用旧值。
> 
> @JvmField：如果希望对于普通的 val 属性或 var 属性，访问时不让 Kotlin 编译器生成 Getter 方法，运行时直接读取数据，但也不会像 const val 那样进行编译器内联，那么可以使用 @JvmField。性能上会少一次 getter，带来一定的性能优化，又避免了 const val 在多模块项目中由于编译期内联而引发的二进制兼容性问题。为了和 Java/老框架的字段约定对齐时，也会使用。
> 
> 在使用AOP 和 Gradle Plugin 的时候，在打包阶段只改 class时，改 const val 定义处，一般改不到已经用到它的地方。
> 
> 必须要用 const val: 注解参数、BuildConfig 字段。
> 
> - 如果是固定的配置项、全局变量、URL 或数值边界（且类型是基本类型或字符串），优先使用 const val 以获得更好的性能。
> - 其他绝大多数只需要只读、不可修改的普通属性、局部变量或对象实例，统一使用 val。

Kotlin 中 const val 和 val 的核心区别在于赋值的时机和使用场景。简单来说，const val 是编译器常量（在编译时确定值），而 val 是运行时只读变量（在运行时确定值）。

* 使用 const val：适用于严格意义上的 Kotlin 编译期常量（compile-time constants）。其值会被直接内联（inline）到字节码中，执行效率更高，但在跨模块（module）使用时存在二进制兼容性（binary compatibility）风险。

* 使用 val：适用于普通的运行时只读属性。它更加安全、灵活，支持自定义对象类型，并且能够避免因常量内联导致的代码重复和版本兼容问题。

核心区别一览表：

| 特性          | const val                                         | val                                |
| ----------- | ------------------------------------------------- | ---------------------------------- |
| 赋值时机        | 编译时（compile-time）                                 | 运行时（Runtime）                       |
| 数据类型        | 仅限基本数据类型（如 Int、Long、Boolean 等）和 String            | 支持任意类型                             |
| 定义位置        | 只能在顶层（Top-level）、object 中或伴生对象（companion object）中 | 可以修饰局部变量、类成员属性等                    |
| 注解参数        | 可以用做注解的参数                                         | 不能用作注解的参数                          |
| 对应 Java 字节码 | 相当于Java 的 public static final 基本类型或 String        | 相当于带 getter 的变量，或者 Java 的 final 字段 |

## 详细对比

1. const val （编译器常量）
   
   Kotiln 编译器会将 const val 视为一个字面量常量（Literal constant）。在编译过程中，编译器会直接把属性访问替换为实际的常量值，并将该值写入使用它的字节码指令中。
   
   * 特点：它的值在编译器就已经完全确定。编译器在处理时，会把代码中所有用到这个常量的地方直接替换（内联）成对应的值。
   
   * 限制条件：
     
     * 只能修饰 String 或基本数据类型（Primitive types）。
     
     * 不能修饰自定义类或复杂对象。
     
     * 只能在顶层（Top-level）、object 声明或者 companion object 内部定义，不能在普通类的方法局部作用域中声明。
   
   ```kotlin
   const val MAX_COUNT = 100 // 正确：顶层编译时常量
   
   object Config {
       const val API_URL = "https://example.com" // 正确：单例对象内
   }
   ```

2. val (只读属性)
   
   即使一个普通的 val 在编译时已经能够确定其值，编译器通常也不会进行内联。它会被视为一个真正的属性（Property），拥有实际的内存存储位置（Backing Field），并通过字段访问或 Getter 方法获取值。
   
   * 特点：它是 Read-only （只读）的引用，类似于 Java 中加了 final 关键字的变量。它的值可以动态计算得出，在程序运行时才被赋值。
   
   * 底层原理：在类中声明的 val 属性，kotlin 会自动生成一个幕后字段（backing field）和一个 get() 方法。
   
   ```kotlin
   fun printCurrentTime() {
       val currentTime = System.currentTimeMillis() // 正确：运行时动态赋值
       val message: String = computeMessage()       // 正确：由函数返回值赋值
   }
   ```

## Android 实际案例

为了理解这一差异为什么重要，来看一个典型的 Android 配置类，在同一个场景下同时使用 const val 和 val。

```kotlin
package com.example.app


// 1. Excellent use of const val (Intent Key)
const val EXTRA_USER_ID = "com.example.app.EXTRA_USER_ID"

object NetworkConfig {
    // 2. Standard val for dynamic or configurable fields
    val baseUrl: String = if (BuildCOnfig.DEBUG) {
        "https://sandbox.api.example.com"
    } else {
        "https://api.example.com"
    }
}
```

#### 明位置与严格限制（Placement and Strict Constraints）

由于 const val 必须在编译期间（compile time）就能完全确定其值，因为 Kotlin 对它施加了严格的限制。

换句话说：

```
编译器必须能够在不运行任何代码的情况下，直接计算出常量的最终值。
```

因此，编译期常量不能随意声明在任何位置，也不能依赖运行时计算结果。

```kotlin
// 1. Top-level declarations (Allowed)
const val GLOBAL_TIMEOUT = 5000 

object AppConfig {
    // 2. Inside named objects (Allowed)
    const val PREFS_NAME = "app_settings" 
}

class MainActivity : AppCompatActivity() {
    companion object {
        // 3. Inside companion objects (Allowed)
        const val REQUEST_CODE_AUTH = 101 
    }

    // 4. Standard class properties (FORBIDDEN)
    // const val SCREEN_TITLE = "Dashboard" // ❌ Compile Error
    val screenTitle = "Dashboard" //  Allowed
}
```

#### 深入字节码

为了真正理解 const val 和 val 的区别，需要看看 Kotlin 编译器在生成 JVM 字节码时究竟做了什么。

下面通过一个简单示例来观察两者在编译后的差异。

```kotlin
// Physics.kt
package com.example

const val SPEED_OF_LIGHT = 299792458

class PhysicsEngine {
    val gravitationalConstant = 9.81

    fun calculateEnergy(mass: Double): Double {
        return mass * SPEED_OF_LIGHT * SPEED_OF_LIGHT 
    }

    fun calculateWeight(mass: Double): Double {
        return mass * gravitationalConstant
    }
}
```

##### 1. const val 在底层是怎么实现的

当编译器编译 Physics.kt 时，会为这个常量生成一个静态字段（static field），并将其放入一个特殊的 File Facada Class（文件门面类）中。

```kotlin
public static final init SPEED_OF_LIGHT = 299792458;
```

但是反编译后的 Java 代码中的 calculateEnergy 方法你会注意到，SPEED_OF_LIGHT 字段实际上从未被访问过。编译器直接将常量值内联（inline）到了表达式中：

```java
public final class PhysicsEngine {
   public final double calculateEnergy(double mass) {
      // Bypassed the field entirely; hardcoded directly into instructions!
      return mass * (double)299792458 * (double)299792458; 
   }
}
```

在原始字节码层面，JVM 会根据常量值的大小和类型，使用不同的优化指令来加载这些被内联的值：

* <mark>ICONST_*：用于加载较小的整数（0到5）。</mark>

* <mark>BIPUSH 或 SIPUSH：用于加载 byte 或 short 范围内的整数。</mark>

* <mark>LDC（Load Constant：用来加载较大的数字、浮点数以及字符串常量）。</mark>

##### 2. val 在底层是如何实现的

现在再来看 gravitationalConstant。由于它是一个普通的 val，因此会保留一个私有的后备字段（backing field）。它在字节码中的访问方式完全取决于调用者所处的上下文：

* 内部调用（同一个类内部）：当 calculateWeight 读取该属性时，字节码会直接使用 <mark>GETFIELD 指令</mark>访问后备字段。此时不会通过 Getter，而是直接读取字段值。

* 外部访问（类外部）：如果其他类调用 engine.gravitationalConstant，字节码会使用 <mark>INVOKEVIRTUAL 指令</mark>调用编译器自动生成的公共 Getter 方法（getGravitationalCOnstant()）来获取属性值。

```kotlin
public final class PhysicsEngine {
   private final double gravitationalConstant = 9.81;

   // Generated for external callers
   public final double getGravitationalConstant() {
      return this.gravitationalConstant;
   }

   public final double calculateWeight(double mass) {
      // Internal access reads the field directly via GETFIELD instruction
      return mass * this.gravitationalConstant; 
   }
}
```

## 内存权衡：需要注解的细节（Memory Trade-offs: The Fine Print）

一个常见的误区是任务 const val 总是优于 val，因为它消除了运行时对象开销。

实际上，const val 与 val 在性能上的差异本质上是一种权衡：

* val 的内存占用：会在每个对象实例中为该字段分配运行时存储空间。

* const val 的内存占用：不需要在对象实例中为字段分配运行时存储空间，但它们仍然会占用类文件（Class File）和常量池（Constant Pool）中的空间。由于常量值会被复制到每一个调用点，如果将较长的字符串或较大的值定义为 const val 并被大量使用，可能会轻微增加编译后 .class 或 .dex 文件的体积。

#### 隐藏的陷阱：二进制兼容性

由于 const val 的值会在编译期间被直接复制到调用方的字节码中，因此在多模块项目或公共库（Library）中，它会带来一个非常大的架构风险。

假设有一个库模块（:network）和一个应用模块（:app）。

```kotlin
// In the :network module (Version 1.0)  
const val API_VERSION = 1
```

```kotlin
// In the :app module  
fun connect() {  
    println("Connecting to version: $API_VERSION")  
}
```

当 :app 模块编译时，整数值 1 会被直接写入（backed into）它自身的字节码中。

如果之后你将 :network 库升级到 2.0 版本，并把：

```kotlin
const val API_VERSION = 2
```

进行了修改，但在发布新版库时并没有强制对主应用模块 :app 进行完整重新编译，那么 :app 仍然会继续使用旧的内联值 1 来执行代码。

#### 高级技巧：使用 @JvmField 抑制 Getter 的生成

如果你希望对于普通的 val 属性或 var 属性，访问时不让 Kotlin 编译器生成 Getter 方法，运行时直接读取数据，但也不会像 const val 那样进行编译器内联，那么可以使用 @JvmField 注解：

```kotlin
@JvmField
val CONFIG_ID = 999
```

这会强制编译器将后备字段（backing field）声明为 public，并移除对应的 Getter 方法。这样，外部调用方将通过 GETSTATIC 或 GETFIELD 指令直接访问字段，而不是调用 Getter 方法。这种方式能够带来一定的性能优化，同时又避免了 const val 在多模块项目中由于编译期内联而引发的二进制兼容性问题。

###### 用途

1. 给 Java 用，对方要的是字段，不是 getter
   
   Java 访问普通 val name 只能写 obj.getName()。加了 @JvmField 才能写 obj.name，和 Java 自己的 public final 一样。
   
   库给 Java 项目用、或者要对齐已有 Java API 时，少一层 get，调用方更自然。

2. 某些框架用反射罩字段，找不到 getter 就当没有
   
   这是最常见的必须加的：
   
   ```kotlin
   companion object {
       @JvmField
       val CREATOR = object : Parcelable.Creator<User> { ... }
   }
   ```
   
   Android 要的是 User.CREATOR 这个静态字段。不加 @JvmField，编出来是 User.Companion.getCREATOR()，系统找不到，解包会挂。
   
   JUnit 4 的 @Rule 也一样，必须是 public 字段：
   
   ```kotlin
   @JvmField
   @Rule
   val folder = TemporaryFolder()
   ```
   
   ButterKnife、部分 Gson/Jackson 配置，老 Android 框架，都是按字段去扫的。

3. Companion 里的属性，给 Java 当静态字段用
   
   ```kotlin
   class Api {
       companion object {
           @JvmField
           val TIMEOUT = 30
       }
   }
   ```
   
   Java 可以直接 Api.TIMEOUT，不用 Api.Companion.getTIMEOUT()。

4. 比 const val 松，又比普通 val 更像字段
   
   * 类型不限（对象、集合都行），const val 只能是 String/基本类型
   
   * 可以是实例属性，const val 只能顶层或 object
   
   * 值仍在字段上，不会内联，AOP/插桩还拦得住
   
   需要 Java 能直接读字段，但值不要被抄走时，用它，不要用 const val。
* 性能：少一次 getter，热路径上 JIT 本来就会内联 getXxx()，一般测不出来。

* 纯 Kotlin：两边都是 obj.name，加不加对你没感觉

* 代价：以后不能随便改成自定义 getter，Java 调用方会断；也不再经过 get，少一层封装，

@JvmField 是为了和 Java/老框架的字段约定对齐。没有这种约定，用普通 val 就行。

#### 需要避免的常见错误（Common Mistakes to Avoid）

❌在模块间暴露易变配置时使用 const val：如果某个配置值会在不同版本之间发生变化，将其声明为 const val 可能导致依赖模块出现问题，除非这些模块全部重新编译。

❌尝试将 const val 用于自定义对象：不能将自定义类实例或可变集合赋值给 const val。const val 严格只支持基本属性类型（Primitive Type）和字符串（String）。

❌认为 val 就意味着完全不可变：val 只保证引用（Reference）本身不可变，但它所指向的对象内部状态仍然可能发生变化：

```kotlin
val systemLog = mutableListOf("Init")
systemLog.add("Error") // Fully legal！ The reference cannot change, but the object is mutable.
```

## 关于 AOP 和 Gradle Plugin

#### Gradle Plugin

Grade 是 Android /Kotlin 项目的 “流水线工头”。点 Run/打正式包时，它按顺序干活：

```kotlin
你的 .kt 源码
    ↓ Kotlin 编译器
.class 字节码    ←  这时 const val 已经被抄进各个 class
    ↓ 还可以再加工
.apk / .aab
    ↓
装在手机上运行
```

Plugin 就是挂在这条流水线上的插件。官方就有：

* Android Gradle Plugin：编 Android 应用

* Kotlin 插件：编 Kotlin

也可以写自己的插件。

 <mark>常见用途</mark>:

* 自动插入埋点

* 把明文 URL / Key 加密

* 换环境地址

* 统计有哪些方法

* 插入权限检查

所以：

* 改源码、重新编译 -> 谁都能改到 const val

* 只在打包阶段改 class（Gradle Plugin 典型做法） -> const val 经常改不到

Pulugin 如果去 PhysicsEngine.class 里把 gravitationalConstant 改成 9.18：定义处改了，但是 calculateEnergy 里面还是 9.81，因为抄作业（内联）发生在 Plugin 上场之前。

如果是 val，则是 getstatic。Plugin 有两条路：

1. 只改初始值

2. 把这条“读字段”指令换成别的（读新字段、调解密函数、先打日志再读）。

#### AOP 是什么

AOP = Aspect-Oriented Programming，面向切面编程。

如果没有 AOP 每个按钮都要埋点：

```kotlin
fun onLoginClick() {
    Tracker.log("login_click")   // 每个地方都手写
    doLogin()
}

fun onPayClick() {
    Tracker.log("pay_click")
    doPay()
}
```

项目里有 200 个点击，就要写 200 次。这种“很多地方都要做、又和业务本身无关”的事，叫横切逻辑。

AOP 的想法是：业务代码保持干净，另外写一条规则：

凡是带 @Track 的方法，调用前自动打点。

```kotlin
@Track("login_click")
fun onLoginClick() {
    doLogin()   // 这里只关心登录
}
```

"在方法周围自动插一段逻辑"，这种插进去的东西就叫切面（Aspect）。

Android 里 AOP 很少在运行时“魔法附加”（哪样慢，也容易和系统打架）。更常见的是：

编译后用 Gradle Plugin 改字节码，把那段逻辑写进 class。

* AOP ：一种想法：统一插入横切逻辑，不要复制粘贴。

* Gradle Plugin + ASM 改 class = Android 里实现这个想法的常用手段。

切面要能插进去，字节码里必须还留着钩子：

* 调用了某个方法 -> 能在调用前后插入

* 读取了某个字段 -> 能在读取时插入

* 执行了某段方法体 -> 能在方法开头插入

const cal 把读取字段这个钩子拆掉了。调用方只剩一个字符串字面量，没有读这件事，切面也不知道该挂在哪。

#### 平时怎么选

* 真的永远不变、也不需要被工具改：用 const val
  
  例如“ok”、超时 300、日志 tag

* 可能换环境、要加密、要被<mark>埋点/热修/插桩</mark>改：用 val
  
  必要时故意写成运行时表达式，防止编译器抄走

Android 自己的 BuildConfig.DEBUG 就是这个坑：如果写成真正的 true，很多 if（BuildConfig.DEBUG）会被直接剪掉。所以官方常用运行时写法，保证读这个字段还在。

## 必须要用 const

记住一条硬规则：

Kotlin/Java 里，下面这些位置只接受编译器常量：

* 注解参数：@Xxx(...)

* 另一个 const val 的初始值

* Gradle Kotlin DSL 的 plugins {} 里的 id/version

#### 1. 注解处理器：这里是真的必须

注解不是普通函数调用。它是贴在代码上的标签，给编译器、注解处理器（<mark>KAPT/KSP/APT</mark>）看的。

处理器在 App 还没运行时就开始干活：读注解、生成 *Factory、路由表、数据库代码。它只能读已经写死在 class 里的常量，不能去执行你的 val。

```kotlin
object Api {
    const val USERS = "/users"   // 可以
    val users = "/users"         // 看起来一样，其实不行
}
interface UserApi {
    @GET(Api.USERS)    // OK：编译器把 "/users" 写进注解
    suspend fun list(): List<User>
    @GET(Api.users)    // 编译失败
    suspend fun list2(): List<User>
}
```

@Get(Api.users) 过不了，因为 Retrofit / 编译器要的是一个现在就能写进 class 的字符串，不是“等程序跑起来再问 Api.users”。

字符串拼接也一样，参与的每个变量都必须是 const：

```kotlin
object Api {
    const val BASE = "/v1"
    const val USERS = "$BASE/users"   // OK，还是编译期常量
}

@Query("SELECT * FROM $TABLE")  // TABLE 必须是 const val
```

<mark>ARouter</mark>、Hilt @Named、Room @Query、Retrofit @GET 都是这类：

```kotlin
object Di {
    const val API = "api"
}

@Named(Di.API)      // 必须 const
class ApiImpl
```

所以注解处理器必须用 const 的意思是：值要进注解，就必须是编译器常量；val 进不去，后面的生成代码也触发不了。

#### 2. BuildConfig 字段：要像官方 BuildConfig 那样能用

BuildCconfig 是 Android 构建时自动生成的一个 Java 类。每个模块、每种变体（debug/release、各种 flavor）各有一份。在代码里读它的字段，就能知道“当前这个包是怎么打出来的”。

```java
package com.example.app;

public final class BuildConfig {
    public static final boolean DEBUG = Boolean.parseBoolean("true");
    public static final String APPLICATION_ID = "com.example.app";
    public static final String BUILD_TYPE = "debug";
    public static final String FLAVOR = "";
    public static final int VERSION_CODE = 1;
    public static final String VERSION_NAME = "1.0";
}
```

代码里当普通变量用：

```kotlin
if (BuildConfig.DEBUG) {
    Log.d("Pay", "调试信息")
}

val url = BuildConfig.APPLICATION_ID
```

包名要写对。有多个 module 时，每个都有自己的 BuildConfig，别引错成 library 的。

AGP 8 起默认不生成，要在模块的 build.gradle.kts 里打卡：

```kotlin
android {
    namespace = "com.example.app"
    buildFeatures {
        buildConfig = true
    }
}
```

Sync / 编译一次后，才能 import com.example.app.BuildConfig。

* 最常见的用法
  
  1. 只在 debug 打日志、开调试工具
     
     ```kotlin
     if (BuildConfig.DEBUG) {
         Timber.plant(Timber.DebugTree())
         StrictMode.enableDefaults()
     }
     ```
     
     运行时读 DEBUG，debug 包是 true，release 包是 false。
  
  2. 按版本做逻辑
     
     ```kotlin
     if (BuildConfig.VERSION_CODE < 20) {
         showForceUpdate()
     }
     ```
  
  3. 自己加字段：不同环境不同地址
     
     ```kotlin
     android {
         buildFeatures { buildConfig = true }
     
         defaultConfig {
             buildConfigField("String", "API_URL", "\"https://api.example.com\"")
             buildConfigField("boolean", "LOG_HTTP", "false")
             buildConfigField("int", "TIMEOUT_SEC", "30")
         }
     
         buildTypes {
             getByName("debug") {
                 buildConfigField("String", "API_URL", "\"https://dev.example.com\"")
                 buildConfigField("boolean", "LOG_HTTP", "true")
             }
             getByName("release") {
                 buildConfigField("String", "API_URL", "\"https://api.example.com\"")
                 buildConfigField("boolean", "LOG_HTTP", "false")
             }
         }
     }
     ```
     
     注意字符串外面那层 \"...\"：Gradle 里要写成带引号的 Java 源码片段。最终生成的是：
     
     ```kotlin
     public static final String API_URL = "https://dev.example.com";
     ```
     
     ```kotlin
     val retrofit = Retrofit.Builder()
         .baseUrl(BuildConfig.API_URL)
         .build()
     
     if (BuildConfig.LOG_HTTP) {
         // 加 HttpLoggingInterceptor
     }
     ```
     
     跑 debug 就打开发环境，打正式包就走生产，不用改业务逻辑。
  
  4. 再加 flavor：同一套代码打多套包
     
     flavor 是 Android 的产品风味：同一套代码，按“产品线/渠道/环境”打出多套不同的包。
     
     ```kotlin
     flavorDimensions += "env" // 必须归到某个“维度”里
     productFlavors {
         create("staging") { // 创建了一个 staging
             dimension = "env"
             applicationIdSuffix = ".staging"
             buildConfigField("String", "API_URL", "\"https://staging.example.com\"")
         }
         create("prod") { // 创建了一个 prod
             dimension = "env"
             buildConfigField("String", "API_URL", "\"https://api.example.com\"")
         }
     }
     ```
     
     会得到 `stagingDebug`、`prodRelease`、stagingRelease、prodDebug 四个变体，每份 `BuildConfig` 的 `FLAVOR`、`API_URL` 不同。

AGP 生成的 Java 大致是：

```kotlin
public final class BuildConfig {
    public static final String APPLICATION_ID = "com.example.app";
    public static final String BUILD_TYPE = "debug";
    // DEBUG 是例外，下面单独说
}
```

public static final String X = "字面量" 就是 Java 的编译器常量，等价于 Kotlin 的 const val。

所以可以写：

```kotlin
@GET(BuildConfig.BASE_URL) // BASE_URL 若是真正的编译期常量，才能这样写
```

如果自己手写配置，漏了 const：

```kotlin
object BuildConfig {
    val BASE_URL = "https://api.example.com" // 普通 val
}
```

后果：

* @GET(BuildConfig.BASE_URL)  编不过

* 别的模块当注解参数用，也编不过

* 行为也不再像官方 BuildConfig （不会内联）

你想让字段和官方 BuildConfig 一样，能进注解、能当编译期常量，就必须是 const val / static final 字面量。

注意：BuildConfig.DEBUG 官方常常故意写成 Boolean.parseBoolean("true")，避免它变成编译器常量，防止 if(DEBUG) 被剪死。

String/int 这类是给注解用的，才必须是真正的 const。

```kotlin
public static final boolean DEBUG = true;
public static final boolean DEBUG = Boolean.parseBoolean("true");
```

第一个是编译器常量，现在就确定，字节码里 if(BuildConfig.DEBUG) 直接变成 if(true)，甚至把 if 删掉。而 Boolean.parseBoolean("true") 运行时才知道， 字节码里 if(BuildConfig.DEBUG) 还是 getstatic BuildConfig.DEBUG，先读字段在判断。

parseBoolean 对编译器来说是 “运行时算出来的”，所以它不敢内联、不敢剪枝。

Boolean.parseBoolean("true") 是为了让 DEBUG 不要变成编译期常量：运行时该 true 还 true，该 false 还 false；但编译器不能提前拆掉 if，也不能把 true/false 抄进所有 clasee。这样 debug/relase 行为正确，库和缓存也不会被内联坑到。

#### 3. Gradle 配置缓存：有的是必须，有的是应该

##### 真必须：plugin {} 块

build.gradle.kts 的 plugins {} 被 Gradle 单独、受限地编译，里面机会只能出现字面量或 const val。

```groovy
// buildSrc 或 convention 里
object Vers {
    const val agp = "8.7.0"     // 可以
    val kotlin = "2.0.0"        // 在 plugins {} 里不能用
}
```

```groovy
plugins {
    id("com.android.application") version Vers.agp     // OK
    kotlin("android") version Vers.kotlin              // 失败：不是编译期常量
}
```

版本号、插件 id 要抽成变量，必须 const val。这时语法硬限制，和 AOP 无关。

以前的一种写法。

##### 应该：Configuration Cache（配置缓存）

Gradle 的 Configuration Cache 会把“配置阶段”的结果序列化存盘。下次构建尽量不再重新跑一遍 build.gradle.kts。

它要求：配置阶段抓到的东西必须能安全存下来 -- 不能悄悄抓着 Project、不能每次去读会变的环境。

```gr
object Sdk {
    const val compile = 35          // 写死在字节码里，缓存复用也是 35
}

val compileSdk = project.extensions  // 抓住了 Project
    .getByType<...>                 // 配置缓存经常直接报错：cannot serialize
```

* const val：纯常量，不捕获工程对象，天然可缓存

* 普通 val：如果只是 val x = 35，多数可能过；一旦初始化时读了 project、文件、System.getenv()，缓存复用就会过期或失败。

更准确的说法是：

* plugins {} / 注解：必须 const val

* **Configuration Cache**：能用 const val 就用，避免把不可序列化的对象抓进缓存。

## 总结建议

* 如果是固定的配置项、全局变量、URL 或数值边界（且类型是基本类型或字符串），优先使用 const val 以获得更好的性能。

* 其他绝大多数只需要只读、不可修改的普通属性、局部变量或对象实例，统一使用 val。

## 参考内容

* [Kotlin const val vs val：字节码、性能与隐藏陷阱详解如果你正在优化 Android 应用，或者准备 - 掘金](https://juejin.cn/post/7651930056574550057)
