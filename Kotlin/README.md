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

Kotlin 每个类型都有一个明确的空状态：非空状态和可空状态，编译器在编译期强制检查：用 T/T? 规定哪里运行null，不合规的代码编不过，NPE 只会在特定场景出现（显式抛（故意抛出 NPE，或者调用了会抛它的函数）、unsafe 操作（用哪个不安全操作强行当非空!!）、Java 互调（null 从 Java 漏出来））。

- Kotlin 提供安全调用操作符 ?.

- 非空断言运算符 !!

- Elvis 操作符 ?:

- 智能转换：Kotlin 的编译期能够进行智能转换（smart cast），这意味着如果一个变量被检查过是否为 null，那么在检查之后，编译器会认为这个变量是非空的，直到它的值再次改变或者作用域结束。

- 空合并函数：除了 ?: 外，Kotlin 还支持 let、apply、run、with 和 also 等范围函数，这些函数在处理可空类型时非常有用。

- 空检查与类型断言：类型断言 as 和安全类型断言 as? 在处理可能为 null 的对象时非常有用。

Kotlin 不是绝对的空指针安全，比如 Kotlin 调用 Java 代码，Null 安全性被破坏了。

平台类型：Java 返回的值在 Kotlin 中显示为 String!，既不是 String 也不是 String?，需要手动处理。

- Java 补上 @Nullable 或 @NotNull，平台类型就会消失。

- Java 基本类型（byte、short、int、long、float、double、char、boolean 8个，不能为 null）是普通非空 Kotlin 类型，不是 T!.

- Java 包装类型（Byte、Short、Integer、Long、Float、Double、Character、Boolean）（可以为 null） ，才是平台类型。

- 自定义类型和 String 规则相同，都是引用。

非空断言什么时候用：

**能证明这里不是 null，并且空了就该立刻崩，而不是继续跑。**

- 编译器看出去，但刚保证过非空
- Java/系统 API 标成可空，契约却保证有值，比如 findViewById（现在更常见用 ViewBinding）
- 测试代码
- 非空状态必须立刻炸

更推荐使用 requireNotNull 和 checkNotNull:

- requireNotNull：为空则抛出 IllegalArgumentException 异常，用在函数入口、校验参数。
- checkNotNull：为空抛出 IllegalStateException 异常，用在业务做到一半，内部状态该有却没有。
- require 和 check 类似，不脱检查的是布尔条件。

Kotlin 中对空安全背后的处理套路如下：

1. 非空类型的属性编译器添加 @NotNull 注解，可空类型添加 @Nullable 注解。
2. 非空类型直接对参数进行判空，如果为空直接抛出异常。
3. 可空类型，如果是 ?. 判空，不空才执行后续代码，否则返回 null；如果是 !!，空的话直接抛出NPE 异常。
4. as 操作符会判空，空的话直接抛出异常，不为空才执行后续操作，没做类型判断运行时可能会报错。
5. as? 实际上就是 instanceof + 原引用或 null，这里有一点注意：as? 处理后的类型可能为空，所有调用 as? 转换后的对象还需要添加安全调用操作符 ?.

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

## [Kotlin 之 data class]

Data class 在编译时会自动生成 equals（）、hashCode()、toString()、copy（） 以及一组 componentN() 方法。

* hashCode（）、equals() 是用来比较对象内容是否相同，多用于 HashMap 等容器中。

* toString() 是用来打印对象内容。

* copy() 实现了复制功能，浅拷贝，原理：在 copy 方法里面 new 一个新实例，浅拷贝所有属性。

* componentN() 提供了快速访问元素的功能。

data class 的功能 class 都能实现，data class 只是 Kotlin 提供的具有常用数据 model 功能的类，用来提升开发效率。

非常适合用作数据传输对象、API 响应模型或配置实体等场景。

data class 必须在主构造函数中定义至少一个 val 或 var 属性，否则编译器会报错。

data class 不能继承其他类，只能实现接口，这是为了保证自动生成的方法行为一致且不会被父类干扰。

data class 也不能是 abstract、open、sealed 或 inner。这些限制确保数据类的结构固定、行为可预测。

在 Kotlin 1.5+，数据类可以结合 sealed 使用，实现有限集合的数据子类。

这样设计保证数据类主要专注于存储和传递数据，而不承担复杂继承或业务逻辑，从而保持简洁和安全。

inner：内部类，Kotlin 里默认写在类里面的类拿不到外层的 this。加上 inner 才是内部类，每个实例都绑着一个外层对象。（inner 持有外层引用，注意内存泄漏，能不用就不用）。

data class 中的普通变量和普通 class 的普通变量是相同的含义：不参与到 equals、hashCode、copy、toString、componentN 中。

在 ViewModel 中使用 Kotlin 的 data class 来定义 UI 状态（UI State）是现代 Android 开发（特别是 Jetpack Compose）中的标准最佳实践。

页面手机 state，每次 _state.value = ...copy(...) 都是新的 state，Compose/订阅方发现对象换了，按新数据刷新。

ViewModel 用一个只读的 data class 表示当前界面；每次变化都 copy 出新状态再发出去，而不是改旧对象上的字段。这样更新可追踪，Compose/StateFlow 也才能可靠刷新。（copy() 方法中对象属性值没有变化的话，equals 会是 true，那就不会触发更新，所以需要注意 list、map、array、StringBuilder、Date、Calendar、有 var、MutableMap、HashMap、MutableSet、MutableList、嵌套对象包含这些类型的属性，如果有这些，copy 之后，equals 方法返回 true，也不会触发刷新。）

data class 为什么适合，因为它会生成：

- copy：只改一部分字段，做出新状态（精髓，copy 之所以是精髓，是因为它让不改旧的，只产新的写起来很轻）
- equals：StateFlow/Compose 用来判断【要不要刷新】
- toString：打日志能看到整份状态

日常开发使用 data class 的地方：

- ViewModel 状态使用 data class + copy 轻松实现不可变更新（数据对象创建后不改内部，变化用 copy 得到新对象，copy 之所以是精髓，是因为它让不改旧的，只产新的写起来很轻）。
- Rom 数据库 Entity(表示一张表，属性对应列)使用 data class，自动实现 toString，方便日志。测试、去重、和内存里另一份对比时，常靠 equals 方法。update 变更数据时可以使用 copy 方法。
- Retrofit DTO(Data Transfer Object，专门对接接口的数据盒子，字段名、解构跟着 JSON/后段走，常加 Gson/Moshi 的 @SerializaedName) 转 Entity(对象)：意思是接口返回的 JSON 先收成一套【网络用的对象】，再翻译成数据库（或业务）用的另一套对象。这里的 DTO 和 Entity 都是使用的 data class。

数据类通过自动生成常用方法，大幅减少了样板代码，让开发者可以专注于数据本身。它适用于存储和传递数据，而非承载复杂业务逻辑。借助 copy()、解构声明以及自动生成的 equals（）、hashCode()、toString()，数据类让对象的比较、复制、打印和解构操作变得直观而高效。

合理使用数据类，保持构造函数简洁、属性直观且尽量不可变，不仅可以减少重复代码，还能提升可读性和维护性，使 Kotlin 开发体验更加简洁优雅。

## [Kotlin 相等比较]

Kotlin 中 == 比较的是数值是否相等，而 === 比较的是两个对象的地址是否相等。

对基本类型, 对基本类型 === 和 == 生成同一套指令，都是比数值。

- 非空 Int：是值，没有对象，没有地址，=== 只能比大小。
- 可空 Int？：是对象，有地址，=== 比是不是同一个实例；-128~127 才可能装上 IntegerCache。

在 Java 平台上，数值被 JVM 虚拟机以字节码的方式物理存储的，除非需要做可空标识（比如说 Int？)或者涉及范型。在后者中数值是装箱的。

```kotlin
val a: Int = 999 // a 就是个数值，不是个对象，不涉及装箱的问题。
val b: Int? = a // b 是个对象，涉及装箱问题
val c: Int? = a
println(b == c) // true
println(b === c) // false

val d: Int = a
val e: Int = a
println(d == e) // true
println(d === e) // true

val f: Int? = 999
val g: Int? = f
val h: Int? = f
println(g == h) // true
println(g === h) // true
```

Integer、Long 等包装类型会有缓存，如果Int a 数值是在 -128 到 127 之间，数值是不会重新包装成新对象，而是使用 IntegerCache 里缓存的数据。那么 b===c 也是 true。

会缓存的类型有：Byte、Short、Int、Long、Char、Boolean。

字符串走的是字符串常量池+intern：

1. 源码里的字面量 “hello”，编译进 class 的常量池。
2. 类加载时，JVM 保证池里只有一份 "hello"。
3. 所有写 “hello”的地方，都拿到同一个 String 对象。

intern() 是把这个字符串换成常量池里的那一份。字面量已经换过了；new 出来的默认不换。它只影响是不是同一个对象（===），不影响内容是否相等（==）。

- 与 Java 的差异
  - 判断值相等，使用 equals()
  - 判断地址相等，使用 ==

Kotlin 里面的对象、String 的 == 会调用 equals() 方法。

data class 会生成 equals()，所以 == 比的是主构造属性，不是地址。

自己写的类如果没有重写 equals()，继承的是 Any.equals()，那就是比引用，这时 == 和 ===结果一样。如果某个类有比较属性是否相等的需求，需要自行覆盖 equals() 方法。

基本类型的 == 不会调用 equals() 方法，可空类型会装箱，才走包装类型的 equals()。

Kotlin 的 == 和 === 有一定的类型判断能力。Kotlin 编译期会检查操作符两边对象的类型，如果发现两边数据不存在相等的可能时，会直接出现编译错误。

满足下面两点之一，Kotlin 编译期才不会出现编译错误：

- 两边对象类型相同
- 左右对象存在父子关系（继承或实现）

## [Kotlin const val 与 val]

const val 是编译器常量（在编译时确定值），而 val 是运行时只读变量（在运行时确定值）。

kotlin 编译器会将 const val 视为一个字面量常量。在编译过程中，编译器会直接把属性访问替换为实际的常量值，并将该值写入使用它的字节码指令中。

- 只能修饰 String 或基本数据类型。
- 不能修饰自定义类或复杂对象。
- 只能在顶层、object 声明或者 companion object 内部定义，不能在普通类的方法局部作用域中声明。

即使一个普通的 val 在编译时已经能够确定其值，编译器通常也不会进行内联。它会被视为一个真正的属性，拥有实际的内存存储位置，并通过字段访问或 Getter 方法获取值。

const val 与 val 在性能上的差异本质上是一种权衡：

- val 的内存占用：会在每个对象实例中为该字段分配运行时存储空间。
- const val 的内存占用：不需要在对象实例中为字段分配运行时存储空间，但它们仍然会占用类文件和常量池中的空间。由于常量值会被复制到每个调用点，如果将较长的字符串或较大的值定义为 const val 并被大量使用，可能会轻微增加编译后 .class 或 .dex 文件的体积。

const val 的值会在编译期间被直接复制到调用方的字节码中，因此在多模块项目或公共库中，它会带来一个非常大的架构风险。库模块的值是 constant val，app 模块编译时会将值写入它本身的字节码中，如果后面修改值，但没有对 app 进行完整重新编译，那 app 模块会继续使用旧值。

@JvmField：如果希望对于普通的 val 属性或 var 属性，访问时不让 Kotlin 编译器生成 Getter 方法，运行时直接读取数据，但也不会像 const val 那样进行编译器内联，那么可以使用 @JvmField。性能上会少一次 getter，带来一定的性能优化，又避免了 const val 在多模块项目中由于编译期内联而引发的二进制兼容性问题。为了和 Java/老框架的字段约定对齐时，也会使用。

在使用AOP 和 Gradle Plugin 的时候，在打包阶段只改 class时，改 const val 定义处，一般改不到已经用到它的地方。

必须要用 const val: 注解参数、BuildConfig 字段。

- 如果是固定的配置项、全局变量、URL 或数值边界（且类型是基本类型或字符串），优先使用 const val 以获得更好的性能。
- 其他绝大多数只需要只读、不可修改的普通属性、局部变量或对象实例，统一使用 val。

## [Kotlin lateinit 与 by lazy]

在 kotlin 中，lateinit 和 by lazy 都是用于延迟初始化。

**lateinit**

lateinit 只能用于 var（可变类型），手动选择在未来的某个时间点进行初始化。只能是非空对象（不能是基本数据类型），不保证线程安全。

lateinit 属性不能写自定义 getter/setter 方法，如果写，编译期直接报错，代码不会通过编译。

常见场景：依赖注入（如 Dagger/Hilt）、Android 的 onCreate 生命周期回调中初始化 View。

lateinit 实现是通过对 get 和 set 方法做了一些操作，get 方法中会判断值是否为 null，如果为 null 就抛出异常。

lateinit 本质：给非空的 var 留一个可以先不赋值的字段，字段用 null 表示还没初始化。

lateinit 不能修饰基本类型。原因是因为基本类型有默认值，无法判断是否还没初始化。

**by lazy**

by lazy 只能用于 val（只读变量），在变量第一次被使用时自动执行初始化逻辑，并且默认是线程安全的。支持任意类型（包括基本数据类型和可空类型）。

by lazy 是一个属性委托。它包含一段初始化 lambda 表达式。只有当代码中第一次读取该字段是，该段表达式才会执行，并将结果缓存起来，后续读取直接返回保存值。

常见场景：创建开销较大的对象（比如数据库连接、复杂的计算结果、大型数据集合），或者只有特定条件下才会用到的变量。

by：kotlin 的委托属性，就是这个变量的 get、set 都是委托给了另外一个类去操作。如果是 var 变量，必须要有 setValue 和 getValue 两个方法，val 变量不需要 setValue 方法。

lazy：一个 kotlin 的函数。实现使用 synchroinzed 来保证线程安全，如果值已经存在则返回，如果值不存在则创建并保存。

现在可以使用注入框架（Hilt）可以 @Inject lateinit var，减少自己管理对象怎么创建、写在哪。

**选择：**

- 如果希望这个变量是不可变的（只读） -> 选择 by lazy
- 如果初始化的控制权在外部（例如生命周期回调、依赖注入框架） -> 选择 lateinit var。
- 如果需要延迟初始化基本数据类型 -> 必须选择 by lazy。

从本质上来说，lateinit 和 by lazy 的区别是体现在内存上：

lateinit 修饰的属性，会在内存中创建，只不过没有赋值。

by lazy 修饰的属性，只有在使用的时候才会在内存中创建。

**get/set、getter/setter 与 getValue/setValue**

get/set：属性上写的访问器，写在属性声明后面，用来规定这个属性被读取、被赋值时做什么。

getter/setter：getter 就是 get，setter 就是 set。get/set 是 kotlin 源码里的写法，getter/setter 是编译后的 getxxx/setxx 方法。

getValue/setValue：委托对象上的运行符。用在 by 后面的那个对象上，属性自己的 getter/setter 仍会生成，方法体改成去调用委托。

## 资料收集

1. [Android：这是一份全面 & 详细的Kotlin入门学习指南](https://juejin.im/entry/5d36609ff265da1b88121bc3)
2. https://www.jianshu.com/p/a90e39a6079e Kotlin之集合排序(sortBy、sortByDescending)
3. https://blog.csdn.net/u010368726/article/details/122285884 kotlin的runCatching代替try catch的各种用法
4. runCatching https://blog.csdn.net/u010368726/article/details/122285884
5. 变量、常量 https://www.jianshu.com/p/501f87506ac3/
6. http://events.jianshu.io/p/5f68c815407a Kotlin-runCatching
7. https://www.jianshu.com/p/3c401a51669d/ Kotlin基础知识一：classes, objects和interfaces
8. [Kotlin刨根问底(二)：for循环引起的一起“血案”不难看出底层是：while循环 + Iterator(迭代器) - 掘金](https://juejin.cn/post/6844904065093074958)
