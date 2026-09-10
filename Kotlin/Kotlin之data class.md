# Kotlin 之 data class

> Data class 在编译时会自动生成 equals（）、hashCode()、toString()、copy（） 以及一组 componentN() 方法。
> 
> * hashCode（）、equals() 是用来比较对象内容是否相同，多用于 HashMap 等容器中。
> 
> * toString() 是用来打印对象内容。
> 
> * copy() 实现了复制功能，浅拷贝，原理：在 copy 方法里面 new 一个新实例，浅拷贝所有属性。
> 
> * componentN() 提供了快速访问元素的功能。
> 
> data class 的功能 class 都能实现，data class 只是 Kotlin 提供的具有常用数据 model 功能的类，用来提升开发效率。
> 
> 非常适合用作数据传输对象、API 响应模型或配置实体等场景。
> 
> data class 必须在主构造函数中定义至少一个 val 或 var 属性，否则编译器会报错。
> 
> data class 不能继承其他类，只能实现接口，这是为了保证自动生成的方法行为一致且不会被父类干扰。
> 
> data class 也不能是 abstract、open、sealed 或 inner。这些限制确保数据类的结构固定、行为可预测。
> 
> 在 Kotlin 1.5+，数据类可以结合 sealed 使用，实现有限集合的数据子类。
> 
> 这样设计保证数据类主要专注于存储和传递数据，而不承担复杂继承或业务逻辑，从而保持简洁和安全。
> 
> inner：内部类，Kotlin 里默认写在类里面的类拿不到外层的 this。加上 inner 才是内部类，每个实例都绑着一个外层对象。（inner 持有外层引用，注意内存泄漏，能不用就不用）。
> 
> data class 中的普通变量和普通 class 的普通变量是相同的含义：不参与到 equals、hashCode、copy、toString、componentN 中。
> 
> 在 ViewModel 中使用 Kotlin 的 data class 来定义 UI 状态（UI State）是现代 Android 开发（特别是 Jetpack Compose）中的标准最佳实践。
> 
> 页面手机 state，每次 _state.value = ...copy(...) 都是新的 state，Compose/订阅方发现对象换了，按新数据刷新。
> 
> ViewModel 用一个只读的 data class 表示当前界面；每次变化都 copy 出新状态再发出去，而不是改旧对象上的字段。这样更新可追踪，Compose/StateFlow 也才能可靠刷新。（copy() 方法中对象属性值没有变化的话，equals 会是 true，那就不会触发更新，所以需要注意 list、map、array、StringBuilder、Date、Calendar、有 var、MutableMap、HashMap、MutableSet、MutableList、嵌套对象包含这些类型的属性，如果有这些，copy 之后，equals 方法返回 true，也不会触发刷新。）
> 
> data class 为什么适合，因为它会生成：
> 
> - copy：只改一部分字段，做出新状态（精髓，copy 之所以是精髓，是因为它让不改旧的，只产新的写起来很轻）
> - equals：StateFlow/Compose 用来判断【要不要刷新】
> - toString：打日志能看到整份状态
> 
> 日常开发使用 data class 的地方：
> 
> - ViewModel 状态使用 data class + copy 轻松实现不可变更新（数据对象创建后不改内部，变化用 copy 得到新对象，copy 之所以是精髓，是因为它让不改旧的，只产新的写起来很轻）。
> - Rom 数据库 Entity(表示一张表，属性对应列)使用 data class，自动实现 toString，方便日志。测试、去重、和内存里另一份对比时，常靠 equals 方法。update 变更数据时可以使用 copy 方法。
> - Retrofit DTO(Data Transfer Object，专门对接接口的数据盒子，字段名、解构跟着 JSON/后段走，常加 Gson/Moshi 的 @SerializaedName) 转 Entity(对象)：意思是接口返回的 JSON 先收成一套【网络用的对象】，再翻译成数据库（或业务）用的另一套对象。这里的 DTO 和 Entity 都是使用的 data class。
> 
> 数据类通过自动生成常用方法，大幅减少了样板代码，让开发者可以专注于数据本身。它适用于存储和传递数据，而非承载复杂业务逻辑。借助 copy()、解构声明以及自动生成的 equals（）、hashCode()、toString()，数据类让对象的比较、复制、打印和解构操作变得直观而高效。
> 
> 合理使用数据类，保持构造函数简洁、属性直观且尽量不可变，不仅可以减少重复代码，还能提升可读性和维护性，使 Kotlin 开发体验更加简洁优雅。

## 前言

在日常开发中，常常需要创建一些只用来保存数据的类。问题是，这样的类往往需要写一堆模版化的方法：equals()、hashCode()、toString()... 每次都重复，既枯燥又容易出错。

```kotlin
// 手动实现所有方法
class User(val id: Int, val name: String) {
    override fun equals() = ...
    override fun hashCode() = ...
    // ... 其他模版代码
}
```

这个类的核心价值只是存储和传递数据，其他方法只是为了使其在集合中比较、打印输出或做解构时表现正常。样板代码占了大半，却和业务逻辑几乎无关。

Kotlin 给出的解决方案是 Data Class(数据类)。它让这些常用方法自动生成，开发者只需关注数据本身，用最少的代码得到一个功能完整的类，从此告别手写样板的时代。

## 什么是 Data Class

Data Class 是 Kotlin 提供的一种特殊类，专门用于承载数据。与普通类相比，它在编译时会自动生成 equals()、hashCode()、toString()、copy() 以及一组 componentN() 方法，让一个最小定义就能拥有完整的基础功能。

hashCode() 、equals() 是用来比较对象内容是否相同，多用于 HashMap 等容器中。

toString() 是用来打印对象内容。

copy() 实现了复制功能。

componentN() 提供了快速访问元素的功能。

data class 的功能 class 都能实现，data class 只是 Kotlin 提供的具有常用数据 model 功能的类，用来提升开发效率。

要声明数据类，只需在类名前加上 data 关键字，并在主构造函数中定义一个 val 或 var 属性：

```kotlin
data class User(val id:Int, val name:String)
```

这样的类无需额外编写任何样板方法，就能直接比较内容、打印可读信息、进行解构赋值，甚至快速复制并修改部分属性。它非常适合用作数据传输对象（DTO）、API 响应模型或配置实体等场景。

## 自动生成的成员

数据类在编译时会为主构造函数中的属性自动生成多种常用方法，省去重复编写的麻烦：

* equals() 与 hashCode()：按属性值比较对象是否相等，并生成对应的哈希值，可直接作为集合的键使用。

* toString() ：生成包含类名和属性值的字符串表示，便于调试与日志输出。
  
  ```kotlin
  println(player.toString()) // 普通 class
  // PlayerClass@27c170f0
  
  println(dataClassPlayer.toString()) // data class
  // PlayerDataClass(name=Alan, age=18)
  ```

* componentN() 系列方法：每个属性对应一个方法，支持解构声明：
  
  ```kotlin
  val user = User(1, "Alice")
  val (id, name) = user // id = 1, name = "Alice"
  ```

* copy() 方法：
  
  ```kotlin
  val updated = user.copy(name = "Bob")
  ```

这些成员都基于主构造函数中的 val 或 var 属性生成，且会按声明顺序对应 component1()、component2() 等方法。这样，一个简短的定义就具备了完整的比较、复制和解构能力。

这种自动生成的字符串表示会完整暴露所有属性值。当数据类包含敏感信息时，这可能会带来安全风险。在实际开发中，对于包含密码、密钥或其他敏感数据的类，可以重写 toString() 方法来过滤敏感字段。当然，也可以借助工具来达到更高级别的保护，如使用 Virbox Protector 加固工具的字符串加密功能，它能够在运行时动态保护字符串内容，防止通过内存 dump 或反编译获取敏感信息，同时保持与 Kotlin/JVM 项目的良好兼容性。

**Virbox Protector**

Virbox Protector 是深盾（原深思数盾）的一套商业加固/加壳工具，用来在软件发出去之前，把可执行文件、APK、so、.NET、Unity 等保护起来，提高反编译、调试、篡改的成本。

加固在这里不是变快、也不是修 bug，而是：发布前再包一层保护，让别人更难把逻辑和资源拆出来。

它在构建链上的位置：

```
写代码 -> 编译出 .class / so / exe / apk
            ↓
         Virbox Protector 加固    ← 发布前一步
            ↓
         再签名、打包、上架
```

发生在编译之后、交付之前，属于发布侧的安全处理，不是 kotlinc/JVM 的一部分。

**内存 dump 或反编译**

这里的 内存 dump 指的是：程序已经跑起来之后，有人把进程当时在内存里的内容拷出来看，从中找到密码、密钥这类字符串。

反编译：看磁盘上的 .class / DEX -> 源码里写死的 “mypassword” 能被搜到

内存 dump：看进程正在用的 RAM -> 运行时已经出现的铭文密钥可能被翻到

字符串加密（Virbox 那类）想补的是：

* 磁盘上：密钥不是命嗯 “abc123”，而是密文。

* 运行时：用到再解开；文案说【动态保护】，意思是尽量少以明文长时间躺在内存里。

这样反编译搜不到铭文，dump 时也不那么容易整段看到现成的密钥。只能提高成本，不能保证内存里永远没有明文 - 用的那一瞬间往往还是要解密的。

重新 toString() 将敏感信息加密打印。

**RAM**

Random Access Memory，内存，电脑/手机里程序正在跑的时候用的那块临时存储。CPU 要算什么、对象里的密码字段、解开的字符串，当时都在 RAM 里。

与硬盘的差别

|     | RAM(内存)      | 硬盘/闪存（ROM、磁盘） |
| --- | ------------ | ------------- |
| 速度  | 很快           | 慢很多           |
| 断电  | 内容没了         | 还在            |
| 放什么 | 正在运行的程序、临时数据 | APK、JAR、照片、系统 |

kotlin-stdlib、你的 .class 平时躺在磁盘上；点开 App，JVM/ART 把要用的东西装进 RAM 再执行。

【随机存取】的意思是：不一定从头读到尾，任意地址都能很快读写。

ART：Android Runtime，Android 运行时。手机上真正执行 App 的那一层，角色类似电脑上的 JVM，但跑的是 Android 的 dex 字节码，不是桌面那种 .class 直接交给 HotSpot（最常用的 Java 虚拟机实现，靠给热点代码做 JIT 而得名，JVM 是规格，HotSpot 湿气重一台真正再跑的引擎）。Android 上的运行时，负责把 App 的 dex 真正跑起来。相当于手机版的虚拟机，对应电脑上的 JVM。

## 构造函数与属性要求

数据类必须在主构造函数中定义至少一个 val 或 var 属性，否则编译器会报错。这是因为自动生成的方法依赖这些属性来比较、复制和解构对象。

```kotlin
data class Point(val x: Int, val y: Int) //合法
data class Empty() // 编译错误
```

属性的可见性会影响生成结果：

* private  属性不会参与 equals()、hashCode()、toString() 等方法的生成。

* var 属性在对象创建后可修改，val 则是只读。

构造函数的参数还可以设置默认值，以减少构造对象时的重复输入：

```kotlin
data class User(val id: Int, val name: String = "Unknown")
val u = User(1) // name 自动为 "Unknown"
```

这种设计让数据类在保持简洁的同时，依然具备灵活性和安全性。

## 解构声明（Destructuring Declarations）

数据类会为主构造函数中的每个属性生成对应的 componentN() 方法，从而支持解构声明。通过这种语法，可以将对象的属性一次性拆分成多个独立变量。

```kotlin
data class User(val id: Int, val name: String)


val user = User(1, "Alice")
val (id, name) = user
// id = 1, name = "Alice"
```

变量的顺序与属性在主构造函数中的声明顺序一致，如果只需要前几个属性，可以使用下划线 _ 跳过：

```kotlin
val (_, username) = user // 只取 name
```

解构声明在集合操作中也很常用，例如配合 map、forEach 等函数处理数据：

```kotlin
val users = listOf(User(1, "A"),User(2, "B"))
users.forEach{ (id, name) -> 
    println("$id -> $name")
}
```

这种语法让数据类在批量处理数据时更简洁，避免了频繁的属性访问写法。

**解构**

解构就是：把一个对象里的几份数据拆开，分别赋给几个变量，不用一个个写 .id，.name。

```kotlin
val (id, name) = user
```

为什么 data class 能拆？编译器生成了 component1()、component2().....

`val (id, name) = user` 其实是：

```kotlin
val id = user.component1() // 主构造期第一个属性
val name = user.Component2()   // 第二个
```

顺序跟主构造期参数顺序一致，不是按名字对上的。

其他地方也会使用

```kotlin
val (k,v) = "id" to 42 // Pair
for ((key, value) in map) { } // Map 的每一项
val (a, b, c) = listOf(1, 2, 3) // 列表（有 componentN 时）
```

不需要的位置可以用 _ 丢掉：

```kotlin
val (_, name) = User(1, "Alice") // 只要 name
```

## copy() 方法详解

copy() 是数据类自带的浅拷贝方法，用于基于现有对象快速创建一个新对象，同时可修改部分属性。

```kotlin
data class User(val id:Int, val name: String)

val original = User(1, "Alice")
val updated = original.copy(name = "Bob")
// updated = User(id=1, name=Bob)
```

复制时，未显式传入的参数会沿用原对象的属性值，只修改传入的部分。

需要注意几点：

* 这是浅拷贝，引用类型属性不会递归复制，如果内部对象可变，两个实例会共享同一个引用。

* 复制后返回的是一个全新的对象，equals() 比较结果可能相等，但不是同一个引用。

* 可与解构声明配合，先拆出值再用 copy() 创建新对象。

```kotlin
data class Profile(val id: Int, val tags: MutableList<String>)


val p1 = Profile(1, mutableListOf("dev"))
val p2 = p1.copy()
p2.tags.add("kotlin")
// p1.tags 也会被修改，因为 tags 是同一个列表引用
```

在需要深拷贝时，需要手动处理内部可变对象，例如在 copy() 参数中显式创建新的集合或对象。

```kotlin
// 一般 class
class PlayerClass(val name: String, var age: Int) {

    var point: Int = 0

    constructor(name: String, age: Int, point: Int) : this(name, age) {
        this.point = point
    }
}

// data class
data class PlayerDataClass(val name: String, var age: Int) {

    var point: Int = 0

    constructor(name: String, age: Int, point: Int) : this(name, age) {
        this.point = point
    }
}


val player1 = PlayerClass("Peter", 24)
val player2 = PlayerClass("Peter", 24)


println(player1.equals(player2))
// false

val dataclassPlayer1 = PlayerDataClass("Peter", 24)
val dataclassPlayer2 = PlayerDataClass("Peter", 24)

println(dataclassPlayer1.equals(dataclassPlayer2))
// true
```

类型为 PlayerClass 的 player1 和 player2 使用相同的参数初始化，两者调用 equals() 的结果为 false。

类型为 DataPlayerClass 的 dataClassPlayer1 与 dataClassPlayer2 使用相同的参数初始化，两者调用 equals() 的结果为 true。

判断两个对象是否相同，涉及到 hashCode 和 equals。

在Java 应用程序执行期间，对一个物体呼叫 hashCode() 方法应该都传回同一个值。

如果两个物体调用 equals() 得到相同的结果，那这两个物件呼叫 hashCode() 的结果应该也要一样。

如果两个物件调用 equals() 得到不同的结果，那这两个物件呼叫 hashCode() 的结果不一定要不一样。

可以看到一般的 class 本身就设计为一个现存的物件的 hashCode 和新创建出来的不一样，所以就算类型属性相同，呼叫 hashCode() 时还是传回了不同数值。而在 Kotlin 的 data class 中，data class 按照了类型中的属性自行去 override 和 hashCode，让属性相同的类型可以传回相同的 hashCode，符合了 equals() 方法中要求的条件。

每一个一般的 class 在 Java 中，预设都 extend 了 object 类别。也就是说，Java 中的每个类别，在未 override equals() 方法的前提下，都是呼叫 Object 的 equals() 方法，其代码如下：

```kotlin
public boolean equals(Object obj) {
    return (this == obj)
}
```

可以看到在 Java 中预设是如何去判断两个物体是否相等，即使用 == 来判断两个物件 reference 的记忆体，是不是在同一个地址，也可以归纳为，一般的 class 在比较的对象为：记忆体位置。

Kotlin 中的 data class 比较的对象为何呢？它比较的是，是该体型中资料本身。一个 data class 中会有多个属性，可以看成是这个类别中夹带的资料；使用 equals() 来比较两个 data class 的实例时，它比较的并不是记忆体位置，而是在比较两个实例中夹带的资料一样不一样，所以就不难看出范例中属性一样的类型会被判断为是相等的了。

**kotlin 中的 Equality**

Java 中的 Equality 分为两种，分别是：

* Structural Equality（结构性的，和 equals() 有关）

* Referential Equality（参开性的，和记忆体位置有关）

在 Kotlin 中也是一样，也分为上述两种，只是符号有些许差异：

Java:

* 判断是否为 Structural Equality，使用 equals()

* 判断是否为 Referential Equality，使用 ==

Kotlin：

* 判断是否为 Structural Equality，使用 equals() 或 ==

* 判断是否为 Referential Equality，使用 ===

因为 data class override 了 equals()，将预设判断是否为 Referential Equality 更改为判断是否为 Structural Equality，但只要使用 === 进行判断，就可以知道其实两个 data class 实例的记忆体位置还是不一样的：

```kotlin
println(dataClassPlayer1 === dataClassPlayer2)
// false
```

### copy 原理

copy 不是运行时反射去克隆对象。编译器在编 data class 时生成一个普通函数：参数带默认值，函数体就是再 User(...) 一次。

```kotlin
data class User(val id: Int, val name: String)
```

生成的 copy 方法大约是：

```kotlin
fun copy(id: Int = this.id, name: String = this.name): User {
    return User(id, name)
}
```

所以：

```kotlin
val u1 = User(1, "Alice")
val u2 = u1.copy(name = "Bob")
```

等于:

```kotlin
val u2 = User(id = u1.id, name = "Bob")
```

没写的参数用当前对象上的值；写了的用传入的新值。然后 new 一个新实例，就对象不动。

copy 原理 = 编译器生成的，带默认参数的工厂函数，内部 return 类名（新或旧字段）。所以是浅的、只含主构造期属性、就对象不变。

## Data class 的继承与限制

数据类在继承上有一定限制。它不能继承其他类，只能实现接口，这是为了保证自动生成的方法行为一致且不会被父类干扰。

```kotlin
interface Identifiable {
    val id: Int
}

data class User(override val id: Int, val name: String) : Identifiable
```

此外，数据类也不能是 abstract、open、sealed（早期 Kotlin 版本）或 inner。这些限制确保数据类的结构固定、行为可预测。

在 Kotlin 1.5+，数据类可以结合 sealed 使用，实现有限集合的数据子类：

```kotlin
sealed class Result
data class Success(val data:String): Result
data class Error(val code:Int):Result
```

这些设计保证数据类主要专注于存储和传递数据，而不承担复杂继承或业务逻辑，从而保持简洁和安全。

**sealed**

sealed（密封）表示：这个类型的子类只能是你指定的那几个，不能在外面随便再继承。

编译器因此知道【所有可能的子类型】是一份完整名单，when 可以写全、不必再写 else。

kotlin 里怎么用：

```kotlin
sealed class Result {
    data class Ok(val data: String) : Result()
    data class Err(val msg: String) : Result()
}


fun handle(r: Result): String = when(r) {
    is Result.OK -> r.data
    is Result.Err -> r.msg
    // 两种都写了，不用 else
}
```

若再加一种 Loading，这里的 when 会编不过，逼你补上。这就是密封的价值：分支穷尽，漏了编译器会说。

sealed interface 一样，只是用接口而不是类。

和普通继承的差别

|      | 普通 open class | sealed class          |
| ---- | ------------- | --------------------- |
| 谁能继承 | 任何地方（可见就行）    | 同一模块、规定好的那几个          |
| when | 往往要 else      | 写全子类型可以不写 else        |
| 典型用途 | 框架扩展点         | 有限几种结果：成功/失败、几种 UI 状态 |

子类一般写在同一个文件（或同一个模块）。外面再写 class Hacker: Result() 不行。

和几个词别混

* final / 默认 class：完全不能继承

* sealed：能继承，但子类名单封闭

* enum class：也是有限几种，但每种通常是单例，不好各自带不同字段

* data class：生成 equals / copy 等；可以同时是某个 sealed 的子类。

**inner**

Kotlin 里默认写在类里面的类拿不到外层的 this。加上 inner 才是【内部类】：每个实例都绑着一个外层对象。

* 嵌套类（不是 inner）

```kotlin
class Outer {
    private val name = "Alice"

    class Nested {
        fun foo() {
            // name       // 编译错误：没有外层对象
        }
    }
}


val n = Outer.Nested() // 不用先有 Outer
```

相当于 Java 的 static 嵌套类：只是把类写在一起，两套实例互不绑定。

* inner：内部类，持有外层引用

```kotlin
class Outer {
    private val name = "Alice"

    inner class Inner {
        fun foo() = name          // OK：能用外层的 name
    }
}

val o = Outer()
val i = o.Inner()                 // 必须先有一个 Outer
```

Inner 的每个实例都藏着对某个 Outer 的引用。所以能用外层的 val / var / this@Outer。

* 和 Java 正好反着

|         | Kotlin         | Java            |
| ------- | -------------- | --------------- |
| 类中的类，默认 | 嵌套类，不持有外层      | 内部类，持有外层        |
| 要反过来时   | 写成 inner class | 写成 static class |

Kotlin 默认更【干净】，避免无意中把外层对象拖住、造成泄漏。需要外层状态时再显式写 inner。

* 什么时候用 inner

需要跟着某一个外层实例走，例如：

```kotlin
class Adapter(val items: List<String>) {
    inner class ViewHolder {
        fun bind(position: Int) {
            println(items[position]) // 用外层
        }
    }
}
```

ViewHolder 离开这个 Adapter 就没有意义，用 inner。

只是把工具类放在旁边、不依赖外层实例 -> 不要 inner，用默认嵌套类（或顶层类）。

* 注意泄漏

inner 持有外层引用。若内部类活的比外层长（丢进静态缓存、延迟回调），外层对象也回收不了。

Android 里 Activity 的非静态内部类 / Kotlin inner 就是经典泄漏点。能不用 inner 就不用。

## 普通变量和元素变量

```kotlin
data class VisibilityChangeRecord(val position: Int) {
    var id = 0L // 普通变量
}
```

data class 中的普通变量和普通 class 的普通变量是相同的含义：不参与到 equals、hashCode、copy、toString、componentN 中。

## ViewModel 状态用 data class

在 ViewModel 中使用 Kotlin 的 data class 来定义 UI 状态（UI State）是现代 Android 开发（特别是 Jetpack Compose）中的标准最佳实践。

界面收集 state。每次 \_state.value = ...copy(...) 都是新的 state，Compose/订阅方发现对象换了，按新数据更新。

ViewModel 用一个只读的 data class 表示当前界面；每次变化都 copy 出新状态再发出去，而不是改旧对象上的字段。这样更新可追踪，Compose/StateFlow 也才能可靠刷新。（copy() 方法中对象属性值没有变化的话，equals 会是 true，那就不会触发更新，所以需要注意 list、map、array、StringBuilder、Date、Calendar、有 var、MutableMap、HashMap、MutableSet、MutableList、嵌套对象包含这些类型的属性，如果有这些，copy 之后，equals 方法返回 true，也不会触发刷新。）

* 为什么使用 Data Class
  
  * 单一数据源：将页面的所有状态聚合到一个 data class 中，UI 只需要观察这一个状态对象，避免多个 StateFlow 或 LiveData 带来的状态不一致问题。
  
  * 不可变性（Immutability）：结合 copy() 方法，每次状态更新都产生一个新对象，确保单向数据流（UDF）清晰可预测。
  
  * 结构清晰：一眼就能看到当前界面包含的所有数据和字段。

**进程异常杀死恢复**：如果需要配合 SaveStateHandle 在系统回收进程后恢复复杂状态，可以结合 Kotlinx Serialization 将 @Serializable 的 data class 存入其中。

**状态合并**：复杂页面可将多个子状态分类组合进主 data class 中，保持层次清晰。

data class 为什么适合，因为它会生成：

* copy：只改一部分字段，做出新状态（精髓）（copy 之所以是精髓，是因为它让不改旧的，只产新的写起来很轻）

* equals：StateFlow/Compose 用来判断【要不要刷新】

* toString：打日志能看到整份状态

ViewModel 的状态 data class 尽量满足：

1. 主构造函数属性全是 val

2. 只用不可变类型：String、Int、Boolean、List、Map、另一个不可变 data class、sealed

3. 更新只通过 copy + 换新集合/新嵌套对象

4. 不放 View、Context、lambda、可变 Java 对象

5. 用 \_state.update { it.copy(...) } 改 StateFlow

凡是可变集合、数组、Date、带 var 的嵌套对象，都有浅拷贝+不刷新的问题。

## 日常开发使用 data class 的地方

- ViewModel 状态使用 data class + copy 轻松实现不可变更新（数据对象创建后不改内部，变化用 copy 得到新对象，copy 之所以是精髓，是因为它让不改旧的，只产新的写起来很轻）。
- Rom 数据库 Entity(表示一张表，属性对应列)使用 data class，自动实现 toString，方便日志。测试、去重、和内存里另一份对比时，常靠 equals 方法。update 变更数据时可以使用 copy 方法。
- Retrofit DTO(Data Transfer Object，专门对接接口的数据盒子，字段名、解构跟着 JSON/后段走，常加 Gson/Moshi 的 @SerializaedName) 转 Entity(对象)：意思是接口返回的 JSON 先收成一套【网络用的对象】，再翻译成数据库（或业务）用的另一套对象。这里的 DTO 和 Entity 都是使用的 data class。

## 总结

数据类通过自动生成常用方法，大幅减少了样板代码，让开发者可以专注于数据本身。它适用于存储和传递数据，而非承载复杂业务逻辑。借助 copy()、解构声明以及自动生成的 equals()、hashCode()、toString()，数据类让对象的比较、复制、打印和解构操作变得直观而高效。

合理使用数据类，保持构造函数简洁、属性直观且尽量不可变，不仅可以减少重复代码，还能提升可读性和维护性，使 Kotlin 开发体验更加简洁优雅。

## 参考内容

* [Kotlin Data Classes 快速上手](https://zhuanlan.zhihu.com/p/1939373636151644361)

* [Kotlin使用心得（六）：data class](https://carterchen247.medium.com/kotlin%E4%BD%BF%E7%94%A8%E5%BF%83%E5%BE%97-%E5%85%AD-data-class-1689b1782abb)

* [kotlin data class和普通class区别](https://blog.csdn.net/ZHXLXH/article/details/121029633)
