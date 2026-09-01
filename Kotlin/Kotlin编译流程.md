# Kotlin 编译流程

> * **词法分析**：切成 token，编译器从左到右扫描源码，切成最小有意义的碎片。
>   
>   负责认出关键字、标识符、数字、括号。拼写错误（比如 fun 写成 fuc），往往在这一部分就开始报错。
> 
> * **语法分析**：建成语法树。
>   
>   token 按 kotlin 语法规则组成树（PSI/AST）。
>   
>   ```
>   文件
>    └─ 属性声明：const val TIMEOUT = 30
>    └─ 函数声明：main
>         └─ 函数体
>              ├─ 局部变量 now = 调用 System.currentTimeMillis
>              └─ 调用 println(...)
>   ```
>   
>   这时编译器知道【这是一个属性声明】，但不知道 TIMEOUT 的类型，println 是哪个函数。
>   括号不匹配、fun 后面没有名字，会在这一步失败。
> 
> * **语义分析**（FIR）：真正【看懂】程序
>   K2 前端把语法树变成 FIR（Frontend IR）。这是编译器最关键的一步，前面说的【编译器知道 TIMEOUT 是 30】就发生在这里。
>   编译器会一次做这些事：
>   
>   * 名字解析：println 到底是 kotlin.io.println 还是自己写的？System 是 Java 的哪个类？
>   
>   * 类型推断：val now = System.currentTimeMillis() 没写类型，编译器推断出 Long。
>   
>   * 常量折叠/编译器常量：检查 TIMEOUT 是否满足 const val 规则：顶层或 object 里、基本类型或 String、右边是字面量。通过后，被记成编译器常量。后面读 TIMEOUT 的地方，都可以直接当 30 用。
>   
>   * 空安全、只能转换、可见性：String？能不能直接调用、private 能不能从外面访问，都在这里查。
>   
>   * 诊断：类型不匹配、val 被重新赋值、const val 用 System.currentTimeMillis()，都在这里报红。
> 
> * **FIR2IR***：换成后端能用的中间代码
>   FIR 很适合做类型检查，不太适合生成目标代码。
>   可以把它想成【还带着 Kotlin 语义的伪代码】：
>   data class 的 equals/copy 在这生成
>   object 的单例结构在这定下来
>   跨平台的 expect/actual 在这对齐
>   从这里开始，前端的【这是 kotlin】逐渐变成后段的【这时即将变成字节码的指令】。
>   Jetpack Compose、kotlinx.serialization 这类编译器插件，主要也是在 IR 上改这棵树，而不是直接改你的 .kt 文件。
> 
> * 后端降级：把 Kotlin 特有语法拆成更简单的操作
>   JVM 并不理解 inline fun、协程、when、默认参数。这一步就是把高级语法拆成 JVM 能表达的东西。
>   
>   * const val 内联到调用处：常量值被嵌进使用它的地方。
>   
>   * inline fun 内联：调用处直接贴上函数体。
>   
>   * 默认参数：合成重载方法。
>   
>   * object：带 INSTANCE 静态字段的类。
>   
>   * lambda：invokednamic 或匿名类
>   
>   * 协程 suspend：状态机类（Continuation）
>   
>   * 空安全!!：运行时空检查调用
>   
>   这一步结束，IR 已经接近【就是字节码的树形版】。
> 
> * 代码生成：写出 .class
>   后端遍历 IR，写出 JVM 字节码。同时还会写入 Kotlin Metadata：这时塞在 class 文件里的 kotlin 专属信息（哪些是 internal、哪些是suspend、属性是 val 还是 var）。JVM 自己不执行它，但编译器、反射、IDE 需要它。Java 互操作靠的也是这些标注。
>   产物是标准的 .class，和 javac 产出的是同一种格式。所以 kotiln 和 Java 能互相调用。
> 
> * 打包：把 class 和依赖放在一起
>   运行时还要把这些凑成 classpath：
>   自己的 class
>   kotlin-stdlib(listOf、println、空检查、集合等)
>   用到的其他库
>   Android: .class -> D8/R8 转成 .dex -> 打进 APK/AAB
>   到这里，编译期结束。磁盘上是已经内联好的字节码。
> 
> * JVM 启动：加载、链接、初始化
>   
>   * 加载（Loading）：ClassLoader 按名字找到 Hellokt.class，读进内存，变成 JVM 内部的类表示。
>   
>   * 链接（Linking）分三小步：
>     
>     * 验证：字节码是否合法、会不会破坏类型安全。
>     
>     * 准备：给静态字段分配默认值（0、null）
>     
>     * 解析：把自负引用变成直接引用，例如 System.currentTimeMillis 到底指向哪个方法。
>   
>   * 初始化（Initilization）
>      执行类的 ，也就是静态初始化。
>   
>   普通顶层 val timeout = 30 往往在这里真正赋成 30.
> 
> * 执行 main：真正的运行时
>   开始真正的运行代码，kotlin-stdlib 也在这时被加载；空安全失败抛出的 NullPointerException，也是编译器插入的运行时检查。

用一小段代码当贯穿例子：

```kotlin
const val TIMEOUT = 30

fun main(){
    val now = System.currentTimeMillis()
    println(TIMEOUT)
    println(now)
}
```

```
源码 .kt
 -> 词法/语法分析
 -> 语义分析（FIR）
 -> 转成后端 IR
 -> 降级 + 生成字节码 .class
 -> 打包 classpath / JAR
 -> JVM 加载、链接、初始化
 -> 执行 main
```

前半段是编译期（从词法分析到生成字节码，程序还没启动），后半段是运行时（JVM 加载链接初始化到执行 main，JVM 已经在跑）。

### 谁在驱动这条链

Gradle / Android Studio / IntelliJ 会：

1. 找到所有 .kt / .java 源文件

2. 准备 classpath（kotlin-stdlib、第三方库）

3. 调用 Kotlin 编译器（2.0 起默认是 K2）

4. 把输出交给打包、安装、启动

编译器内部可以想成两段：

* 前端：看懂源码、查错、确定每个名字是什么

* 后端：把已经理解的程序，翻译成 JVM/JS/Native 能执行的东西

### 源码：还只是文本

Hello.kt 此时只是字符。编译器还不认识 fun、TIMEOUT 这些词。

文件名会影响后面生成的类型：顶层函数 main 会进 HelloKt 这类文件类。

### 词法分析：切成 token

编译器从左到右扫描源码，切成最小有意义的碎片：

```kotlin
const  val  TIMEOUT  =   30
fun   main   (  )  {  ...  }
```

这一步不管 【30是不是 Int】，【TIMEOUT 能不能当常量】，只负责认出关键字、标识符、数字、括号。

拼写错成 fuc main ，往往在这附近就开始报错。

### 语法分析：建成语法树

token 按 Kotlin 语法规则组成树（PSI/AST）。例如：

```
文件
 └─ 属性声明：const val TIMEOUT = 30
 └─ 函数声明：main
      └─ 函数体
           ├─ 局部变量 now = 调用 System.currentTimeMillis
           └─ 调用 println(...)
```

这时编译器知道【这是一个属性声明】，但还不知道 TIMEOUT 的类型、println 是哪个函数。括号不配、fun 后面没有名字，会在这一步失败。

### 语义分析（FIR）：真正【看懂】程序

K2 （K2 是 Kotlin 2.0 起默认启动的新编译器前端）前端把语法树变成 FIR（Frontend IR）。这是编译器最关键的一步，前面说的【编译器已经知道 TIMEOUT 是 30】就发生在这里。

编译器会一次做这些事：

**名字解析**

println 到底是 kotlin.io.println，还是你自己写的？System 是 Java 的哪个类？

**类型推断**

val now = System.currentTimeMillis() 没写类型，编译器推断出 Long。TIMEMOUT 右边是 30，类型是 Int。

**常量折叠/编译期常量**

检查 TIMEOUT 是否满足 const val 规则：顶层或 object 里、基本类型或 String、右边是字面量。通过后，它被记成编译期常量。后面所有读 TIMEOUT 的地方，都可以直接当 30 用。

**空安全、智能转换、可见性**

String？能不能直接调用、private 能不能从外面访问，都在这查。

**诊断（checkers）**

类型不匹配、val 被重新赋值、const val 用 System.currentTimeMillis()，都会在这报红。

过了这一步，程序在 Kotlin 语法层面上已经合法。还没有 .class，更没有【跑起来】。

### FIR2IR：换成后端能用的中间代码

FIR 很适合做类型检查，不太适合生成目标代码。编译器把它翻译成另一棵树：IR（Intermediate Representation）。

可以把它想成【还带着 Kotlin 语义的伪代码】：

* data class 的 equals / copy 在这生成

* object 的单例结构在这定下来

* 跨平台的 expect/actual 在这对齐

从这里开始，前端的 【这是 Kotlin】逐渐变成后端的 【这是即将变成字节码的指令】。

Jetpack Compose、kotlinx.serialization 这类编译器插件，主要也是在 IR 上改这棵树，而不是直接改你的 .kt 文件。

### 后端降级：把 Kotlin 特有语法拆成更简单的操作

JVM 并不原生理解 inline fun、协程、when、默认参数。后端会做一连串 lowering（降级），把高级语法拆成 JVM 能表达的东西。

const val 内联到调用处

println(TIMEOUT) 在 IR/字节码里变成 println(30)。常量值被嵌进使用它的地方，运行时不必再读一个叫 TIMEOUT 的字段。

inline fun 内联

调用处直接贴上函数体，不是普通的 invokevirtual。这和 const val 内联是同一类思想：编译器把内容拷到调用处。

其他降级举例

| Kotlin 写法  | 大致降成               |
| ---------- | ------------------ |
| 默认参数       | 额外的合成重载方法          |
| object     | 带 INSTANCE 静态字段的类  |
| lambda     | invokedynamic 或匿名类 |
| 协程 suspend | 状态机类（Continuation） |
| 空安全!!      | 运行时空检查调用           |

这一步结束，IR 已经接近【就是字节码的树形版】。

### 代码生成：写出 .class

后端遍历 IR，写出 JVM 字节码。你的例子大约变成：

```
HelloKt.class
   - 可能根本没有 TIMEOUT 字段（已被内联）
   - public static void main(String[] args)
       调用 System.currentTimeMillis
       把结果存进局部变量 now
       打印 30
       打印 now
```

同时还会写入 Kotlin Metadata：这是塞在 class 文件里的 Kotlin 专属信息（哪些是 internal、哪些是 suspend、属性是 val 还是 var）。JVM 自己不执行它，但编译器、反射、IDE 需要它。Java 互操作靠的也是这些标注。

> Java 互操作就是：Kotlin 和 Java 能直接互相调用，像用同一门语言的代码一样，不必写一层翻译器或 JNI。

产物是标准的 .class，和 javac 产出的是同一种格式。所以 Kotlin 和 Java 能互相调用。

如果目标不是 JVM，这里会分叉：

* JS：生成 .js/.mjs

* Native：经 LLVM 生成可执行文件或库

* Wasm：生成 .wasm

* 中间库常用 KLIB（.klib），给多平台复用 IR

### 打包：把 class 和依赖放在一起

运行时还要把这些凑成 classpath：

* 自己的 HelloKt.class

* kotlin-stdlib（listOf、println、空检查、集合等）

* 你用到的其他库

常见形态：

* 开发时（给 JVM/测试/Instant run 用）：build/classes + Gradle 依赖（build.gradle 里写的库，Gradle 下载后放在本地缓存里）

* 发布（拷给被人、部署服务器）：JAR（可执行 JAR 会把 stdlib 打进去，或用 -include-runtime）

* Android：.class -> D8/R8 转成 .dex -> 打进 APK/AAB

到这里，编译器结束。磁盘上是已经内联好的字节码，还没有任何 now = currentTimeMillis() 被执行。

### JVM 启动：加载、链接、初始化

运行：

```
kotlin HelloKt
# 或
java -cp hello.jar:kotlin-stdlib.jar HelloKt
```

JVM 不会一次加载全部类，而是按需要来。对入口类大约是：

**加载（Loading）**

ClassLoader 按名字找到 HelloKt.class，读进内存，变成 JVM 内部的类表示。

**链接（Linking）分三小步：**

1. 验证：字节码是否合法、会不会破坏类型安全

2. 准备：给静态字段分配默认值（0、null）

3. 解析：把符号引用变成直接引用，例如 System.currentTimeMillis 到底指向哪个方法。

**初始化（Initialization）**

执行类的 <clinit> ，也就是静态初始化。

普通顶层 val timeout = 30 往往在这里真正赋成 30。

const val TIMEOUT 如果已被内联，这里可能根本没有这个字段。

初始化完成前，别的线程去读这个类的静态 val，仍可能看到默认值。这就是之前说的【安全发布】边界。

> 顶层：写在文件里，但不在任何 class / object / fun 里面
> 
> 普通顶层：与 const val 区分。
> 
> ```kotlin
> // Hello.kt
> 
> const val TIMEOUT = 30          // 顶层：直接写在文件里
> val timeout = 30                // 也是顶层，但是普通 val，不是 const
> 
> fun greet() {                   // 顶层函数
>     val inner = 1               // 不是顶层：在函数里面
> }
> 
> class User {
>     val name = "Alice"          // 不是顶层：在 class 里面
> 
>     companion object {
>         const val TYPE = "user" // 不是顶层：在 companion object 里
>     }
> }
> 
> object Config {
>     const val HOST = "api.com"  // 不是顶层：在 object 里
> }
> ```
> 
> const val：编译期常量，会被内联，运行时往往没有这个字段。
> 
> 普通 val：只是一个不能重新赋值的顶层属性，运行时会变成某个 xxxKt 类上的静态字段，在累初始化(<clinit>)时赋值。
> 
> timeout 和 greet 方法大致相当于 Java:
> 
> ```java
> public final class Hellokt {
>     private static final int timeout = 30;
>     public static final void greet() {}
> }
> ```
> 
> 这个 timeout 不在某个你写的 class 里，而是在编译期生成的 Hellokt 里，类第一次加载时才赋成 30。
> 
> const val 不能写在 class 实例成员里，也不能写在函数里。只允许：
> 
> 1. 顶层
> 
> 2. object 里
> 
> 3. companion object 里

安全发布指的是：一个对象构造完成后，把它交给别的线程时，对方一定能看到已经初始化完的最终状态，而不是半成品。

### 执行 main：真正的运行时

初始化完成后，JVM 调用：

```kotlin
HelloKt.main(args)
```

这时才进入写的函数体：

1. 执行 System.currentTimeMillis() -- 运行时才能知道现在几点

2. 把返回值放进局部变量 now

3. println(30) -- 30 是编译期就写进字节码的

4. println(now) -- 去读刚才那个局部变量

执行引擎大致是：

* 开头用解释器一条条跑字节码

* 热点方法由 JIT 编成机器码

* 部分 JVM/Android ART 还会 AOT（安装时或启动时先编写好）

* 对象在堆上分配，垃圾回收器稍后回收

kotlin-stdlib 也在这时被加载。你写的 listOf(1,2)，运行时其实是在调 stdlib 里的 Java/Kotlin 方法；空安全失败抛的 NullPointerException，也是编译器插入的运行时检查。

> stdlib 是 standard library （标准库）的缩写。在 Kotlin 里就是 Kotlin-stdlib ：语言自带的那一套基础 API，不用自己写，编译和运行都默认会用到它。
> 
> AOT 是 Ahead-Of-Time Compilation（提前编译）：在程序跑起来之前，就把字节码（或 IR）编成本地机器码。
> 
> 对比 JIT（Just-In-Time）：程序已经在跑了，发现某段代码很热（代码很热=这段代码正在被频繁执行，短时间内跑了很多次），再临时编成机器码。
> 
> Android ART 是典型的 AOT。安装 APK 或空闲时，系统可能把 .dex 提前编成本机指令，存在 oat -类文件里。打开 App 时，不少代码已经是机器码，不必等 JIT 热身。现代 ART 通常是 AOT + JIN + 解释器 混用：
> 
> * 安装/空闲：AOT 编一部分
> 
> * 运行中：解释或 JIT 补其余热点
> 
> * 下次启动：用上次的剖面继续 AOT
> 
> GraalVM Native Image（GraalVM 是 Oracle 的一个 JDK/运行时，里面有编译器 Graal，Native Image 是它的一个功能：在构建时用 AOT 把 JVM 字节码编成单一本地可执行文件）/Kotlin Native（Kotlin 自己的后端，Kotlin 编译器直接生成原生代码，为的是 iOS 等没有 JVM 的平台） 更彻底：直接生成可执行文件，运行时甚至可以没有 JVM。

### 总结：

| 步骤        | 阶段  | 输入       | 输出       | 人能感知到的                |
| --------- | --- | -------- | -------- | --------------------- |
| 1 源码      | 编译前 | 字符       | .kt 文件   | 写的代码                  |
| 2 词法      | 编译期 | 字符       | token    | 关键字被认出来               |
| 3 语法      | 编译期 | token    | 语法树      | 括号/结构错误               |
| 4 语义      | 编译期 | 语法树      | 带类型的 FIR | 红线、类型错误、const val 被确认 |
| 5 FIR2IR  | 编译期 | FIR      | 后端 IR    | data class 方法生成       |
| 6 降级      | 编译期 | IR       | 更简单的 IR  | 内联、协程状态机              |
| 7 生成      | 编译期 | IR       | .class   | 出现字节码                 |
| 8 打包      | 编译后 | class+依赖 | JAR/APK  | 可分发的程序                |
| 9 加载链接初始化 | 运行时 | .class   | JVM 里的类  | 静态 val 赋值             |
| 10 执行     | 运行时 | 字节码      | 程序行为     | 打印、网络、耗时              |

## Java 编译流程

Java 的编译流程和 kotlin 一样，也是先变成 .class，再交给 JVM 跑。差别是中间只由 javac 处理，没有 FIR/IR 那两套 Kotlin 专用层。

用这段代码贯穿：

```java
public class Hello {
    public static final int TIMEOUT = 30; // 编译器常量，会内联

    public static void main(String[] args) {
        long now = System
    }
}
```
