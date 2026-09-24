# 面试题- Kotlin 基本语法与核心特性

## 1. val 和 var 到底有什么区别？val 就一定线程安全吗？

val 和 var 的差别只在引用能不能重新赋值，不在对象能不能改，也不保证线程安全。

val 只读引用（类似 Java 的 final），var 可变引用。

指向的对象能够改的意思是：变量这个名字不能换，但名字背后那份数据有时还能被改掉。

比如 val list = mutableListOf(1,2,3)

val 锁住的是变量 list，对象本身（可变列表）是可以修改的。

val = 这个名字以后不能指向别的东西，var = 这个名字可以换指向。

**Java 的 final**

Java 的 final 表示不能再改这个绑定：变量不能重新赋值，方法不能被重写，类不能被继承。

局部变量、成员字段、方法参数、static 字段都可以加 final。

成员 final 必须在构造结束前赋好（声明时、构造函数或实例初始化块里）。

static final 对应 kotlin 的 const val。、

kotlin 的类默认也是 final，要继承写 open class。

kotlin 的默认方法就是 final，要开发需要写 open。Java 反过来：默认能重写，加上 final 才锁住。

**val 就一定线程安全吗？**

val 不一定线程安全。val 只保证【这个变量本身不会被换成另一个引用】，线程安全要看三件事：

1. 引用可不变（val 给的）

2. 对象不可变（val 不保证）

3. 可见性 / 初始化安全（val 也不完全保证）

val x by lazy {...} 的首次初始化是线程安全的。

<mark>by lazy</mark>

<mark>并发工具：AtomicInteger、Mutex、ConcurrentHashMap、@Volatile（只保证可见性，不保证 count++ 这种复合操作）</mark>

<mark>data class 不可变对象，多线程读取安全</mark>

**val 与 const val 的对比**

const val 是编译期常量（），必须是顶层/object 里的基本类型或 String

val 是运行时赋值的只读引用。

const val 会内联到调用处，没有字段、没有 getter，当然也不存在运行时共享可变状态的问题。普通 val 仍然是一个字段（或自定义 getter）。

* 编译期常量：写代码的时候就已经能确定是多少，而且以后用于不会变的数。
  
  编译期一看源码就知道答案，不必等程序跑起来再算。它会把这个值直接填进每一个用到的地方，运行时往往连这个变量都不再去读。
  
  * 编译器常量：打开文件就知道是 30。
  
  * 运行时的值：要等程序跑、用户输入、读表、看时钟才知道。

const val 相当于 java 的 static final。

val 相当于 java 的 final。

安全发布指的是：一个对象构造完成后，把它交给别的线程时，对方一定能看到已经初始化完的最终状态，而不是半成品。

<mark>Java 和 kotlin 编译流程的区别</mark>

<mark>Android 编译过程</mark>

<mark>讲解一下 object 和 companion object</mark>

**kotlin 编译流程**

- **词法分析**：切成 token，编译器从左到右扫描源码，切成最小有意义的碎片。
  
  负责认出关键字、标识符、数字、括号。拼写错误（比如 fun 写成 fuc），往往在这一部分就开始报错。

- **语法分析**：建成语法树。
  
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

- **语义分析**（FIR）：真正【看懂】程序
  K2 前端把语法树变成 FIR（Frontend IR）。这是编译器最关键的一步，前面说的【编译器知道 TIMEOUT 是 30】就发生在这里。
  编译器会一次做这些事：
  
  - 名字解析：println 到底是 kotlin.io.println 还是自己写的？System 是 Java 的哪个类？
  
  - 类型推断：val now = System.currentTimeMillis() 没写类型，编译器推断出 Long。
  
  - 常量折叠/编译器常量：检查 TIMEOUT 是否满足 const val 规则：顶层或 object 里、基本类型或 String、右边是字面量。通过后，被记成编译器常量。后面读 TIMEOUT 的地方，都可以直接当 30 用。
  
  - 空安全、只能转换、可见性：String？能不能直接调用、private 能不能从外面访问，都在这里查。
  
  - 诊断：类型不匹配、val 被重新赋值、const val 用 System.currentTimeMillis()，都在这里报红。

- **FIR2IR***：换成后端能用的中间代码
  FIR 很适合做类型检查，不太适合生成目标代码。
  可以把它想成【还带着 Kotlin 语义的伪代码】：
  data class 的 equals/copy 在这生成
  object 的单例结构在这定下来
  跨平台的 expect/actual 在这对齐
  从这里开始，前端的【这是 kotlin】逐渐变成后段的【这时即将变成字节码的指令】。
  Jetpack Compose、kotlinx.serialization 这类编译器插件，主要也是在 IR 上改这棵树，而不是直接改你的 .kt 文件。

- 后端降级：把 Kotlin 特有语法拆成更简单的操作
  JVM 并不理解 inline fun、协程、when、默认参数。这一步就是把高级语法拆成 JVM 能表达的东西。
  
  - const val 内联到调用处：常量值被嵌进使用它的地方。
  
  - inline fun 内联：调用处直接贴上函数体。
  
  - 默认参数：合成重载方法。
  
  - object：带 INSTANCE 静态字段的类。
  
  - lambda：invokednamic 或匿名类
  
  - 协程 suspend：状态机类（Continuation）
  
  - 空安全!!：运行时空检查调用

这一步结束，IR 已经接近【就是字节码的树形版】。

- 代码生成：写出 .class
  后端遍历 IR，写出 JVM 字节码。同时还会写入 Kotlin Metadata：这时塞在 class 文件里的 kotlin 专属信息（哪些是 internal、哪些是suspend、属性是 val 还是 var）。JVM 自己不执行它，但编译器、反射、IDE 需要它。Java 互操作靠的也是这些标注。
  产物是标准的 .class，和 javac 产出的是同一种格式。所以 kotiln 和 Java 能互相调用。

- 打包：把 class 和依赖放在一起
  运行时还要把这些凑成 classpath：
  自己的 class
  kotlin-stdlib(listOf、println、空检查、集合等)
  用到的其他库
  Android: .class -> D8/R8 转成 .dex -> 打进 APK/AAB
  到这里，编译期结束。磁盘上是已经内联好的字节码。

- JVM 启动：加载、链接、初始化
  
  - 加载（Loading）：ClassLoader 按名字找到 Hellokt.class，读进内存，变成 JVM 内部的类表示。
  
  - 链接（Linking）分三小步：
    
    - 验证：字节码是否合法、会不会破坏类型安全。
    
    - 准备：给静态字段分配默认值（0、null）
    
    - 解析：把自负引用变成直接引用，例如 System.currentTimeMillis 到底指向哪个方法。
  
  - 初始化（Initilization）
     执行类的 ，也就是静态初始化。

普通顶层 val timeout = 30 往往在这里真正赋成 30.

- 执行 main：真正的运行时
  开始真正的运行代码，kotlin-stdlib 也在这时被加载；空安全失败抛出的 NullPointerException，也是编译器插入的运行时检查。

## Kotlin 的空安全机制，null 到底是怎么被管理的？非空断言什么时候用？

**null 到底是怎么被管理的？**

在 kotlin 中，每个类型都有一个明确的空状态：非空类型和可空类型。Kotlin 的空安全首先是编译期的类型检查：用 T/T? 规定哪里允许 null，不合规的代码编不过。非法的 null 用法在编译期拦住。

Kotlin 在类型系统层面区分可空和非空类型，编译器在编译期强制检查，NPE 只会在特定场景出现（显式抛、unsafe 操作、Java 互调）。

**非空断言什么时候用？**

能证明有值，为空就崩就使用非空断言。

可以用：

* 编译器看不出，但刚保证过非空 
  
  ```kotlin
  map["id"] = "42"
  val id = map["id"]!!
  ```

* Java/系统 API 标成可空，契约却保证有值
  
  ```kotlin
  val view = findViewById<TextView>(R.id.title)!!
  ```

* 测试代码
  
  为 null 就让测试失败，可以接受。

* 非法状态必须立刻炸

最好是使用 requireNotNull 和 checkNotNull。

* requireNotNull：检查值是否为空，为空则抛出 IllegalArgumentException。

* checkNotNull：检查值是否为空，为空则抛出 IllegalStateException。

* require / check 检查的是布尔条件，requireNotNull/checkNotNull 检查值是否为空。

* requireNotNull 适合检查函数入参/参数是否为空，checkNotNull 检查字段、缓存、流程到这步该有的值。

**Java 与 Kotlin 交互**

* 在 Java 与 kotlin 交互的时候，基本类型（byte、short、int、long、float、double、char、boolean）会转换成非空类型，而包装类型（Byte、Short、Integer、Long、Float、Double、Character、Boolean）、String和自定义类型会是平台类型（Platform Type）（String!，kotlin 无法分别是 String 还是 String?），需要手动处理(根据信息转换为 String 或者 String)。如果 Java 代码添加了 @Nullable 和 @NotNull，Kotlin 就能正确识别类型。

* Kotlin 编成 JVM 字节码时，可空类型会带上 @Nullable，非可空类型带上 @NotNull，方便 Java 侧的 IDE 和编译检查。

## data class 除了自动生成 equals/hashCode，还能干点啥？

data class 会自动生成 equals、hashCode、toString、copy、componentN 函数。

* hashCode()、equals() 是用来比较对象内容是否相同，多用于 HashMap 等容器中。

* toString：是用来打印对象内容。

* copy：实现了复制功能，浅拷贝所有属性，原理：在 copy 方法里面 new 一个新实例，浅拷贝所有属性。

* componentN() 提供了快速访问元素的功能。

data class 适用于存储和传递数据，而非承载复杂业务逻辑。

限制：不能继承其他类（为了保证自动生成的方法行为一致且不会被父类干扰），只能实现接口，也不能是 abstract、open 或 inner，这样设计保证数据类主要专注于存储和传递数据，而不承担复杂继承或业务逻辑，从而保持简洁和安全。

data class 中的普通属性不参与到 equals()、hashCode()、toString、componentN、copy 中，只有主构造函数中的属性参与，而且要求主构造函数中定义至少一个 val 或 var 属性，否则编译器报错。

* ViewModel 状态使用 data class + copy 轻松实现不可变更新（数据对象创建后不改内部，变化用 copy 得到新对象，copy 之所以是精髓，是因为它让不改旧的，只产新的写起来很轻）。

* Rom 数据库 Entity(表示一张表，属性对应列)使用 data class，自动实现 toString，方便日志。测试、去重、和内存里另一份对比时，常靠 equals 方法。update 变更数据时可以使用 copy 方法。

* Retrofit DTO(Data Transfer Object，专门对接接口的数据盒子，字段名、解构跟着 JSON/后段走，常加 Gson/Moshi 的 @SerializaedName) 转 Entity(对象)：意思是接口返回的 JSON 先收成一套【网络用的对象】，再翻译成数据库（或业务）用的另一套对象。这里的 DTO 和 Entity 都是使用的 data class。

与 Java 对比：Kotlin data class 用 copy 换新对象，旧的不变，新的也还是不可变，而 Java 中的 Bean 是可变的，通过对象的修改方式 (如 setXXX()） 直接改变对象的内容，到处 setXXX 改同一个对象。

## == 和 === 到底比的是什么？

Kotlin 中 == 比较的是数值是否相等，而 === 比较的是两个对象的地址是否相等。

基本类型：

- 非空 Int：是值，没有对象，没有地址，=== 只能比大小。
- 可空 Int？：是对象，有地址，=== 比是不是同一个实例；
- Integer、Long 等包装类型会有缓存，-128 到 127 范围内的值 == 和 === 结果相同

字符串：JVM 有字符串常量值，会缓存字符串字面量，intern() 方法会将是把这个字符串换成常量池里的那一份，字面量已经换过了。"abc"==="abc"是 true。

Kotlin 中的对象、String 的 == 会调用 equals() 方法。如果自己写的类没有重写 equals() 方法，继承的是 Any.equals() 方法，那就是比地址，这时 == 和 === 结果一样。如果某个类有比较属性是否相等的需求，需要自行覆盖 equals() 方法。

data class 会生成 equals()，所以 == 比的是主构造属性，不是地址。

kotlin 的 == 和 === 有一定的类型判断能力。

## const val vs val 的底层差异

const val：编译器常量，在编译时确定值，在编译过程中，编译器会直接把属性访问替换为实际的常量值，并将该值写入使用它的字节码指令中。只能修饰 String 或基本类型，不能修饰自定义类或复杂对象，只能在顶层、object 声明或者 companion object 内部定义，不能在普通类的方法局部作用域中声明。

val：运行时只读变量，在运行时确定值。拥有实际的内存储存位置。

const val 的值会在编译期间被直接复制到调用方的字节码中，因此在多模块项目或公共库中，它会带来一个非常大的架构风险。库模块的值是 constant val，app 模块编译时会将值写入它本身的字节码中，如果后面修改值，但没有对 app 进行完整重新编译，那 app 模块会继续使用旧值。

@JvmField：如果希望对于普通的 val 属性或 var 属性，访问时不让 Kotlin 编译器生成 Getter 方法，运行时直接读取数据，但也不会像 const val 那样进行编译器内联，那么可以使用 @JvmField。性能上会少一次 getter，带来一定的性能优化，又避免了 const val 在多模块项目中由于编译期内联而引发的二进制兼容性问题。为了和 Java/老框架的字段约定对齐时，也会使用。

在使用 AOP 和 Gradle Plugin 的时候，在打包阶段只改 class时，改 const val 定义处，一般改不到已经用到它的地方，改 val 才容易一处生效。

必须要用 const val: 注解参数、BuildConfig 字段、Gradle 配置缓存。

- 如果是固定的配置项、全局变量、URL 或数值边界（且类型是基本类型或字符串），优先使用 const val 以获得更好的性能。
- 其他绝大多数只需要只读、不可修改的普通属性、局部变量或对象实例，统一使用 val。

<mark>Gradle Plugin 常见用途：</mark>

- 自动插入埋点

- 把明文 URL / Key 加密

- 换环境地址

- 统计有哪些方法

- 插入权限检查

<mark>AOP</mark>

<mark>注解处理器（KAPT/KSP/APT）</mark>

<mark>ARouter</mark>

## lateinit vs by lazy ：我到底该用哪个？

## kotlin when vs Java switch