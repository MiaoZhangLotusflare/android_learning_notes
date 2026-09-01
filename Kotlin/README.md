# Kotlin

* [动脑学院 kotlin 学习笔记]
* [Kotlin 之 String]
* 协程
  - [Kotlin 协程概念]
  - Kotlin 协程的使用
  - Kotlin 协程与 Flow
  - Kotlin 协程实现
  - [Kotlin 协程疑问]
* [利用 Kotlin 特点]
* [Kotlin 之 let、with、run、apply、also 函数的使用]
* [Kotlin Flow 的 collect 和 collectLatest 有什么区别]
* [Kotlin Multiplatform]
  - Koin
  - 官方文档翻译
    - [Supported platform]
    - [Platform differences]
    - [Resources overview]
    - [Setup and configuration]
    - Access and usage

## [Kotlin 空安全]

## [Kotlin 编译流程]

#### 1. 词法分析：切成 token，编译器从左到右扫描源码，切成最小有意义的碎片。

负责认出关键字、标识符、数字、括号。拼写错误（比如 fun 写成 fuc），往往在这一部分就开始报错。

#### 2. 语法分析：建成语法树。

token 按 kotlin 语法规则组成树（PSI/AST）。

```
文件
 └─ 属性声明：const val TIMEOUT = 30
 └─ 函数声明：main
      └─ 函数体
           ├─ 局部变量 now = 调用 System.currentTimeMillis
           └─ 调用 println(...)
```

这时编译器知道【这是一个属性声明】，但不知道 TIMEOUT 的类型，println 是哪个函数。
括号不匹配、fun 后面没有名字，会在这一步失败。

#### 3. 语义分析（FIR，前端中间表示）：真正【看懂】程序

K2 前端把语法树变成 FIR（Frontend IR）。这是编译器最关键的一步，前面说的【编译器知道 TIMEOUT 是 30】就发生在这里。
编译器会一次做这些事：

* 名字解析：println 到底是 kotlin.io.println 还是自己写的？System 是 Java 的哪个类？

* 类型推断：val now = System.currentTimeMillis() 没写类型，编译器推断出 Long。

* 常量折叠/编译器常量：检查 TIMEOUT 是否满足 const val 规则：顶层或 object 里、基本类型或 String、右边是字面量。通过后，被记成编译器常量。后面读 TIMEOUT 的地方，都可以直接当 30 用。

* 空安全、只能转换、可见性：String？能不能直接调用、private 能不能从外面访问，都在这里查。

* 诊断：类型不匹配、val 被重新赋值、const val 用 System.currentTimeMillis()，都在这里报红。

#### 4. FIR2IR：换成后端能用的中间代码

FIR 很适合做类型检查，不太适合生成目标代码。
可以把它想成【还带着 Kotlin 语义的伪代码】：
data class 的 equals/copy 在这生成
object 的单例结构在这定下来
跨平台的 expect/actual 在这对齐
从这里开始，前端的【这是 kotlin】逐渐变成后段的【这时即将变成字节码的指令】。
Jetpack Compose、kotlinx.serialization 这类编译器插件，主要也是在 IR 上改这棵树，而不是直接改你的 .kt 文件。

#### 5. 后端降级：把 Kotlin 特有语法拆成更简单的操作

JVM 并不理解 inline fun、协程、when、默认参数。这一步就是把高级语法拆成 JVM 能表达的东西。

* const val 内联到调用处：常量值被嵌进使用它的地方。

* inline fun 内联：调用处直接贴上函数体。

* 默认参数：合成重载方法。

* object：带 INSTANCE 静态字段的类。

* lambda：invokednamic 或匿名类

* 协程 suspend：状态机类（Continuation）

* 空安全!!：运行时空检查调用

这一步结束，IR 已经接近【就是字节码的树形版】。

#### 6. 代码生成：写出 .class

后端遍历 IR，写出 JVM 字节码。同时还会写入 Kotlin Metadata：这时塞在 class 文件里的 kotlin 专属信息（哪些是 internal、哪些是suspend、属性是 val 还是 var）。JVM 自己不执行它，但编译器、反射、IDE 需要它。Java 互操作靠的也是这些标注。
产物是标准的 .class，和 javac 产出的是同一种格式。所以 kotiln 和 Java 能互相调用。

#### 7. 打包：把 class 和依赖放在一起

运行时还要把这些凑成 classpath：
自己的 class
kotlin-stdlib(listOf、println、空检查、集合等)
用到的其他库
Android: .class -> D8/R8 转成 .dex -> 打进 APK/AAB
到这里，编译期结束。磁盘上是已经内联好的字节码。

#### 8. JVM(Java 虚拟机) 启动：加载、链接、初始化

* 加载（Loading）：ClassLoader 按名字找到 Hellokt.class，读进内存，变成 JVM 内部的类表示。

* 链接（Linking）分三小步：
  
  * 验证：字节码是否合法、会不会破坏类型安全。
  
  * 准备：给静态字段分配默认值（0、null）
  
  * 解析：把自负引用变成直接引用，例如 System.currentTimeMillis 到底指向哪个方法。

* 初始化（Initilization）
   执行类的 ，也就是静态初始化。
  
  普通顶层 val timeout = 30 往往在这里真正赋成 30.

#### 9. 执行 main：真正的运行时

开始真正的运行代码，kotlin-stdlib 也在这时被加载；空安全失败抛出的 NullPointerException，也是编译器插入的运行时检查。

## 资料收集

1. [Android：这是一份全面 & 详细的Kotlin入门学习指南](https://juejin.im/entry/5d36609ff265da1b88121bc3)
2. https://www.jianshu.com/p/a90e39a6079e Kotlin之集合排序(sortBy、sortByDescending)
3. https://blog.csdn.net/u010368726/article/details/122285884 kotlin的runCatching代替try catch的各种用法
4. runCatching https://blog.csdn.net/u010368726/article/details/122285884
5. 变量、常量 https://www.jianshu.com/p/501f87506ac3/
6. http://events.jianshu.io/p/5f68c815407a Kotlin-runCatching
7. https://www.jianshu.com/p/3c401a51669d/ Kotlin基础知识一：classes, objects和interfaces
8. [Kotlin刨根问底(二)：for循环引起的一起“血案”不难看出底层是：while循环 + Iterator(迭代器) - 掘金](https://juejin.cn/post/6844904065093074958)
