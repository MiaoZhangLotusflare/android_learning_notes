# Kotlin lateinit 与 by lazy

> 在 kotlin 中，lateinit 和 by lazy 都是用于延迟初始化。
> 
> **lateinit**
> 
> lateinit 只能用于 var（可变类型），手动选择在未来的某个时间点进行初始化。只能是非空对象（不能是基本数据类型），不保证线程安全。
> 
> lateinit 属性不能写自定义 getter/setter 方法，如果写，编译期直接报错，代码不会通过编译。
> 
> 常见场景：依赖注入（如 Dagger/Hilt）、Android 的 onCreate 生命周期回调中初始化 View。
> 
> lateinit 实现是通过对 get 和 set 方法做了一些操作，get 方法中会判断值是否为 null，如果为 null 就抛出异常。
> 
> lateinit 本质：给非空的 var 留一个可以先不赋值的字段，字段用 null 表示还没初始化。
> 
> lateinit 不能修饰基本类型。原因是因为基本类型有默认值，无法判断是否还没初始化。
> 
> 现在可以使用注入框架（Hilt）可以 @Inject lateinit var，减少自己管理对象怎么创建、写在哪。
> 
> **by lazy**
> 
> by lazy 只能用于 val（只读变量），在变量第一次被使用时自动执行初始化逻辑，并且默认是线程安全的。支持任意类型（包括基本数据类型和可空类型）。
> 
> by lazy 是一个属性委托。它包含一段初始化 lambda 表达式。只有当代码中第一次读取该字段是，该段表达式才会执行，并将结果缓存起来，后续读取直接返回保存值。
> 
> 常见场景：创建开销较大的对象（比如数据库连接、复杂的计算结果、大型数据集合），或者只有特定条件下才会用到的变量。
> 
> by：kotlin 的委托属性，就是这个变量的 get、set 都是委托给了另外一个类去操作。如果是 var 变量，必须要有 setValue 和 getValue 两个方法，val 变量不需要 setValue 方法。
> 
> lazy：一个 kotlin 的函数。实现使用 synchroinzed 来保证线程安全，如果值已经存在则返回，如果值不存在则创建并保存。
> 
> **选择**：
> 
> - 如果希望这个变量是不可变的（只读） -> 选择 by lazy
> - 如果初始化的控制权在外部（例如生命周期回调、依赖注入框架） -> 选择 lateinit var。
> - 如果需要延迟初始化基本数据类型 -> 必须选择 by lazy。
> 
> 从本质上来说，lateinit 和 by lazy 的区别是体现在内存上：
> 
> lateinit 修饰的属性，会在内存中创建，只不过没有赋值。
> 
> by lazy 修饰的属性，只有在使用的时候才会在内存中创建。
> 
> **get/set、getter/setter 与 getValue/setValue**
> 
> get/set：属性上写的访问器，写在属性声明后面，用来规定这个属性被读取、被赋值时做什么。
> 
> getter/setter：getter 就是 get，setter 就是 set。get/set 是 kotlin 源码里的写法，getter/setter 是编译后的 getxxx/setxx 方法。
> 
> getValue/setValue：委托对象上的运行符。用在 by 后面的那个对象上，属性自己的 getter/setter 仍会生成，方法体改成去调用委托。

在 kotlin 中，lateinit 和 by lazy 都是用于延迟初始化（延后变量的创建时间）的关键字，但它们的实现机制和适用场景有很大不同。

核心区别：

* lateinit 只能用于 var(可变变量)，由你手动选择在未来的某个时间点进行初始化。

* by lazy 只能用于 val（只读变量），在变量第一次被使用时自动执行初始化逻辑，并且默认是线程安全的。

| 特性       | lateinit var                               | by lazy                    |
| -------- | ------------------------------------------ | -------------------------- |
| 关键字类型    | 修饰符（Modifier）                              | 属性委托（Property Delegation）  |
| 变量类型     | 必须是 var(可变)                                | 必须是 val（只读）                |
| 支持的类型    | 只能是非空对象（不能是基本数据类型如 Int,Boolean 等，也不能是可空类型） | 任意类型（包括基本数据类型和可空类型）        |
| 初始化时机    | 需要手动赋值初始化                                  | 自动在第一次调用                   |
| 线程安全     | 不保证线程安全（需手动控制）                             | <mark>默认线程安全（采用同步锁）</mark> |
| 判断是否已初始化 | 可以通过 ::property.isInitialized 判断           | 无法直接判断是否已初始化               |

## 详细解析与代码示例

#### 1. lateinit(延迟初始化)

lateinit 告诉编译器：我现在不给这个变量编译器，但我保证在使用它之前一定会赋值。

* 常见场景：依赖注入（如 Dagger/Hilt）、Android 的 onCreate 生命周期回调中初始化 View，单元测试的 setUp 方法中。
  
  ```kotlin
  class MainActivity : AppCompatActivity() {
     // 1. 声明一个 lateinit 变量（不能是 Int 等基本类型，不能是可空类型）
     lateinit var nameTextView: TextView 
  
     override fun onCreate(savedInstanceState: Bundle?) {
         super.onCreate(savedInstanceState)
         setContentView(R.layout.activity_main)
  
         // 2. 在之后的某个生命周期手动初始化
         nameTextView = findViewById(R.id.tv_name)
  
         // 3. 使用前可以判断是否已经准备好
         if (::nameTextView.isInitialized) {
             nameTextView.text = "Hello Kotlin"
         }
     }
  }
  ```
  
  注意：如果在未赋值前就调用 nameTextView，程序会抛出 UninitializedPropertyAccessException 异常。

由于 Kotlin 有严格的语法要求变量需要声明是否可以为 null，但由于在实际的业务场景中，这个变量必须在某些时候才能做初始化操作，并且这个变量肯定不为 null，如果为 null，就是逻辑有问题了。这个时候可以使用 lateinit 来修饰这个变量。如果没有初始化就使用这个变量，那么就会抛出异常。

**laateinit 的具体实现**

```kotlin
class LateInitExample {
    lateinit var value:String
}

fun main() {
    val example = LateInitExample()
    // 如果没有赋值就使用，直接抛出异常。
    example.value = "lateinit example"
    println("${example.value}")
}
```

```kotlin
public class LateInitExample {

    private String value;

    public String getValue() {
        // 如果没有初始化过，就抛出异常
        if (value == null){
            throw new RuntimeException("lateinit property value has not been initialized");
        }
        return value;
    }

    public void setValue(String value) {
        // 这里要做非null检查
        this.value = value;
    }
}
```

我们知道使用 kotlin 的属性其实是在调用 get 和 set 方法，lateinit 关键字其实就是对 get 和 set 方法做了一些操作。

注意 lateinit 不能修饰基本类型。这是因为 lateinit 的实现是用 null 当还没赋值的标记，而 JVM 上的基本类型存不了 null。Int、Boolean、Double 这些会降成 int、boolean、double。JVM 给它们的默认值是 0、false、0.0，这些都是合法业务值，分不出还没初始化和已经赋成 0。getter 返回的也是基本类型，没有 null 可以比较。所以这套读字段，是 null 就抛异常的做法在基本类型上没有可用的标记值。

lateinit 属性不能写自定义 getter/setter 方法，如果写成自定义 getter 或 setter 时，编译期直接报错，代码不会通过编译。

#### 2. by lazy（懒加载）

by lazy 是一个属性委托。它包含一段初始化 lambda 表达式。只有当代码中第一次读取该变量时，该段表达式才会执行，并将结果缓存起来，后续读取直接返回缓存值。

* 常见场景：创建开销较大的对象（比如数据库连接、复杂的计算结果、大型数据集合），或者只有在特定条件下才会用到的变量。

```kotlin
class DatabaseManager {
    // 只有当第一次调用 database 时，才会执行大括号里的初始化代码
    val database: SQLiteDatabase by lazy {
        println("数据库开始初始化...") // 只会打印一次
        openOrCreateDatabase("my_db", null)
    }

    fun doSomething() {
        // 此时第一次使用 database，触发初始化
        database.execSQL("SELECT * FROM users") 

        // 第二次使用，直接使用缓存好的 database 对象，不会重新初始化
        database.close() 
    }
}
```

by 和 lazy 要单独拿出来看，不能当作一个整体来看。

by：这里涉及到了 Kotlin 的委托属性。

lazy：一个 kotlin 的函数。

**属性委托**

简单通俗理解就是这个变量的 get、set 都是委托给了另外一个类来去操作。

如果是 var 变量，必须要有 getValue 和 setValue 2 个方法，val 变量不需要 setValue 方法。

```kotlin
// 委托的类
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, 这里委托了 ${property.name} 属性"
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$thisRef 的 ${property.name} 属性赋值为 $value")
    }
}
```

注意别和 Kotlin 的 get 和 set 方法混淆了。

语法是：val/var <属性名>：<类型> by <表达式>

Kotlin 标准库为几种有用的委托提供了工厂方法，延迟属性 Lazy 就是其中一个。

**lazy的实现**

```kotlin
public actual fun <T> lazy(lock: Any?, initializer: () -> T): Lazy<T> = SynchronizedLazyImpl(initializer, lock)



private class SynchronizedLazyImpl<out T>(initializer: () -> T, lock: Any? = null) : Lazy<T>, Serializable {
    private var initializer: (() -> T)? = initializer
    @Volatile private var _value: Any? = UNINITIALIZED_VALUE
    // final field is required to enable safe publication of constructed instance
    private val lock = lock ?: this

    override val value: T
        get() {
            val _v1 = _value
            if (_v1 !== UNINITIALIZED_VALUE) {
                @Suppress("UNCHECKED_CAST")
                return _v1 as T
            }

            return synchronized(lock) {
                val _v2 = _value
                if (_v2 !== UNINITIALIZED_VALUE) {
                    @Suppress("UNCHECKED_CAST") (_v2 as T)
                } else {
                    val typedValue = initializer!!()
                    _value = typedValue
                    initializer = null
                    typedValue
                }
            }
        }

    override fun isInitialized(): Boolean = _value !== UNINITIALIZED_VALUE

    override fun toString(): String = if (isInitialized()) value.toString() else "Lazy value not initialized yet."

    private fun writeReplace(): Any = InitializedLazyImpl(value)
}
```

by 的 getValue 方法使用了扩展函数来做：

```kotlin
// 这里返回value，就会执行实现类的override value get了。
public inline operator fun <T> Lazy<T>.getValue(thisRef: Any?, property: KProperty<*>): T = value
```

**关于 get/set 、getter/setter 与 getValue/setValue**

get/set 和 getter/setter 是同一层，getValue/setValue 时委托对象上的另一层。

get/set：在属性上写的访问器

写在属性声明后面，用来规定这个属性被读取、被赋值时做什么。

```kotlin
var name: String = ""
    get() = field
    set(value) { field = value.trim() }
```

name 触发 get()，name="a" 触发 sey(value)。field 是这个属性的后备字段，只能在这两个访问器里用。val 只有 get()。

getter/setter：统一对访问器编译出来的方法

getter 就是 get()，setter 就是 set()。编译到 JVM 后，它们变成普通方法，Java 也是这样调用的：

```
读 name -> getName()
写 name -> setName(value)
```

所以 [get/set]说的是 Kotlin 源码里的写法，【getter/setter 方法】说的是编译后的 getName()/setName()。没有自定义访问器时，编译器也会生成这一对方法：getter 返回字段，setter 写入字段。

getValue/setValue：委托对象上的运算符。

用在 by 后面的那个对象上。属性自己的 getter/setter 仍会生成，方法体改成去调委托。

```kotlin
var name: String by TrimDelegate()

class TrimDelegate {
    private var storage = ""

    operator fun getValue(thisRef: Any?, property: KProperty<*>): String = storage

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        storage = value.trim()
    }
}
```

一次赋值的实际调用是：

```
name = "a"
  → setName("a")                  // 属性的 setter
    → delegate.setValue(..., "a")  // 委托的 setValue
```

读取则是 getName() 再调用 getValue(...)。val 的委托只需要 getValue，所以标准库的 lazy 只能配 val。

by lazy 没有后备字段存那个字符串，隐藏字段里放的是 Lazy 对象；真正的值在 Lazy 内部，第一次 getValue 时才计算。

## 3. 如何选择？

* 如果希望这个变量是不可变的（只读） -> 选择 by lazy

* 如果初始化的控制权在外部（例如生命周期回调、依赖注入框架） -> 选择 lateinit var。

* 如果你需要延迟初始化基本数据类型（如 Int、Boolean） -> 必须选择 by lazy（因为 lateinit 不支持基本类型）。

从本质上来说，lateinit 和 by lazy 的区别是体现在内存上：

lateinit 修饰的属性，会在内存中创建，只不过没有赋值

by lazy 修饰的属性，只有在使用的时候才会在内存中创建

## 参考内容

* [Kotlin的lateinit和by lazy的区别](https://blog.csdn.net/m0_37707561/article/details/128662181)
