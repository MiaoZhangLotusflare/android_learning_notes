# Kotlin inline 与 reified

> Lambda 用起来很轻巧，但是每个 Lambda 都会编译成一个函数对象，高频调用时这些对象会带来实实在在的开销。inline 让编译器把函数体直接复制到调用处，从而消除函数对象和调用开销。
> 
> inline: 编译时将函数体直接插入到调用处，消除了函数调用的栈帧创建、参数传递等开销，对Lambda 表达式进行深度优化，避免匿名类对象的创建。
> 
> inline 常见收益有三个：
> 
> - 减少函数对象和间接调用带来的开销
> - 允许 Lambda 使用非局部 return
> - 配合 reified 在运行时使用泛型类型
> 
> 无捕获 Lambda 可能被编译成单例。
> 
> 适合内联的函数通常有这些特征：
> 
> - 接收一个或多个 Lambda
> - 函数体比较短
> - 调用频率较高
> - Lambda 通常立即执行，不需要保存
> - 需要非布局返回
> - 需要 reified 泛型
> 
> 不适合随便内联的函数：
> 
> - 函数体很大
> - 调用点很多
> - 没有 Lambda，也不需要 reified
> - 很少调用，性能收益没有实际意义
> - 递归函数
> 
> 标准库里的 forEach、map、filter、let、run、apply、also、repeat 都是内联函数，这也是它们能支持非局部返回的原因。
> 
> 内联函数最直观的好处是：Lambda 里的 return 可以从外层函数直接返回，这叫非局部返回（no-local return）。
> 
> 非局部返回只在 Lambda 直接作为参数传给内联函数时有效。如果 Lambda 先被存进变量、或者被传给一个普通函数，它将失去了内联上下文，不能再跳出外层函数。
> 
> 实践使用：很多项目都会重复写日志、耗时统计和异常处理，可以把固定流程放进一个内联函数。
> 
> 这种小型流程函数很适合 inline：固定逻辑不复杂，Lambda 在函数内立即执行，也不需要保存到其他地方。
> 
> noinline：内联函数的函数类型参数（Lambda 参数）默认可以被内联，如果 Lambda 参数需要被保存到变量、作为参数传递给非内联函数，就要用 noinline 关掉它的内联。
> 
> 加了 noinline 的函数里面不允许非局部返回。
> 
> noinline 函数具有普通函数值的能力：
> 
> - 保存到属性或集合
> - 赋值给变量
> - 作为返回值返回
> - 传给普通高阶函数
> - 稍后或异步执行
> 
> crossinline：Lambda 仍然内联，但它被放进了另一个执行上下文，比如另一个 Lambda 或对象表达式里，此时需要用 crossinline，它暴露内联，同时禁止在这个 Lambda 里使用非局部返回。
> 
> inline：展开函数体和 Lambda，支持非局部返回
> 
> noinline：保留普通函数对象，允许保存、传递和稍后执行，禁止非局部返回。
> 
> crossinline ：可以跨层调用，保持可内联，但禁止非局部返回。
> 
> reified：在函数内部可以访问类型参数的具体类型。通常情况下，由于类型擦除（type erasure），在运行时是无法直接获取泛型类型参数的具体类型的。reified 关键字解决了这个问题。
> 
> 只能用在 inline 函数上，因为只有内联才能在每个调用点看到具体类型。
> 
> 如果 reified 的是 List，无法靠 T::class 区分 List<String> 和 List<Int>。
> 
> 当调用一个带有 reified 泛型参数的 inline 函数时，编译器会在调用处为每个具体的泛型参数类型生成一份独立的字节码。这有点像函数重载，但实际上是编译器在编译时将 inline 函数的代码嵌入到调用点，并替换掉泛型参数为其实际类型。
> 
> 由于代码被内联并替换了具体的类型，所以在运行时，这些具体的类型信息就得以保留。
> 
> reified 关键字必须与 inline 关键字联用，这是因为 reified 的核心机制就是通过在编译期将函数体展开（内联）到调用点，才能在字节码中嵌入具体的类型信息。如果不是 inline 函数，它无法进行这种编译期优化，也就无法实现类型实化。
> 
> reified 只能用于非空类型。
> 
> @PublishedApi：public 的内联函数会被复制到调用方模块，如果它内部访问了 internal 或 private 的声明，那些声明的字节码也必须在调用方可见。为了防止代码给公开并干扰 open/closed(OCP)开闭原则，Kotlin 允许将代码内部化，只要在方法或类中应用特殊注解 PublishedApi 即可。
> 
> 尽管 inline 函数可以减少函数调用的开销，但它的副作用是会增加代码体积（DEX 膨胀）。如果大量使用 inline 函数，并且这些函数体较大，可能会导致最终的 APK 或 Jar 文件变大，在 Android 开发中，过大的 DEX 文件可能会影响应用启动速度。
> 
> 并非所有的泛型场景都适合 reified。过度或不恰当地使用 reified 可能导致复杂的泛型逻辑更难以理解，反而降低代码的清晰度。建议将其封装为通用且可重用的工具函数或扩展函数。
> 
> inline 属性：属性的访问器也可以内联。带幕后字段的属性或访问器不能标 inline，否则编译错误。
> 
> 内联函数会影响库的升级方式：如果库里面修改了 public inline 函数的实现，但应用没有重新编译，应用里可能继续运行旧的内联函数。所以公共库里的内联函数需要特别注意。
> 
> Lambda 每次都创建新对象？不一定，无捕获 Lambda （函数体里没有用到外层函数的局部变量、参数，也没有用到外层类的实例）可能复用实例；JVM 也可能优化掉部分分配（即时编译器的逃逸分析有时会把没逃出方法的对象分配消掉，有捕获的 Lambda 也可能收益。这取决于是否内联得动、对象有没有被存下来，不保证发生）。
> 
> reified 保留全部泛型信息？不是，reified 让内联函数能使用具体的 T，嵌套泛型参数仍可能受 JVM 类型擦除影响。
> 
> reified 优势：
> 
> - 简化代码：告别了冗余的 Class<T> 参数传递，让泛型代码更加简化、优雅。
> - 类型安全：在运行时保留了类型信息，从而能够进行安全的泛型类型检查，有效避免了 ClassCastException 运行时错误。
> - 逻辑封装：使得类型解析、集合过滤、网络请求封装等通用泛型逻辑的实现变得更加简单和直观。
> - 与 Kotlin 生态契合：reified 与 kotlin 的其他特性（如协程、扩展函数）无缝结合，进一步提升了开发体验。
> 
> 常用内联函数源码剖析：
> 
> - let 函数：return block(this) 将调用对象 this 作为参数传递给 block 并返回其结果。str?.let{...}
> - run 函数：return block()。view.run{init(); calculate()}
> - with 函数：接收一个对象 receiver 和 Lambda 表达式 block，通过 receiver.block() 以 receiver 为接收者执行 block 并返回结果。with(list){sort(); filter(...);}
> - apply 函数：调用对象为 this 为接收者执行 block（block() return this），最后返回调用对象本身，支持链式调用。Button().apply{text="OK"; onClick={...}}
> - also 函数：调用对象 this 作为参数给 block，执行 block 后返回调用对象本身（block(this) return this），支持链式调用。file.also{log(it)}.delete()
> 
> LiveData 的 observe 方法为何不内联？observe 接收的 Observe 是接口而非 Lambda，无需内联（接口实现本身是对象，内联无法优化）。
> 
> ViewModel 的工厂函数是否需要内联？不需要。ViewModel 工厂（如 ViewModelProvider.Factory）是接口，其 create 方法的实现是类而非 Lambda，内联无优化作用。内联主要针对 Lambda 参数的高阶函数。
> 
> 为什么 Kotlin 的 let 函数要声明为内联函数？不内敛会有什么问题？let 是接收 Lambda 参数的高阶函数，若不声明 inline，Lambda 会被编译为匿名类对象（每次调用创建新实例），增加内存开销。
> 
> 声明 inline 后，Lambda 代码直接嵌入调用处，避免对象创建，同时消除函数调用栈开销，提升高频调用时的性能（例如在集合遍历或 UI 链式配置中）。
> 
> apply 与 also 有什么区别？
> 
> - this 指向不同：apply 中 this 是当前对象，可直接调用成员；also 中 this 是函数参数，需要 it 访问对象。
> - 返回值不同：apply 返回当前对象（用于链式配置），also 也返回当前对象，但更侧重执行副作用（如日志、校验）。
> 
> 内联函数一定比普通函数快吗？为什么？
> 
> 不一定，需结合场景：
> 
> - 优势场景：短函数+高频调用（如标准库工具函数），或含 Lambda 的高阶函数（避免对象创建）
> - 劣势场景：长函数内联会导致代码膨胀（函数体复制到所有调用处，增加 APK 体积）；若函数仅调用一次，内联的开销（编译时间）可能超过运行时收益。
> 
> 最佳实践：只对小而频繁调用的高阶函数使用内联（如 Kotlin 标准库的设计原则）。

Lambda 用起来很轻巧，但在 JVM 上每个 Lambda 都会被编译成一个函数对象，高频调用时这些对象会带来实实在在的开销。inline 让编译器把函数体和 Lambda 体直接复制到调用处，从而消除函数对象和调用开销，也是 reified 类型参数能够存在的前提。

内联是一种特殊的操作符，它允许我们将高阶函数或泛型属性中的代码转移到由 Kotlin 生成的 Java bytecode 级别的函数调用点。

这将在代码执行过程中为我们带来性能上的优势，因为代码将被转移到使用点。

## 高阶函数

高阶函数（HOF）在 kotlin 和其他语言中处理回调问题方面享有盛誉。在日常场景下，经常会遇到这样的情况：执行范围被转移，而另一个线程（Thread）或协程（Coroutine）的返回值最终会在其操作结束时被看到。

高阶函数是接收函数作为参数，或者返回一个函数的函数。函数在这里和数字、字符串一样，是一个可以传来传去的值。

```kotlin
fun main() {
    val description = "Inline giving you magical powers"
    executeOperation {
        print(description)
    }
}

fun executeOperation(operationTwo: () -> Unit) {
    operationOne()
    operationTwo()
    operationThree()
}
```

## 为什么需要内联

内联函数通过 inline 关键字修饰，其核心思想是：在编译时将函数体直接插入到调用处，而非进行传统的函数调用。这意味着：

* 消除了函数调用的栈帧创建、参数传递等开销。

* 对 Lambda 表达式进行深度优化，避免匿名类对象的创建。

**与高阶函数的关系**

高阶函数是将函数作为参数或返回值的函数（如 map、filter），而内联函数常作为高阶函数的“优化搭档”。当高阶函数接收Lambda 表达式时，配合 inline 关键字可显著提升性能。

没有 inline 时，一个接收 lambda 的函数在编译后是这样工作的：调用方先创建一个函数对象，再把它传给函数，函数内部通过 invoke() 调用它。

如果这个函数在循环里被调用成千上万次，就会产生成千上万个临时对象。

```kotlin
// 文件路径：InlineBasic.kt

// 普通高阶函数：Lambda 会被编译成一个函数对象
fun runTask(action: () -> Unit) {
    action()
}

// 内联函数：编译器把函数体和 Lambda 体复制到调用处
inline fun runTaskInline(action: () -> Unit) {
    action()
}

fun main() {
    runTask { println("普通调用：RUNOOB") }
    runTaskInline { println("内联调用：RUNOOB") }
}
```

两者的输出完全一样，区别只在编译结果。

```
普通调用：RUNOOB
内联调用：RUNOOB
```

可以用 javap -p -c InlineBasicKt 查看字节码：普通调用会生成一个函数并调用它的 invoke，内联调用则只剩下两条 println 指令。

| 对比项       | 普通高阶函数       | 内联函数            |
| --------- | ------------ | --------------- |
| Lambda 参数 | 编译成函数对象      | 直接展开到调用处，不创建对象  |
| 调用方式      | 通过 invoke 调用 | 代码内联，无额外调用      |
| 生成代码体积    | 较小，只有一份函数体   | 每个调用处都复制一份，体积变大 |
| 非局部返回     | 不支持          | 支持              |

一段 Lambda 看起来只有几行代码：

```kotlin
measure {
    loadUsers()
}
```

为什么 kotlin 还专门提供了 inline？

如果只是为了“少调用一次函数”，JVM 自己也会做方法内联，似乎没必要再加一个关键字。

真正的原因在于 Kotlin 高阶函数。

普通高阶函数接收 Lambda 时，Lambda 需要以函数对象的形式传进去，再通过 invoke() 执行。某些无捕获 Lambda 可以被缓存，JVM 也可能继续优化，但从语言模型看，它仍然是一份需要传递和调用的函数值。

inline 会在编译期把函数体和可内联 Lambda 展开到调用处，常见收益有三个：

* 减少函数对象和间接调用带来的开销

* 允许 Lambda 使用非局部 return

* 配合 reified 在运行时使用泛型类型

所以 inline 不只是一个性能关键字，它还会改变 Lambda 的控制流能力和泛型能力。

这也是 let、run、apply、also、use、forEach 等标准库函数大量使用 inline 的原因。

无捕获 Lambda（无捕获 Lambda 是指函数体里没有用到外层函数的局部变量、参数，也没有用到外层类的实例。它闭包里是空的，不携带创建现场的任何数据）可能被编译成单例，JVM 的 JIT 也可能消除部分分配和调用。inline 的意义是让 Kotlin 编译器在编译阶段明确展开代码，而不是把所有希望都留给运行时优化。

**inline 不等于一定要快**

inline 经常和性能放在一起讲，但不能把它当作“加了就快”的开关。

适合内联的函数通常有这些特征：

* 接收一个或多个 Lambda

* 函数体比较短

* 调用频率较高

* Lambda 通常立即执行，不需要保存

* 需要非布局返回

* 需要 reified 泛型

不适合随便内联的函数：

* 函数体很大

* 调用点很多

* 没有 Lambda，也不需要 reified

* 很少调用，性能收益没有实际意义

* 递归函数

内联函数体会复制到每个调用点。

假设一个 100 行函数被调用 50 次，内联后可能在不同位置重复生成大量代码，带来：

* 字节码体积增大

* 编译时间增加

* 指令缓存压力增大

* 调试栈和代码定位更复杂。

给没有函数参数、也没有 reified 参数的普通函数加 inline，编译器通常还会给出 NOTHING_TO_INLINE 警告。

```kotlin
// 通常没有必要
inline fun add(a: Int, b: Int): Int {
    return a + b
}
```

## inline 关键字

在 fun 前面加 inline 就是内联函数，它的函数体以及所有函数类型参数都会在调用处展开。

![](https://www.runoob.com/wp-content/uploads/2026/09/12-inline-expand.svg)

普通代码参数（非函数类型）只是被复制过去，函数类型则连 Lambda 体一起复制。

```kotlin
// 给 List 增加一个内联的遍历扩展
inline fun <T> List<T>.forEachRunoob(action: (T) -> Unit) {
    for (element in this) {
        action(element)      // 内联后这里直接展开成 Lambda 的代码
    }
}

fun main() {
    val sites = listOf("Runoob", "www.runoob.com", "RUNOOB")

    sites.forEachRunoob {
        println(it)
    }
}
```

```
Runoob
www.runoob.com
RUNOOB
```

内联函数的调用语法和普通函数完全相同，调用方不需要知道它是内联的。

标准库里的 forEach、map、filter、let、run、apply、also、repeat 都是内联函数，这也是它们能支持非局部返回的原因。

## 非局部返回

内联函数最直观的好处是：Lambda 里的 return 可以从外层函数直接返回，这叫非局部返回（non-local return）。

普通 Lambda 里的 return 只结束 Lambda 自己。内联函数不一样：编译器会把 Lambda 的函数体复制到调用处，于是里面的 return 和写在外层函数是一回事，会直接结束外层函数。这就是非局部返回。

因为 Lambda 的代码已经被复制进调用方函数，这个 return 在编译后就位于外层函数体内。

```kotlin
inline fun <T> List<T>.forEachRunoob(action: (T) -> Unit) { // 内联函数
    for (element in this) {
        action(element)
    }
}

fun main() {
    listOf(1, 2, 3, 4, 5).forEachRunoob { // Lambda 
        if (it == 3) return          // 非局部返回：直接从 main 返回
        println(it)
    }
    println("这一行不会执行")
}
```

把上面的 inline 去掉，代码就无法编译，因为普通函数的 Lambda 是一个独立对象，不能跳回调用方。

```kotlin
// 非内联函数：下面的写法会编译失败
fun runTask(action: () -> Unit) {
    action()
}

fun main() {
    runTask {
        // 编译错误：'return' is not allowed here
        // return
        println("只能正常返回，不能跳出 main")
    }
}
```

注意：非局部返回只在 Lambda 直接作为参数传给内联函数时有效。如果 Lambda 先被存进变量、或者被传给一个普通函数，它就失去了内联上下文，不能再跳出外层函数。

非局部返回很方便，也很容易误伤外层流程。

如果需求只是跳过当前元素，不能直接写裸 return，应该写 return@forEach:

```kotlin
fun printNormalUsers(users: List<String>) {
    users.forEach { user ->
        if (user == "admin") {
            return@forEach
        }

        println(user)
    }

    println("处理完成")
}

fun main() {
    printNormalUsers(listOf("Tom", "admin", "Lucy"))
}
```

输出：

```
Tom
Lucy
处理完成
```

两者返回的区别：

```
return           // 退出外层函数
return@forEach   // 只退出当前 Lambda 调用
```

代码审查时看到内联 Lambda 里的裸 return，需要特别留意它到底退出哪一层。

## Demo：用 inline 封装统一执行流程

很多项目都会重复写日志、耗时统计和异常处理。

可以把固定流程放进一个内联函数：

```kotlin
inline fun <T> executeTask(
    taskName: String,
    block: () -> T
): Result<T> {
    val start = System.nanoTime()
    println("[$taskName] 开始")

    return try {
        val result = block()
        val cost = System.nanoTime() - start
        println("[$taskName] 成功，耗时 ${cost}ns")
        Result.success(result)
    } catch (e: Exception) {
        val cost = System.nanoTime() - start
        println("[$taskName] 失败，耗时 ${cost}ns，原因：${e.message}")
        Result.failure(e)
    }
}
```

使用：

```kotlin
fun queryOrder(orderId: Long): String {
    require(orderId > 0) { "订单编号必须大于 0" }
    return "ORDER-$orderId"
}

fun main() {
    val success = executeTask("查询订单") {
        queryOrder(1001)
    }

    val failure = executeTask("查询订单") {
        queryOrder(-1)
    }

    println(success.getOrNull())
    println(failure.exceptionOrNull()?.message)
}
```

输出类似：

```kotlin
[查询订单] 开始
[查询订单] 成功，耗时 350000ns
[查询订单] 开始
[查询订单] 失败，耗时 120000ns，原因：订单编号必须大于 0
ORDER-1001
订单编号必须大于 0
```

实际耗时会随运行环境变化。

这种小型流程函数很适合 inline：固定逻辑不复杂，Lambda 在函数内立即执行，也不需要保存到其他地方。

## noinline

内联函数的函数类型参数（Lambda 参数）默认全部内联，如果某个 Lambda 需要被保存到变量、作为参数传递给非内联函数，就要用 noinline 关掉它的内联。

内联函数里的 Lambda 参数默认可以被内联。可内联参数只能在允许内联的位置调用，不能随意保存、返回，或者传给普通非内联函数。

下面的代码会报错：

```kotlin
inline fun register(block: () -> Unit) {
    // 编译失败：非法使用内联参数
    // listeners += block
}
```

原因很直接：block 原本要在调用点展开，现在却要被保存到集合里，后面再执行。编译器无法同时把它当成“展开的代码”和“长期存在的函数对象”。

这时需要 noinline。

被 noinline 修饰的参数仍然会创建函数对象，因此只在你确实需要把它当值使用时才加。

```kotlin
// onFinish 需要当作值使用，所以标记为 noinline；action 保持内联
inline fun runTask(noinline onFinish: () -> Unit, action: () -> Unit) {
    action()

    // noinline 的 Lambda 可以赋给变量，也可以再传给其他函数
    val handler: () -> Unit = onFinish
    handler()
}

fun main() {
    runTask(
        onFinish = { println("任务结束：RUNOOB") },
        action = { println("任务执行：www.runoob.com") }
    )
}
```

```
任务执行：www.runoob.com
任务结束：RUNOOB
```

如果去掉 noinline，编译器会报错，提示不能把内联参数保存下来，因为它已经不再是一个真实存在的对象。

注意：加了 oninline 后，onFinish 函数里面不允许 return。

noinline 参数具有普通函数值的能力：

* 保存到属性或集合

* 赋值给变量

* 作为返回值返回

* 传给普通高阶函数

* 稍后或异步执行

代价也很明确：它不再享受该 Lambda 参数的内联收益，而且不能使用非局部 return。

## crossinline

Crossinline 基本上是用来表示高阶函数不允许在执行块中进行本地返回。

还有一种情况：Lambda 仍然内联，但它被放进了另一个执行上下文，比如另一个 Lambda 或对象表达式里。

此时需要用 crossinline，它保留内联，同时禁止在这个 Lambda 里使用非局部返回。

```kotlin
// crossinline：block 仍然内联，但不允许非局部返回
inline fun runCrossInline(crossinline block: () -> Unit) {
    // block 被放进 Runnable 的 Lambda 里，属于另一个执行上下文
    val task = Runnable { block() }
    task.run()
}

fun main() {
    runCrossInline {
        println("crossinline 中的代码：RUNOOB")
        // 编译错误：'return' is not allowed here
        // return
    }
}
```

```
crossinline 中的代码：RUNOOB
```

不加 crossinline 时，上面把 block 放进 Runnable 的写法会报错，提示需要给参数加上 crossinline 修饰符。

block 原本允许非局部返回，但真正执行它的是另一个线程里的 Runnable。等子线程执行时，外层函数早已可能结束，裸 return 根本没有合法的返回目标。

解决方法就是添加 crossinline。

标准库中的 sortedBy 就使用了 crossinline，因为它要把选择器传给 compareBy，属于在另一个上下文中使用。

| 修饰符         | 是否内联 | 是否非局部返回 | 典型场景                       |
| ----------- | ---- | ------- | -------------------------- |
| 默认          | 是    | 是       | Lambda 只在函数体内直接调用          |
| noinline    | 否    | 不可以     | Lambda 要保存成变量或传给其他函数       |
| crossinline | 是    | 不可以     | Lambda 在另一个 Lambda 或对象里被调用 |

crossinline 和 noinline 都会根据构建的流程被生成脚本进行相当大的修改，因此代码生成器可能会根据版本的不同改变在 Java 中的实现方式。可能会出现新的类或中间方法，但原则保持不变。

crossinline 的含义可以拆成两部分：

cross：Lambda 会跨到另一层函数、对象或执行上下文中调用

inline：仍然保留内联参数的性质，但禁止非局部 return

上面的 Thread {...} 本身需要一个 Runnable，编译器可能生成包装对象。crossinline 表示参数没有被 noinline 化，不代表外围执行结构不需要对象。

## inline、noinline、crossinline 对比

| 写法             | Lambda 是否可内联 | 能否保存或稍后执行    | 能否非局部 return | 常见场景           |
| -------------- | ------------ | ------------ | ------------ | -------------- |
| 普通 Lambda 参数   | 否            | 可以           | 不可以          | 一般高阶函数         |
| inline 参数      | 可以           | 不可以          | 可以           | 立即执行的小型 Lambda |
| noinline 参数    | 否            | 可以           | 不可以          | 回调保存、返回、转交     |
| crossinline 参数 | 可以           | 不能直接当普通函数值保存 | 不可以          | 嵌套对象、线程、延迟执行结构 |

一句白话概括：

```
inline    现场展开
noinline  留下一个函数对象以后用
crosssinline   可以跨层调用，但不能从外层函数直接 return
```

## reified

在 Kotlin 中，reified 是一个特殊的关键字，用于修饰内联函数中的类型参数。这使得在函数内部可以访问类型参数的具体类型。通常情况下，由于类型擦除（type erasure），在运行时是无法直接获取泛型类型参数的具体类型的。reified 关键字解决了这个问题。

普通泛型函数里不能直接判断 value is T:

```kotlin
fun <T> isType(value: Any): Boolean {
    // 编译失败：运行时不知道 T 是什么
    // return value is T
    return false
}
```

JVM 的泛型会在编译后被擦除，运行时拿不到具体的类型参数，所以普通泛型函数里不能写 is T 或 T::class。

JVM 泛型通常存在类型擦除。运行时执行到函数内部时，T 的具体类型信息可能已经不存在。

传统做法是显式传入 Class 或 KClass：

```kotlin
fun <T : Any> isType(value: Any, type: kotlin.reflect.KClass<T>): Boolean {
    return type.isInstance(value)
}

fun main() {
    println(isType("Kotlin", String::class))
    println(isType(100, String::class))
}
```

输出：

```
true
false
```

配合 inline 和 reified，类型参数可以实化：

```kotlin
inline fun <reified T> isType(value: Any): Boolean {
    return value is T
}
```

调用：

```kotlin
fun main() {
    println(isType<String>("Kotlin"))
    println(isType<String>(100))
    println(isType<Int>(100))
}
```

输出：

```kotlin
true
false
true
```

为什么 reified 必须和 inline 一起使用？

调用处本来就知道具体类型：

```kotlin
isType<String>(value)
```

函数内联后，编译器可以把 T 替换成 String，效果近似：

```kotlin
value is String
```

如果函数没有内联，运行时只进入同一份通用函数体，类型擦除后就无法直接知道每次调用传入的 T。

reified 把类型实参保留到调用处，编译器会把 T 替换成真实类型，从而绕开类型擦除。

它只能用在 inline 函数上，因为只有内联才能在每个调用点看到具体类型。

Reified 的意思是“将抽象的东西看成具体的”。特别是在 java 中，泛型是在非常特定的上下文中定义的，所有泛型在实现时无一例外都是抽象的。

```kotlin
// reified 必须搭配 inline 使用
inline fun <reified T> Any?.isInstanceOf(): Boolean = this is T

// 运行时获取类型名
inline fun <reified T> typeName(): String = T::class.simpleName ?: "未知类型"

class Site(val name: String)

fun main() {
    val value: Any = "RUNOOB"

    println(value.isInstanceOf<String>())
    println(value.isInstanceOf<Int>())

    println(typeName<String>())
    println(typeName<Int>())
    println(typeName<Site>())
}
```

```
true
false
String
Int
Site
```

reified 最实用的场景是配合集合做类型过滤，标准库的 filterIsInstance 就是这样实现的。

```kotlin
inline fun <reified T> List<*>.filterIsInstanceRunoob(): List<T> {
    val result = mutableListOf<T>()
    for (element in this) {
        if (element is T) {      // 因为 reified，这里才能对 T 做类型判断
            result.add(element)
        }
    }
    return result
}

fun main() {
    val mixed: List<Any> = listOf(1, "Runoob", 2, "RUNOOB", 3.14)

    val strings = mixed.filterIsInstanceRunoob<String>()
    println(strings)
}
```

```
[Runoob, RUNOOB]
```

注意：reified 只能做类型判断和获取类型信息，不能直接写 T() 来创建实例，因为编译器并不知道 T 的函数长什么样。需要创建实例时，可以额外接收一个构造器引用参数。

**使用 reified 关键字的条件**

要是用 reified 关键字，需要遵循以下几点：

1. 函数必须是内联的（使用 inline 关键字修饰）。

2. 类型参数前需要加上 reified 关键字。

**获取类型参数的 Java 类**

当你使用 reified 关键字修饰一个内联函数的类型参数时，可以通过 T::class.java 获取类型参数对应的 Java 类。这在需要访问泛型类型参数的具体类型时非常有用，比如在反射操作中。

下面是一个简单的例子：

```kotlin
import kotlin.reflect.KClass

inline fun <reified T : Any> getClass(): KClass<T> {
    return T::class
}

inline fun <reified T : Any> getJavaClass(): Class<T> {
    return T::class.java
}

fun main() {
    val stringKClass = getClass<String>()
    println("KClass for String: $stringKClass") // 输出 "KClass for String: class kotlin.String"

    val stringJavaClass = getJavaClass<String>()
    println("Java class for String: $stringJavaClass") // 输出 "Java class for String: class java.lang.String"
}
```

在这个示例中，定义了两个内联函数，getClass 和 getJavaClass，它们都接受一个 reified 类型参数 T。getClass 函数返回类型参数对应的 KClass 对象，而 getJavaClass 函数返回类型参数对应的 Java 类。在 main 函数中，用 String 类型参数调用者两个函数，并输出结果。

**注意事项**

需要注意的是，reified 关键字不能用于非内联函数，因为它们的类型参数在运行时会被擦除。此外，reified 类型参数不能用于普通类和接口，只能用于内联函数。

不会自动调用构造函数，T() 仍然不合法，要走 createInstance() 或 getDeclaredCondtractor().newInstance()。

## @PublishedApi

public 的内联函数会被复制到调用方模块，如果它内部访问了 internal 或 private 的声明，那些声明的字节码也必须在调用方可见。

在应用程序接口或库中使用内联发布任何内容时，你的代码最好在整个函数上下文中都是公开的。不过，为了防止代码被公开并干扰 open/closed(OCP)开闭原则。Kotlin 允许将代码内部化，只要在方法或类中应用特殊注解 PublishedAPI 即可。

/@PublishedApi 用来标记这类声明，让编译器为它生成公开的字节码，同时在Kotlin 层面仍然保持 internal 的可见性。

```kotlin
class SiteValidator {
    // 被 public 内联函数调用的 internal 成员，需要 @PublishedApi
    @PublishedApi
    internal fun checkName(name: String): Boolean = name.isNotEmpty()

    inline fun validate(name: String): Boolean = checkName(name)
}

fun main() {
    val validator = SiteValidator()

    println(validator.validate("Runoob"))
    println(validator.validate(""))
}
```

```
true
false
```

去掉 /@PublishedApi 后，编译器会报错，提示 public 内联函数不能访问非 public 的 API。

**Demo: 按类型过滤混合数据**

实现一个简化版 filterIsInstance：

```kotlin
inline fun <reified T> Iterable<*>.onlyType(): List<T> {
    return buildList {
        for (item in this@onlyType) {
            if (item is T) {
                add(item)
            }
        }
    }
}
```

使用：

```kotlin
fun main() {
    val values: List<Any> = listOf(
        "Kotlin",
        100,
        "Java",
        true,
        200
    )

    val strings = values.onlyType<String>()
    val numbers = values.onlyType<Int>()

    println(strings)
    println(numbers)
}
```

输出：

```kotlin
[Kotlin, Java]
[100, 200]
```

reified 常见用途：

* value is T

* value as? T

* T:class

* 按类型查找对象

* 序列化和反序列化入口

* 依赖容器按类型取对象

* 泛型路由或事件分发

**reified 也拿不到所有泛型细节**

reified 能保留 T 对象的运行时类型，但 JVM 的嵌套泛型参数仍然可能被擦除。

例如：

```kotlin
inline fun <reified T> printType() {
    println(T::class)
}

fun main() {
    printType<List<String>>()
    printType<List<Int>>()
}
```

两次拿到的运行时类本质上都是 List，无法只靠 T::class 区分 List<String> 和 List<Int>。

所以：

reified 解决了 T 本身的类型访问

不代表 JVM 泛型擦除彻底消失

需要完整保留嵌套泛型信息时，通常还要使用 typeOf<T>() 或序列化框架提供的类型令牌机制。

## Reified 核心原理：编译器的“魔法”

### 传统泛型的类型擦除：为何会丢失？

Java 和 kotlin 的泛型设计，是为了在编译时提供类型检查，但在编译为字节码后，为了兼容性（尤其是与 Java 早期版本的兼容性）和减少运行时开销，会将泛型类型擦除。例如，List<String> 和 List<Int> 编译后都变成了 List。

这种擦除导致了两个主要限制：

* 运行时无法获取类型信息：你不能在运行时通过 T::class.java 直接获取泛型参数 T 的 Class 对象。

* 强制类型转换风险：由于无法进行准确的运行时类型检查，不安全的强制类型转换可能引发 ClassCastException。

### Reified 的编译器增强：实化类型信息

reified 的出现正是为了打破类型擦除的壁垒，它通过实化泛型参数（Reified Type Atguments），让泛型类型在运行时依然可以被感知。其实现机制得益于 Kotlin 编译器的“魔法”：

1. 编译器生成独立字节码分支：当你调用一个带有 reified 泛型参数的 inline 函数时，编译器会在调用处为每个具体的泛型参数类型生成一份独立的字节码。这有点像函数重载，但实际上是编译器在编译时将 inline 函数的代码嵌入到调用点，并替换掉泛型参数为其实际类型。

2. 保留实际类型信息：由于代码被“内联”并替换了具体的类型，所以在运行时，这些具体的类型信息就得以保留。这使得你可以在 inline 函数内部直接通过 T::class.java 或 T::class 来获取泛型参数的 Class 对象。

3. 仅限于 inline 函数：reified 关键字必须与 inline 关键字联用。这是因为 reified 的核心机制就是通过在编译期将函数体展开（内联）到调用点，才能在字节码中嵌入具体的类型信息。如果不是 inline 函数，它无法进行这种编译期优化，也就无法实现类型实化。

reified 的引入，极大地提升了泛型编程的便利性和安全性。以下是一些典型的实战场景：

**简化类型解析：告别显式 Class 参数**

场景：从 JSON 字符串解析 Java/Kotlin 对象时，传统方式需要手动传入 Class<T> 参数。有了 reified，代码会变得无比简洁。

```kotlin
// 导入 Gson 库
import com.google.gson.Gson

// 使用 inline 和 reified 修饰函数和泛型参数
inline fun <reified T> parseJson(json: String): T {
    val gson = Gson()
    // 在这里，T 的类型信息被保留，可以直接使用 T::class.java
    return gson.fromJson(json, T::class.java)
}

data class User(val name: String, val age: Int)

fun main() {
    val jsonString = """{"name":"Alice", "age":30}"""

    // 调用时无需再传递 Class 参数，编译器会自动实化 T 为 User
    val user: User = parseJson(jsonString)
    println(user) // Output: User(name=Alice, age=30)
}
```

**安全的类型检查：is 关键字的泛型增强**

场景：判断一个对象是否为某种泛型类型，传统方式由于类型擦除而无法直接使用 is T。reified 让这一切成为可能。

```kotlin
inline fun <reified T> isType(obj: Any?): Boolean {
    // 编译期实化 T，使得 obj is T 的类型检查变得安全有效
    return obj is T
}

fun main() {
    val list: Any = ArrayList<String>()
    val number: Any = 123

    println(isType<ArrayList<String>>(list)) // Output: true
    println(isType<ArrayList<Int>>(list))    // Output: false
    println(isType<Int>(number))             // Output: true
}
```

**泛型集合过滤：类型安全的筛选逻辑**

场景：从一个包含多种类型元素的集合中，安全地筛选出特定类型的元素列表。reified 可以简化传统的类型转换和过滤逻辑。

```kotlin
// 扩展函数，直接作用于 List<Any?>
inline fun <reified T> List<Any?>.filterType(): List<T> {
    // filterIsInstance<T>() 是 Kotlin 标准库函数，内部也使用了 reified，
    // 它等价于 filter { it is T }.map { it as T }
    return filterIsInstance<T>()
}

fun main() {
    val items: List<Any?> = listOf("apple", 123, true, "banana", 456.7f)
    val strings: List<String> = items.filterType<String>() // 自动筛选出 String 类型元素
    val numbers: List<Number> = items.filterType<Number>() // 自动筛选出所有 Number 类型元素

    println(strings) // Output: [apple, banana]
    println(numbers) // Output: [123, 456.7]
}
```

**网络请求封装：统一处理相应类型**

场景：在实际应用开发中，尤其是在使用像 Retrofit 这样的网络库时，我们经常需要封装统一的网络请求处理逻辑，例如处理成功或失败的响应。reified 可以帮助避免类型擦除带来的解析问题。

```kotlin
import retrofit2.Call
import retrofit2.Callback
import retrofit2.Response

// 假设 Result 是一个密封类或带泛型的类，表示成功或失败
sealed class MyResult<out T> {
    data class Success<out T>(val data: T) : MyResult<T>()
    data class Failure(val exception: Exception) : MyResult<Nothing>()
}

// 扩展 Retrofit 的 Call 类，让其可以直接返回 MyResult
inline fun <reified T> Call<T>.safeEnqueue(
    crossinline onSuccess: (T) -> Unit,
    crossinline onFailure: (Exception) -> Unit
) {
    this.enqueue(object : Callback<T> {
        override fun onResponse(call: Call<T>, response: Response<T>) {
            if (response.isSuccessful) {
                response.body()?.let(onSuccess) ?: onFailure(NullPointerException("Response body is null"))
            } else {
                onFailure(Exception("API Error: ${response.code()} - ${response.message()}"))
            }
        }

        override fun onFailure(call: Call<T>, t: Throwable) {
            onFailure(Exception(t))
        }
    })
}

// 假设 apiService.getUser() 返回 Call<User>
// apiService.getUser().safeEnqueue(
//     onSuccess = { user -> handleUser(user) },
//     onFailure = { e -> showError(e) }
// )

// 或者更进一步，直接返回一个 MyResult 对象（需要阻塞式执行或配合协程）
inline fun <reified T> Call<T>.executeWithType(): MyResult<T> {
    return try {
        val response = execute() // 注意：此方法会阻塞当前线程，通常在协程中调用
        if (response.isSuccessful) {
            response.body()?.let { MyResult.Success(it) }
                ?: MyResult.Failure(NullPointerException("Body is null"))
        } else {
            MyResult.Failure(Exception("Error code: ${response.code()} - ${response.message()}"))
        }
    } catch (e: Exception) {
        MyResult.Failure(e)
    }
}

// val result = apiService.getUser().executeWithType<User>() // 在协程中调用
// when (result) {
//     is MyResult.Success -> handleUser(result.data)
//     is MyResult.Failure -> showError(result.exception)
// }
```

注意：executeWithType 是一个阻塞调用，在 Android 开发中通常应在协程（Coroutine）或其他异步机之中调用，以避免阻塞主线程。

### Reified 使用限制与最佳实践

尽管 reified 功能强大，但它并非没有限制。理解这些限制并遵循最佳实践，能帮助你更有效地利用它。

#### 1. 必须与 inline 关键字联用

这是 reified 最核心的限制。不能单独使用 reified 而不加 inline：

```kotlin
// 正确用法：inline + reified
inline fun <reified T> createInstance(): T {
    return T::class.java.getDeclaredConstructor().newInstance() as T
}

// 错误用法：缺少 inline
// fun <reified T> createInstance() = T::class.java.newInstance() // 编译报错：Cannot use 'reified' type parameter on a non-inline function
```

原因如前所述，reified 的实现机制依赖于 inline 函数的编译时代码展开。

#### 2. 泛型参数的约束

* 可与 where 子句结合使用类型约束：仍然可以对 reified 泛型参数施加类型约束，使其必须是某个特定类型或实现某个接口。
  
  ```kotlin
  inline fun <reified T : Number> calculate(value: String): T {
      // T 必须是 Number 的子类，例如 Int, Double, Float 等
      // ...
      return value.toFloat() as T // 示例转换，实际可能需要更复杂的逻辑
  }
  ```

* 无法用于可空类型：你不能直接声明一个 reified T: Any? 这样的泛型参数。reififed 只能用于非空类型。如果你需要处理空值，应将 T 声明为非空，然后在函数内部单独处理 T？的情况。
  
  ```kotlin
  // 错误用法：
  // inline fun <reified T : Any?> processNullable(item: T) { ... }
  
  // 正确用法：
  inline fun <reified T : Any> processNullable(item: T?) {
      if (item != null) {
          // 在这里 item 是非空的 T 类型
          println("Processing non-null ${item::class.simpleName}")
      } else {
          println("Processing null item")
      }
  }
  ```

#### 3. 避免滥用：性能与可读性权衡

* 性能影响：尽管 inline 函数可以减少函数调用的开销，但它的副作用是会增加代码体积（DEX膨胀）。如果大量使用 inline 函数，并且这些函数体较大，可能会导致最终的 APK 或 Jar 文件变大。在 Android 开发中，过大的 DEX 文件可能会影响应用启动速度。

* 可读性：并非所有的泛型场景都适合使用 reified。过度或不恰当地使用 reified 可能导致复杂的泛型逻辑更难以理解，反而降低代码的清晰度。建议将其封装为通用且可重用的工具函数或扩展函数。

## inline 属性

除了函数，属性的访问器也可以内联。

```kotlin
class User(
    val firstName: String,
    val lastName: String
)

inline val User.fullName: String
    get() = "$firstName $lastName"
```

使用：

```kotlin
fun main() {
    val user = User("Tom", "Smith")
    println(user.fullName)
}
```

只有没有幕后字段（编译器为属性准备的那个真正存值的字段）的属性访问器才适合内联。

```kotlin
var score: Int = 0
    set(value) {
        field = value          // field 就是 score 的幕后字段
    }
```

也可以单独修饰 getter 或 setter：

```kotlin
val User.displayName: String
    inline get() = "$lastName, $firstName"
```

实际项目里，内联属性远没有内联高阶函数和 reified 常见，了解语法即可。

## public inline 为什么不能随便访问 private 成员

下面的代码会报错：

```kotlin
class TokenStore {
    private val token = "secret"

    inline fun read(block: (String) -> Unit) {
        // 编译失败：public inline 不能访问 private 成员
        block(token)
    }
}
```

read 是 public inline，调用它的代码可能位于另一个模块。函数体会展开到调用模块里，而那个模块本来无权访问 TokenStore 的 private token。

常见解决方式：

* 把内联函数改为 internal 或 private

* 不直接访问私有成员

* 将需要暴露给内联代码的成员设为 internal，并谨慎使用 @PublishedApi

示例：

```kotlin
class TokenStore {
    @PublishedApi
    internal val token = "secret"

    inline fun read(block: (String) -> Unit) {
        block(token)
    }
}
```

@PublishedApi 表示这个 internal 成员会成为公开内联实现的一部分。它不是简单的 “绕过限制”，修改时需要考虑二进制兼容性。

## 内联函数会影响库的升级方式

普通函数调用通常保留为方法调用。库升级后，只有二进制签名兼容，调用方可以在不重新编译的情况下执行新版函数体。

内联函数的旧函数体已经复制进调用方产物。

如果一个库修改了 public inline 函数实现，但应用没有重新编译，应用里可能继续运行旧的内联代码。

所以公共库里的内联函数需要特别注意：

* 保持实现短小稳定

* 谨慎引用 @PublishedApi 成员

* 修改实现后重新编译使用方

* 不把复杂且频繁变化的业务逻辑做成公共内联 API

这也是“不要无脑 inline” 的另一个原因。

## 什么时候不该用 inline

inline 不是 “更快的开关”，它本质是用代码体积换运行开销。

函数体越大、调用点越多，复制带来的体积膨胀就越明显，甚至可能因为指令缓存实效而变慢。

| 适合使用inline             | 不适合使用inline                   |
| ---------------------- | ----------------------------- |
| 函数体很小，接收 lambda 参数     | 函数体很长，逻辑复杂                    |
| 需要 reified 类型参数        | 没有函数类型参数，也没有 reified 需求       |
| 需要非局部返回来简化控制流          | Lambda 需要被保存或转交（应该用 noinline） |
| 调用频率高、希望避免 lambda 对象分配 | 调用点非常多而函数题又大，体积急剧增长           |

一个简单的判断标准：只有当函数“接收 Lambda 参数”或者“需要 reified” 时，才考虑加 inline，其余情况通常得不偿失。  

需要 reified 类型参数：reified 把类型实参保留到调用处，编译器会把 T 替换成真实类型，从而绕开类型擦除。但是 reified 只能用在 inline 函数上，因为只有内联才能在每个调用点看到具体类型。

## 常见问题

* inline 会让程序一定更快吗？不一定，它省掉 Lambda 对象和调用开销，但会增加代码体积，函数题大时可能变慢。

* 为什么我的 return 编译报错？非局部返回要求 Lambda 直接传给内联函数；如果 Lambda 被存成变量就不再成立。

* noinline 和 crossinline 有什么区别？noinline 不内联、可当值用；crossinline 仍内联，但禁止非局部返回。

* reified 能用在普通函数上吗？不能，reified 只允许出现在 inline 函数的类型参数上。

* 为什么内联函数访问 internal 会报错？内联函数会被复制到其他模块，需要给被访问的声明加 /@PublishedApi

## 常见误区

**inline 会把所有东西都变成零开销**

不会。内联可以减少高阶函数的部分开销，但 Lambda 内部创建的集合、字符串、线程和业务对象不会凭空消失。

**Lambda 每次一定创建新对象**

不一定。

无捕获 Lambda （函数体里没有用到外层函数的局部变量、参数，也没有用到外层类的实例）可能复用实例，JVM 也可能优化掉部分分配（即时编译器的逃逸分析有时会把美逃出方法的对象分配消掉，有捕获的 Lambda 也可能收益。这取决于是否内联得动、对象有没有被存下来，不保证发生）。捕获变量、保存回调和跨作用域使用时，更容易产生实际对象。

**noinline 只是“不优化“，其他行为不变**

不完全对。

noinline 让参数恢复成普通函数值，因此可以保存和传递，但也失去了非局部返回能力。

**crossinline 表示异步**

不是。

crossinline 只限制非局部返回。它常出现在异步封装里，也可以用在同步的嵌套对象或另一个 Lambda 中。

**reified 保留全部泛型信息**

不是。

reified 让内联函数能使用具体的 T，嵌套泛型参数仍可能受 JVM 类型擦除影响。

**普通函数调用一定比 inline 慢**

不一定。

JVM JIT 本身会根据运行情况内联普通方法。Kotlin inline 更重要的场景是高阶函数、非局部返回和 reified，不是给每个小函数手动贴性能标签。

## 什么时候该用哪一个

遇到 Lambda 参数时，可以按下面的顺序判断：

```
Lambda 是否需要保存、返回或稍后调用？
是 -> noinline 或直接使用普通高阶函数


Lambda 是否会放进另一个 Lambda、对象或执行上下文？
是 -> crossinline 

Lambda 是否理解执行，而且函数短小、调用频繁？
是 -> 可以考虑 inline

是否需要 T::class、 is T、as? T?
是 -> inline + reified
```

还有一个更实际的判断标准：

没有测量结果时，不要为了“可能更快”把大型函数全部内联。

## 总结

inline 最核心的代码：

```kotlin
inline fun <T> measure(block: () -> T): T {
    return block()
}
```

它让函数体和可内联 Lambda 在调用处展开，常用于小型高阶函数。

三个关键字的分工：

```kotlin
inline     展开函数体和 Lambda，支持非局部返回
noinline   保留普通函数对象，允许保存、传递和稍后执行
crossinline 保持可内联，但禁止非局部返回
```

再加上一个重要组合：

```kotlin
inline fun <reified T> isType(value: Any): Boolean {
    return value is T
}
```

reified 借助内联，让泛型函数能在调用处使用具体类型。

一句话概括：

`inline` 不是给所有函数准备的性能按钮，而是 Kotlin 为高阶函数提供的一套编译期展开机制；用得合适，可以减少抽象开销，也能表达普通函数做不到的控制流和类型操作。

## 与 Java 泛型对比：Kotlin 的独特优势

| 特性          | Java 泛型                          | Kotlin Reified 泛型                 |
| ----------- | -------------------------------- | --------------------------------- |
| 运行时类型信息     | 不可见（类型擦除）                        | 可见（实化类型）                          |
| 获取 Class 对象 | 需显式传递 Class<T> 参数                | 直接使用 T::class.java 或 T::class     |
| 类型检查（is）    | 仅支持原始类型检查（如 obj instanceof List） | 直接泛型类型安全检查（如 obj is List<String>） |
| 代码冗余度       | 较高（需传递类型参数）                      | 较低（编译器自动实化）                       |
| 空安全         | 需手动处理 null                       | 结合 Kotlin 空安全机制                   |

## 总结：Reified 如何提升开发效率

reified 的出现是 Kotlin 泛型发展的一个里程碑。它通过编译器的巧妙增强，彻底解决了传统泛型在运行时类型信息丢失的痛点，为开发者带来了显著的优势：

* 简化代码：告别了冗余的 Class<T> 参数传递，让泛型代码更加简化、优雅。

* 类型安全：在运行时保留了类型信息，从而能够进行安全的泛型类型检查，有效避免了 ClassCastException 运行时错误。

* 逻辑封装：使得类型解析、集合过滤、网络请求封装等通用泛型逻辑的实现变得更加简单和直观。

* 与 Kotlin 生态契合：reified 与 kotlin 的其他特性（如协程、扩展函数）无缝结合，进一步提升了开发体验。

合理且明智地使用 reified，能够让你的 Kotlin 泛型编程更接近“理想中的样子”，提供更强大的类型安全性，并显著提升开发效率。下次在 Kotlin 开发中遇到泛型类型擦除问题时，不妨尝试用 inline+reified 来重构代码，体验类型安全与简洁性的双重提升！

## 常用内联函数源码剖析

### 1. let 函数：对象操作的灵活助手

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T, R> T.let(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block(this)
}
```

* 源码解读：let 函数是一个扩展函数，接收一个 Lambda 表达式 block 作为参数。@kotlin.internal.InlineOnly 注解表明该函数只能内联调用。constract 部分告诉编译器 block Lambda 表达式只会被调用一次。return block(this)将调用对象this作为参数传递给 block 并返回其结果。

* 创新应用场景：在处理可空对象时，let 函数可以安全地对对象进行操作。例如，我们可以结合 let 函数和 run 函数实现更复杂的链式操作。

### 2. run 函数：代码块执行的得力干将

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T, R> T.run(block: T.() -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return block()
}
```

* 源码解读：同样是扩展函数，run 函数接收的 Lambda 表达式 block 以调用对象 this 作为接收者。通过 contract 告知编译器 block 只会被调用一次，最后执行 block 并返回结果。

* 创新应用场景：在进行对象初始化和配置时，run 函数可以让代码更加简洁。可以结合 apply 函数，实现对象的初始化和后续操作的链式调用。

### 3. with 函数：对象上下文的贴心陪伴

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return receiver.block()
}
```

* 源码解读：with 函数是普通函数，接收一个对象 receiver 和 Lambda 表达式 block。contract 保证 block 只被调用一次，通过 receiver.block() 以 receiver 为接收者执行 block 并返回结果。

* 创新应用场景：在处理集合时，with 函数可以方便地对集合进行操作。可以结合 also 函数，在对集合进行操作的同时记录日志。

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val sum = with(numbers) {
    filter { it % 2 == 0 }.sum()
}.also {
    println("The sum of even numbers is: $it")
}
```

### 4. apply 函数：对象配置的链式大师

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T> T.apply(block: T.() -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block()
    return this
}
```

* 源码解读：apply 函数接收一个无返回值的 Lambda 表达式 block，以调用对象 this 为接收者执行 block，最后返回调用对象本身，支持链式调用。

* 创新应用场景：在创建复杂对象时，apply 函数可以让对象的配置更加清晰。可以结合 let 函数，在对象配置完成后进行一些额外的处理。

```kotlin
val button = Button(context).apply {
    text = "Click me"
    setOnClickListener { /* ... */ }
}.let {
    it.layoutParams = LinearLayout.LayoutParams(
        LinearLayout.LayoutParams.WRAP_CONTENT,
        LinearLayout.LayoutParams.WRAP_CONTENT
    )
    it
}
```

### 5. also 函数：对象副作用的处理专家

```kotlin
@kotlin.internal.InlineOnly
public inline fun <T> T.also(block: (T) -> Unit): T {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    block(this)
    return this
}
```

* 源码解读：also 函数接收一个 Lambda 表达式 block，将调用对象 this 作为参数传递给 block，执行 block 后返回调用对象本身。

* 创新应用场景：在对对象进行操作时，also 函数可以方便地进行副作用处理。可以结合 run 函数，在对象操作前后进行不同的处理。

```kotlin
val file = File("example.txt").also {
    it.createNewFile()
}.run {
    writeText("Hello, World!")
    this
}
```

### 大函数对比表

| 函数    | 作用             | 接收者/参数                     | 返回值        | Lambda 内 this 指向 | 典型场景                                         |
| ----- | -------------- | -------------------------- | ---------- | ---------------- | -------------------------------------------- |
| let   | 安全调用+作用域内变量重命名 | 扩展函数（T.let）                | Lambda 返回值 | 函数参数（it）         | 可空对象判空后操作：str?.let {...}                     |
| run   | 对象作用域内执行代码块    | 扩展函数（T.run）                | Lambda 返回值 | 当前对象（this）       | 对象配置后需要返回特定结果：view.run {init(); calculate()} |
| apply | 对象配置（链式调用）     | 扩展函数（T.apply）              | 当前对象（this） | 当前对象（this）       | 对象初始化 Button().apply{text=OK";onClick={...}} |
| also  | 副作用处理（记录日志/调试） | 扩展函数（T.also）               | 当前对象（this） | 函数参数(it)         | 操作后返回原对象：file.also{log(it)}.delete()         |
| with  | 进入对象上下文（非扩展函数） | 普通函数（with(receiver,block)） | Lambda返回值  | 接收者对象（this）      | 避免重复书写对象名：with(list){sort();filter(...);}    |

## 3 大核心原理（需结合编译过程说明）

### 1. 消除函数调用栈：

- 传统函数调用：压栈->参数传递->执行->弹栈（有固定开销）

- 内联后：函数体直接替换调用处，如 add(1,2) 编译后变为 1+2，无栈操作。

### 2. Lambda 去对象化

- 非内联高阶函数：Lambda 编译为 Function 接口的匿名类（如 (T) -> R 对应 java.util.function.Function），每次调用创建新对象。

- 内联后：Lambda 代码直接嵌入，无对象创建（尤其适合高频调用的场景，如循环内的集合操作）。

### 3. 编译器深度优化

- 内联后的代码可进行常量折叠（如 inline fun a() = 1+1 调用处直接替换为 2）、死代码消除（如条件不成立的分支直接删除）。

## 高频陷阱：内联函数的注意事项

### 1. 非局部返回风险

* 问题：内联函数中的 Lambda 可通过 return 直接跳出外层函数，导致逻辑混乱（若在协程或循环中误用）。

* 解决方案：
  
  * 用 crossinline 修饰 Lambda，禁止非局部返回（只能用 return@label 局部返回）

```kotlin
inline fun withAction(block: () -> Unit) {  
    println("Before")  
    block()  // 若block中用return，会直接跳出外层函数  
    println("After")  // 可能不执行  
}  
// 修正：用crossinline避免意外返回  
inline fun withAction(crossinline block: () -> Unit) { ... }  
```

### 2. 泛型类型擦除限制

* 问题：内联函数无法获取泛型的实际类型（如 inline fun <T> f(t:T) 中，运行时 T 被擦除）。

* 解决：配合 refied 关键字保留类型信息（需结合 inline 使用）：

```kotlin
inline fun <reified T> checkType(obj: Any) = obj is T  
// 使用：checkType<String>("abc")  // 编译时知道T是String  
```

### 3. 代码膨胀与性能平衡

* 现象：内联函数被调用 100 次，函数体代码复制 100 次，可能导致 APK 体积增大。

* 面试回答：“应遵循‘小而频’原则”：仅对代码量小（仅几行）、调用频繁（如循环内）的函数内联。对于长函数，即使含 Lambda，也可能因代码膨胀大致性能下降，此时需权衡内存（Lambda 对象）与体积（代码复制）的取舍。

## 面试加分项：内联函数在架构组件中的应用

### 1. LiveData 的 observe 方法为何不内联？

* 答：observe 接收的 Observer 是接口而非 Lambda，无需内联（接口实现本身是对象，内联无法优化）。

* 延伸：Kotlin 的 LiveData 构建器中使用内联函数优化协程上下文，避免匿名类创建。

### 2. ViewModel 的工厂函数是否需要内联？

* 答：不需要。ViewModel 工厂（如 ViewModelProvider.Factory）是接口，其 create 方法的实现是类而非 Lambda，内联无优化作用。内联主要针对 Lambda 参数的高阶函数。

## 其他问题

### 为什么 kotlin 的 let 函数要声明为内联函数？不内联会有什么问题？

let 是接收 Lambda 参数的高阶函数，若不声明 inline，Lambda 会被编译为匿名类对象（每次调用创建新实例），增加内存开销。

声明 inline 后，Lambda 代码直接嵌入调用处，避免对象创建，同时消除函数调用栈开销，提升高频调用时的性能（例如在集合遍历或 UI 链式配置中）。

### apply 和 also 有什么区别？请用代码举例说明。

1. this 指向不同
   
   * apply 中 this 是当前对象，可直接调用成员（如 text = "OK"）;
   
   * also 中 this 是函数参数，需用 it 访问对象（如 it.text = "OK"）。

2. 返回值都是返回当前对象，可以用于链式调用。

示例：

```kotlin
// apply：对象配置，直接使用this  
val button = Button(context).apply {  
    text = "Submit"  // 直接访问成员  
    setOnClickListener { ... }  
}  

// also：副作用处理，用it访问对象  
button.also {  
    log("Button created: ${it.id}")  // 记录日志  
}.setLayoutParams(...)  // 链式调用  
```

### 内联函数一定比普通函数快吗？为什么？

不一定，需结合场景：

* 优势场景：短函数 + 高频调用（如标准库工具函数），或含 Lambda 的高阶函数（避免对象创建）。

* 劣势场景：长函数内联会导致代码膨胀（函数体复制到所有调用处，增加 APK 体积）；若函数仅调用一次，内联的开销（编译时间）可能超过运行时收益。

最佳实践：只对小而频繁调用的高阶函数使用内联（如 Kotlin 标准库的设计原则）。

## 

## 参考知识

* [Kotlin 内联函数与 reified | 菜鸟教程](https://www.runoob.com/kotlin/kotlin-inline-function.html)
* [Kotlin中的inline, noinline, crossinline 和 reified内联是一种特殊的操作符, - 掘金](https://juejin.cn/post/7395850355521339426)
* [理解Kotlin中的reified关键字](https://blog.csdn.net/chuyouyinghe/article/details/133386356)
* [别再把 inline 当性能开关：Kotlin 内联、noinline、crossinline 与 reified 实战详解](https://zhuanlan.zhihu.com/p/2055588936429331423)
* [Kotlin reified 让泛型编程更优雅与安全](https://h89.cn/archives/396.html)
* [Kotlin 内联函数深度解析：从源码到实践优化](https://blog.csdn.net/2301_80329517/article/details/147618992)
