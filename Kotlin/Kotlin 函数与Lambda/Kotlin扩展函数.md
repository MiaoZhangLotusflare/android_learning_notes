# Kotlin 扩展函数

> 扩展函数在Kotlin 中是为现有类或接口添加额外功能的好方法。这有助于保持代码的干净、可重用（1. 功能本身可复用；2.原类保持通用：类本身不被某次业务改动绑死，新能力作为独立函数按需引入）和可读性。
> 
> 扩展函数是静态解析的，即使为某个类定义了扩展函数，函数调用的解析仍是基于编译时引用的类型，而不是运行时。
> 
> 扩展函数无法覆盖成员函数：不能用扩展函数修改类或接口中的现有函数。
> 
> 扩展功能的优势：
> 
> - 清洁守则：扩展函数允许以非侵入式的方式添加使用函数，使代码更具模块化和可维护性。
> - 提升可读性：可以用针对你的应用或领域的也定行为来增强现有类，而不会让它们的原始代码变得杂乱。
> - 提升可重复使用性：扩展函数允许创建可重复使用的函数，这些函数可以应用于同类型的各种对象。
> 
> 顶层扩展函数定义在类的外面，不属于任何类，能在任何类里使用它。
> 
> 把要扩展的类或者接口的名称，放在即将添加的函数前面。这个类或者接口就被称为接收者类型；用来调用这个扩展函数的对象，叫做接收者对象。（String.lastChar() "a".lastChar() String 是接收者类型，"a"是接收者对象）
> 
> **接收者类型就只能类或者接口吗？** 不完全是，接收者写的是一个类型，类和接口只是最常见的两种，还有可空类型、泛型类型、类型参数本身、函数类型、枚举、对象声明（对象声明、嵌套对象、伴生对象）、抽象类等都可以作为接收者类型。
> 
> **扩展函数能访问类私有的或者受保护的成员吗？** 不能，扩展函数只是看起来像类的成员函数，实际上是静态函数，它把调用对象作为静态函数的第一个参数。顶层扩展函数可以访问被扩展类的公开的属性与函数（同模块里还有 internal），不能访问私有与protected 属性与函数；成员扩展函数可以访问外层类的公开、私有、protected 、internal 的属性与函数，但是只能访问被扩展类的公开的属性与函数（同模块里还有 internal）。
> 
> **从 Java 中如何调用扩展函数？** StringExtensions.kt 对应的 Java 类是 StringExtensionsKt，可以通过这个类名调用内部的静态方法，调用者是作为静态函数的第一个参数传入的。
> 
> **扩展函数可以重写吗？** 普通的扩展函数不能重写。它不是类的成员，调用哪个版本在编译器就按接收者的声明类型定了，和运行时的实际类型无关。也就是说，调用哪个扩展函数，取决于接收者的静态类型，而不是接收者的运行时类型。
> 
> 类里声明的成员扩展可以重写。被重写的是外层类的那个成员，多台看的是外层对象，接收者仍然按声明类型静态选择。类里与成员扩展函数同名的成员函数优先级更高。类里已经有这个函数时，扩展函数不会盖过它，也不能用扩展实现接口方法。
> 
> **类的扩展函数和成员函数签名相同，谁会被优先使用？** 成员函数会被优先使用。这就说明：给类添加一个和扩展函数同样签名的成员函数，那么对应类定义的消费者（依赖并调用这个类的代码）将会重新编译代码，开始指向新的成员函数。
> 
> 如果扩展函数只是和成员函数的函数名字相同，参数列表不同，这种情况下，二者是不会干扰的。
> 
> **扩展函数的接收者的本质是什么？** 扩展函数的接收者是表明那个接收者接收了这个扩展函数，就只能由那个类型的对象才调用这个函数。接收者只拥有扩展函数的调用权，而不是扩展函数的所有权。接收者接收了扩展函数的调用权而已，是扩展函数的设计者把这个扩展函数的调用权给了接收者。
> 
> **如何引用一个扩展函数？** 使用::（双冒号）运算符来转换，必须在双冒号运算符前加上接收者类型。val greeting = String?::greeting
> 
> **成员扩展函数**：在另一个类中为另外一个类声明扩展函数，即是外部类的成员函数，又是前缀类的扩展函数。由于是成员函数，所以只能在它所属的类里面被调用，到了外面就不能用了。
> 
> 成员扩展函数有多个隐式的接收者（即不需要限定符就可以访问其成员的对象）：扩展函数声明所在类的实例被称为分发接收者（dispatcher receiver），扩展函数的接收者类型的实例被称为扩展接收者（extension receiver）。
> 
> **当分发接收者和扩展接收者的成员之间出现命名冲突时，会优先使用哪个成员？** 在扩展函数中调用的方法，分发接收者和扩展接收者都有相同的方法时，优先使用扩展接收者的成员。
> 
> **当分发接收者和扩展接收者的成员之间出现命名冲突时，如何引用到分发接收者的成员？** 调用方法前加上 this@方法名即可。
> 
> **成员扩展函数可以重写吗？** 成员扩展函数可以声明为 open，并可以在子类中重写。但对于扩展接收者来说，仍然是静态解析的：哪个接收者对象来调用扩展函数，实际上就会调用以那个接收者为接收者的扩展函数。
> 
> **成员扩展函数与顶层扩展函数的区别是什么？**
> 
> | 比较项          | 顶层扩展函数    | 成员扩展函数            |
> | ------------ | --------- | ----------------- |
> | 编译后的 Java 函数 | 静态函数      | 成员函数              |
> | 作用域          | 可以在任何地方调用 | 只可以在声明它的类中调用      |
> | 函数引用         | 可以引用      | 不可以引用             |
> | 可以重写？        | 不可以       | 可以                |
> | 接收者          | 存在一个扩展接收者 | 存在一个分发接收者和一个扩展接收者 |
> 
> 成员扩展函数不能被引用，顶层扩展函数用类名::函数名引用，成员扩展函数使用类名还是接收者类名是有歧义的，所以 Kotlin 就干脆不需引用即是成员函数还是扩展函数的函数了。
> 
> （String） -> Char 称为普通函数类型，String.() -> Char 称为扩展函数类型。
> 
> 跟普通函数的引用一样，扩展函数的引用也可以被调用，直接调用或者用 invoke() 都可以，不过要记得第一个参数是接收者。(String::method1)("text",1) (String::method1.invoke("text",1)) "text".method1(1) 这三个是等价的。
> 
> 扩展函数的应用也可以赋值给变量。val a:String.(Int) -> Unit = String::method1
> 
> 没有接收者的扩展函数的引用可以与有接收者的扩展函数的引用进行互相赋值。
> 
> ```
> fun String.method1(i: Int) {
> 
> }
> 
> val a: String.(Int) -> Unit = String::method1 // 有接收者的引用
> val b: (String, Int) -> Unit = String::method1 // 无接收者的引用
> val c: String.(Int) -> Unit = b // 无接收者的扩展函数被赋值给有接收者的变量
> val d: (String, Int) -> Unit = a // 有接收者的扩展函数被赋值给无接收者的变量
> 
> "text".method(1)//可以调用
> "text".b(1) // 不允许调用，报错
> ```
> 
> 但是普通函数的引用，没有接收者的函数也可以赋值给有接收者的变量，但是有接收者的
> 
> ```
> fun method3(s: String, i: Int) {
> 
> }
> 
> ...
> 
> val e: (String, Int) -> Unit = ::method3
> val f: String.(Int) -> Unit = ::method3 // 这种写法也行哦
> 
> "text".method3(1)//不允许调用，报错
> "text".f(1) // 允许调用，报错
> ```
> 
> 扩展属性：给现有类增加一个访问的属性，同时不修改这个类的源码。没有字段，也不能写初始值，每次读取都要执行 get()，结果不会保存在对象里。可写的扩展属性用 var，添加 set 方法更改对象里原来就有的数据。
> 
> 扩展属性原理：顶层扩展属性会变成文件生成类里的静态方法，接收者作为第一个参数。解析时机也和扩展函数一样，编译器看变量的声明类型，在编译期决定调用哪个扩展属性，不看运行时的实际类型。

扩展函数：Kotlin 中有一个强大的功能，允许你在不修改代码的情况下为现有类添加新功能。这些函数定义在它们扩展的类之外，但它们的行为就像是该类的成员一样。这有助于保持代码的干净、可重用和可读性。

可重用性：

1. 功能本身可复用：一个扩展函数服务该类型的所有实例。

2. 原类保持通用：类本身不被某次业务改动绑死，新能力作为独立函数按需引入，别的模块或项目只要倒入就能再用。

主要特色：

1. 无继承权：扩展函数允许你扩展类，但实际上不继承它。

2. 可读性：它们看起来像普通的成员函数，使代码更具表现力。

3. 没有实际的改装：扩展函数不修改类本身；它们只为某些对象提供额外功能。

重要说明：

* 扩展函数是静态解析的：这意味着即使你为某个类定义了扩展函数，函数调用的解析仍是基于编译时引用的类型，而不是运行时。

* 扩展函数无法覆盖成员函数：不能用扩展函数修改类或接口中的现有函数。

即使定义了扩展功能，它也不会覆盖原始函数。实际上，它只是一个附加函数。

```kotlin
open class Animal {
    open fun sound() {
        println("Some generic animal sound")
    }
}

class Dog : Animal() {
    override fun sound() {
        println("Woof!")
    }
}

// Extension function
fun Dog.sound() {
    println("This is an extension function sound")
}

fun main() {
    val dog = Dog()
    dog.sound() // Calls the member function sound() from Dog
}        
```

扩展功能的优势：

* 清洁守则：扩展函数允许你以非侵入式的方式添加使用函数，使代码更具模块化和可维护性。

* 提升可读性：可以用针对你的应用或领域的特定行为来增强现有类，而不会让它们的原始代码变得杂乱。

* 提升可重复使用性：扩展函数允许你创建可重复使用的函数，这些函数可以应用于同类型的各种对象。

总之，扩展函数在 Kotlin 中，是为现有类或接口添加额外功能的好方法，提升代码的模块化性和清晰度。它们帮助保持代码库的干净，而无需修改你扩展的类。

## （顶层）扩展函数

把顶层扩展函数简称为扩展函数。它不属于任何类，能在任何类里使用它。

在 kotlin 里，当你给声明的函数名左边加一个类名的时候，表示你要给这个函数先定一个 Receiver -- 直译的话叫接收者，其实也就是哪个类的对象可以调用这个函数。虽然说是个 Top-level Function，不属于任何类 -- 确切来说，不是任何一个类的成员函数--但是要限制只有通过某个类的对象才能调用你。这就是扩展函数的本质。

### 声明（顶层）扩展函数

扩展函数是定义在类的外面

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun String.lastChar(): Char {
    return this.get(this.length - 1)
}
```

把要扩展的类或者接口的名称，放在即将添加的函数前面。这个类或者接口就被称为接收者类型；用来调用这个扩展函数的那个对象，叫做接收者对象。

![](https://i-blog.csdnimg.cn/blog_migrate/9ea8f122334497953e235e3bef8fb2a8.png)

使用：

```kotlin
fun main() {
    println("Kotlin".lastChar()) // 打印：n
}
```

String 是接收者类型，“Kotlin”是接收者对象。

从调用上看，调用 lastChar() 和调用 String 类的普通成员函数的方式是一模一样的，都是通过对象.方法的方式调用的。

另外，这里的扩展函数，可以像普通的成员函数一样，省略掉 this：

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun String.lastChar(): Char {
    return get(length - 1)
}
```

### 对扩展函数的深入理解

#### 接收者类型就只是类或者接口吗？

不完全是。接收者写的是一个类型，类和接口只是最常见的两种。

常见还能写成这些：

* 可空类型：fun String?.orEmpty()，接收者是 String?，调用时 null 也能进这个函数。

* 泛型类型：fun <T> MutableList<T>.swap(i:Int, j:Int)，接收者是 MutableList<T>.

* 类型参数本身：fun <T>T.applyIf(condition: Boolean, block:T.() -> Unit)。这里没有绑定某一个具体类，任何类型都能当接收者。

* 函数类型：可以给 (Int) -> String 这种类型写扩展。

* 枚举、对象声明（<mark>对象声明、嵌套对象、伴生对象</mark>）、抽象类：它们在类型系统里仍是类，写法和普通类一样。

在 Kotlin 中，类型和类是不一样的。

对于一个非泛型类，对应着非空类型和可空类型，如 String 类：

```kotlin
var x: String // 非空类型
var y: String? // 可空类型
```

对于一个泛型类，会存在无限数量的类型，如 List 类：

```kotlin
var stringList: List<String>
var nullStringList: List<String?>
var stringNullList: List<String>?
var stringListList: List<List<String>>
```

现在对 String? 定义一个扩展函数。现在对 String? 定义一个扩展函数：

```kotlin
fun String?.firstChar(): Char? {
    return this?.get(0)
}
```

这里需要注意的是，在 Java 中，this 永远是非空的；而在 Kotlin 中，this 是可以为空的：在可空类型的扩展函数中，this 就可以是 null。因此，在 firstChar 内部，通过 this 来直接调用 get 方法，就会编译报错，可以使用安全调用(?.)来解决。当然了，这种情况下，this 是不可以省略的。

调用：

```kotlin
fun main() {
    println("Kotlin".firstChar()) // 打印：K
    println(null.firstChar()) // 打印：null
}
```

可以对 List<Int> 类型，List<Double> 类型定义求所有元素之和的扩展函数 sum：

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun List<Int>.sum(): Int {
    var sum: Int = 0
    for (element in this) {
        sum += element
    }
    return sum
}

fun List<Double>.sum(): Double {
    var sum: Double = 0.0
    for (element in this) {
        sum += element
    }
    return sum
}
```

调用：

```kotlin
fun main() {
    println(listOf(1, 2, 3).sum()) // 打印：6
    println(listOf(1.1, 2.2, 3.3).sum()) // 打印：6.6
}
```

可以对 List<T> 定义一个把所有元素以逗号拼接为字符串的扩展函数，这也是一个泛型扩展函数：

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun <T> List<T>.joinToString(): String {
    val result = StringBuilder()
    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(", ")
        result.append(element)
    }
    return result.toString()
}
```

调用：

```kotlin
fun main() {
    println(listOf(1, 2, 3).joinToString()) // 1, 2, 3
    println(listOf("a", "b", "c").joinToString()) // a, b, c
}
```

**扩展函数能访问类私有的或者受保护的成员吗？**

扩展函数虽然可以像类的成员函数一样的方式调用，但是扩展函数并不允许打破类的封装性。

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun String.lastChar(): Char {
    return this.get(this.length - 1)
}
```

对应的 Java 代码：

```kotlin
public final class StringExtensionsKt {
   public static final char lastChar(@NotNull String $this$lastChar) {
      Intrinsics.checkNotNullParameter($this$lastChar, "$this$lastChar");
      return $this$lastChar.charAt($this$lastChar.length() - 1);
   }
}
```

可以看到，扩展函数只是看起来像类的成员函数（在调用方式上），实质上是静态函数，它把调用对象作为了静态函数的第一个参数。

**从 Java 中如何调用扩展函数**

```java
public class JavaTest {
    public static void main(String[] args) {
        System.out.println(StringExtensionsKt.lastChar("Java"));
    }
}
```

可以看到，StringExtensions.kt 这个 kt 文件名，对应的 Java 类是 StringExtensionsKt，可以通过这个类名调用内部的静态函数 lastChar，调用者是作为静态函数的第一个参数传入的。

**扩展函数可以重写吗？**

普通的扩展函数是不可以重写的。它不是类的成员，调用哪个版本在编译器就按接收者的声明类型定了，和运行时的实际类型无关。

定义两个类，View 类及其子类 Button：

```kotlin
open class View
class Button: View()
```

分别给 View 类型和 Button 类型定义扩展函数 showOff：

```kotlin
fun View.showOff() = println("View extension showOff")
fun Button.showOff() = println("Button extension showOff")
```

调用：

```kotlin
fun main() {
    val buttonView: View = Button()
    buttonView.showOff()
}
```

```
View extension showOff
```

可以看到，虽然 View 类型和 Button 类型都定义扩展函数 showOff，但是打印是取决于变量的静态类型，而不是变量的运行时类型，比如：val buttonView: View = Button() 这个变量的静态类型是 View 类型，运行时类型是 Button 类型，调用的是 View 的扩展函数：fun View.showOff() = println("View extension showOff")。也就是说，调用哪个扩展函数，取决于接收者的静态类型，而不是接收者的运行时类型。

这是为什么呢？扩展函数实质上是静态函数，看一下调用对应的 Java 字节码：

```java
public final class TestKt {
   public static final void main() {
      View buttonView = (View)(new Button());
      ViewExtenionsKt.showOff(buttonView);
   }
}
```

扩展函数对应的 Java 代码如下：

```java
public final class ViewExtenionsKt {
   public static final void showOff(@NotNull View $this$showOff) {
      Intrinsics.checkNotNullParameter($this$showOff, "$this$showOff");
      String var1 = "View extension showOff";
      boolean var2 = false;
      System.out.println(var1);
   }

   public static final void showOff(@NotNull Button $this$showOff) {
      Intrinsics.checkNotNullParameter($this$showOff, "$this$showOff");
      String var1 = "Button extension showOff";
      boolean var2 = false;
      System.out.println(var1);
   }
}
```

可以看到，一个 View 类型的 buttonView 变量，实际上会作为静态函数的参数传入，会匹配到 public static final void showOff(@NotNull View \$this\$showOff) 这个静态函数，所以打印的是 “View extension showOff”。

类里声明的成员扩展可以重写。被重写的是外层类的那个成员，多态看的是外层对象，接收者仍然按声明类型静态选择。类里与成员扩展函数同名的成员函数优先级更高。类里已经有这个函数时，扩展函数不会盖过它，也不能用扩展去实现接口方法。

**类的扩展函数和成员函数签名相同，谁会被优先使用？**

在 View 类及其子类 Button 增加和扩展函数同签名的成员函数：

```kotlin
open class View {
    open fun showOff() {
        println("View member showOff" )
    }
}
class Button: View() {
    override fun showOff() {
        println("Button member showOff" )
    }
}
```

调用：

```kotlin
fun main() {
    val buttonView: View = Button()
    buttonView.showOff()
}
```

打印日志：

```
Button member showOff
```

成员函数会被优先使用。这就说明：给类添加一个和扩展函数同样签名的成员函数，那么对应类定义的消费者将会重新编译代码，开始指向新的成员函数。实际上，这种情况下，扩展函数是永远不会再被调用的。

如果扩展函数只是和成员函数的函数名字相同，参数列表不同，这种情况下，二者是不会干扰的。

**扩展函数的接收者的本质是什么？**

扩展函数的接收者，是表明哪个接收者接收了这个扩展函数，就只能由那个类型的对象才调用这个函数。

实际上，扩展函数是一个顶层函数，它不属于任何类，当然也不属于接收者。

接收者只拥有扩展函数的调用权，而不是扩展函数的所有者。

接收者接收了什么呢？接收者接收了扩展函数的调用权而已，是扩展函数的设计者把这个扩展函数的调用权给了接收者。

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun String.lastChar(): Char {
    return this.get(this.length - 1)
}
```

String 这个接收者类型，就限定了 lastChar() 这个扩展函数，只能通过 String 类型的对象来调用，而不可以通过 String? 类型或者 Int 类型等其他类型来调用。但是，lastChar 扩展函数并不属于 String。

**如何引用一个扩展函数？**

在 Kotlin 中，和 Java8 一样，只有把函数转换成一个值，才可以传递它。也就是说，函数并不是一个值。

如何转换：使用::（双冒号）运算符来转换。

对于一个顶层函数 greeting 来说：

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun greetings(message: String) {
    println("Hello, $message")
}
```

使用::（双冒号）运算符来转换：

```kotlin
fun main() {
   val greeting = ::greetings
}
```

这里变量 greeting 是使用类型推断的，那么显式的类型是什么呢？

在 AS 中，把鼠标放在 greeting 变量上，按下 Alt+Enter，在弹出菜单中选择 Specify type explicitly，来显式地指定类型：

![](https://i-blog.csdnimg.cn/blog_migrate/af6b8449fead634d5460c0b8ab266192.png)

接着弹出一个类型列表供选择：

![](https://i-blog.csdnimg.cn/blog_migrate/28ed4da07edcb92e47baf3c691a6dc9e.png)

选择 (message: String) -> Unit 这个类型，因为 Any 类型在这里不能太宽了，而其余的类型都是基于 Kotlin 反射的。

```kotlin
fun main() {
   val greeting: (message: String) -> Unit = ::greetings
}
```

(message: String) -> Unit 是一个函数类型，括号中的是函数参数类型，紧接着是一个箭头，剪头后面是函数的返回类型。

![](https://i-blog.csdnimg.cn/blog_migrate/526b1a2306b593816f06d77739d4090b.png)

函数类型的参数名是可以省略的：

```kotlin
fun main() {
   val greeting: (String) -> Unit = ::greetings
}
```

函数的引用方式可以使用的地方：顶层函数，局部函数，成员函数，接口方法和 open 的成员函数，扩展函数，构造函数，对象声明和伴生对象里的函数，没有 reified、也没有要内敛的函数参数的 inline 函数可以引用。

不能用 :: 引用：

* <mark>lambda</mark> 和匿名函数没有名字。它们本身已经是函数值，直接传递即可。变量上的 ::变量名 引用的是这个变量，不是变量里的那个函数。

* 带 <mark>reified</mark> 类型参数的 inline 函数。它的类型参数要在每个调用点替换掉，没有一份可以单独拿来调用的函数。

* 函数参数会被内联的 inline 函数。这种参数没有标 noinline，函数题必须内联到调用处，不能先做成引用再调用。参数标了 noinline 之后可以引用。

* 当前位置不可见的函数，比如别处的 private 函数。

* 几个重载无法区分时，单独写 ::prese 会有歧义。写上目标类型后可以选择其中一个 : val parseText: (String) -> String = ::parse。

说了顶层函数的引用方式，那么扩展函数如何引用呢？

定义两个扩展函数：

```kotlin
fun String.greetings2() {
    println("Hello, $this")
}
fun String?.greeting3() {
    println("Hello, $this")
}
```

直接使用双冒号运算符来转换是不可以的，必须在双冒号运算符前加上接收者类型。

```kotlin
fun main() {
    val greeting2 = String::greetings2
    val greeting3 = String?::greeting3
}
```

需要特别说明双冒号运算符前面加的是接收者类型，而不是接收者类。对于 greeting3 的引用，写为 String::greeting3 也是正确的，但是这样就把 greeting3 的调用者类型收窄了。

对于 String?::greeting3 这样的函数引用，允许传入非空类型：

```kotlin
val greeting3 = String::greeting3
greeting3("Kotlin")
greeting3(null) // 编译报错：Null can not be a value of a non-null type String
```

对于 String::greeting3 这样的函数引用，只允许传入非空类型：

```kotlin
val greeting3 = String::greeting3
greeting3("Kotlin")
greeting3(null) // 编译报错：Null can not be a value of a non-null type String
```

收窄是正确的，但是放宽是不可以的：

```kotlin
// 编译报错：Only safe (?.) or non-null asserted (!!.) calls are 
// allowed on a nullable receiver of type String?
val greeting2: Any = String?::greetings2 
```

通过函数引用，可以收窄调用者类型，在某些情况下，或许是有作用的。

```kotlin
package com.kotlin.lib._1_topextensionfunction
/*
编译报错：
Platform declaration clash: The following declarations have the same JVM signature (greetings(Ljava/lang/String;)V): 
    public fun greetings(message: String): Unit defined in com.kotlin.lib._1_topextensionfunction in file Util.kt
    public fun String.greetings(): Unit defined in com.kotlin.lib._1_topextensionfunction in file Util.kt
*/
fun greetings(message: String) { // 函数声明下有红色波浪线
    println("Hello, $message")
}

fun String.greetings() { // 函数声明下有红色波浪线
    println("Hello, $this")
}
```

上述代码会报错。在 JVM 看来，fun greetings(message: String) 和 fun String.greetings() 的签名是一样的，而一样的签名是不允许的，所以报错了。对应的 Java 字节码：

```kotlin
public final class UtilKt {
   public static final void greetings(@NotNull String $this$greetings) {
      Intrinsics.checkNotNullParameter($this$greetings, "message");
      String var1 = "Hello, " + $this$greetings;
      boolean var2 = false;
      System.out.println(var1);
   }

   public static final void greetings(@NotNull String $this$greetings) {
      Intrinsics.checkNotNullParameter($this$greetings, "message");
      String var1 = "Hello, " + $this$greetings;
      boolean var2 = false;
      System.out.println(var1);
   }
}
```

是一样的。

greeting2 变量的函数类型和 greeting3 变量的函数类型如下：

```kotlin
val greeting2: (String) -> Unit = String::greetings2
val greeting3: (String?) -> Unit = String?::greeting3
```

如何使用扩展函数的引用：

```kotlin
fun main() {
    val greeting2: (String) -> Unit = String::greetings2
    greeting2.invoke("Kotlin") // 打印：Hello, Kotlin
    greeting2("Android") // 打印：Hello, Android
}
```

可以看到，通过使用函数引用，和使用原函数一样，都可以正常调用。但是，它们的调用方式却有些不同：

```kotlin
"Jetpack".greetings2() // 打印：Hello, Jetpack
```

## 成员扩展函数

除了写成 Top Level 的，扩展函数也可以写在某个类里。

即是外部类的成员函数，又是前缀类的扩展函数。

这种即是成员函数、又是扩展函数的函数，它们的用法跟 Top Level 的扩展函数一样，只是由于它同时还是成员函数，所以只能在它所属的类里面被调用，到了外面就不能用了。

通常可以把这个【指向和函数等价的对象的引用】称作是【指向这个函数的引用】。

### 声明成员扩展函数

除了声明顶层扩展函数，Kotlin 还允许在类中声明扩展函数，这样的扩展函数即是它所在类的成员，又是某些其他类型的扩展。这样的函数就叫做成员扩展函数。

成员扩展函数就是在一个类中为另外一个类声明扩展函数。在这样一个扩展中，有多个隐式的接收者（即不需要限定符就可以访问其成员的对象）：扩展函数声明所在类的实例被称为分发接收者（dispatcher receiver），扩展函数的接收者类型的实例被称为扩展接收者（extension receiver）。

一个成员扩展函数的例子：

```kotlin
class PhoneNumber(val number: String) {
    fun isValid(): Boolean {
        return number.length == 11 && number.all { it.isDigit() }
    }
}

class PhoneBook {
    fun verify(phoneNumber: PhoneNumber): Boolean {
        return phoneNumber.check()
    }
    // check 是一个成员扩展函数
    fun PhoneNumber.check(): Boolean {
        printPhoneNumber(this.number) // printPhoneNumber 是由分发接收者，即 PhoneBook 对象来调用的。
        return isValid() // isValid() 是由扩展接收者，即 PhoneNumber 对象来调用的。
    }

    private fun printPhoneNumber(number: String) {
        println("PhoneBook: $number")
    }
}

fun main() {
    println(PhoneBook().verify(PhoneNumber("13912345678")))
}
```

打印：

```kotlin
PhoneBook: 13912345678
true
```

### 对成员扩展函数的深入理解

**当分发接收者和扩展接收者的成员之间出现命名冲突时，会优先使用哪个成员？**

在 phoneNumber 类中也声明一个和 PhoneBook 的 printPhoneNumber 一样的方法：

```kotlin
class PhoneNumber(val number: String) {
    fun isValid(): Boolean {
        return number.length == 11 && number.all { it.isDigit() }
    }

    fun printPhoneNumber(number: String) {
        println("PhoneNumber: $number")
    }
}
```

这时查看 PhoneBook 中的 printPhoneNumber 方法，已经变成灰色的，说明不再被调用了；而在 PhoneNumber 中增加的 printPhoneNumber 方法，已经变成高亮的，说明被调用了。

![](https://i-blog.csdnimg.cn/blog_migrate/a9a8cb6235c261c753ddb7fc05bb1676.png)

运行程序，查看日志：

```
PhoneNumber: 13912345678
true
```

 可以知道，PhoneNumber 中增加的 printPhoneNumber 方法确实被调用了。

所以，当分发接收者和扩展接收者的成员之间出现命名冲突时，则会优先使用扩展接收者的成员。

**当分发接收者和扩展接收者的成员之间出现命名冲突时，如何引用到分发接收者的成员？**

那么怎样让 phoneBook 中的 printPhoneNumber 方法被调用，而不调用 PhoneNumber 中的 printPhoneNumber 方法呢？

在调用 printPhoneNumber 时，前面加上 this@PhoneBook：

```kotlin
fun PhoneNumber.check(): Boolean {
    this@PhoneBook.printPhoneNumber(this.number)
    return isValid()
}
```

这是查看 PhoneBook 中的 printPhoneNumber 方法，已经变成高亮的，说明呗调用了；而在 PhoneNumber 中增加的 printPhoneNumber 方法，已经变成灰色的，说明不再被调用了。

![](https://i-blog.csdnimg.cn/blog_migrate/66c2fbbd1bcdb43ba02ec2d991a96663.png)

运行：

```
PhoneBook: 13912345678
true
```

可以知道，PhoneBook 中的 printPhoneNumber 方法确实被调用了。

**成员扩展函数可以重写吗？**

成员扩展函数可以声明为 open，并可以在子类中重写，这就是说对于分发接收者来说，当是由子类来分发时，就会调用子类重写的成员扩展函数；但是对于扩展接收者来说，仍然是静态解析的：哪个接收者对象来调用扩展函数，实际上就会调用以那个接收者为接收者的扩展函数。

```kotlin
open class Base { }

class Derived : Base() { }

open class BaseCaller {
    open fun Base.printFunctionInfo() {
        println("Base extension function in BaseCaller")
    }

    open fun Derived.printFunctionInfo() {
        println("Derived extension function in BaseCaller")
    }

    fun call(b: Base) {
        b.printFunctionInfo()   // call the extension function
    }
}

class DerivedCaller: BaseCaller() {
    override fun Base.printFunctionInfo() {
        println("Base extension function in DerivedCaller")
    }

    override fun Derived.printFunctionInfo() {
        println("Derived extension function in DerivedCaller")
    }
}

fun main() {
    BaseCaller().call(Base())
    DerivedCaller().call(Base())  
    DerivedCaller().call(Derived()) 
}
```

打印：

```kotlin
Base extension function in BaseCaller
Base extension function in DerivedCaller
Base extension function in DerivedCaller
```

DerivedCaller().call(Base()) 这行，是子类分发接收者 DerivedCaller 对象来分发，所以回调用 DerivedCaller 重写的成员扩展函数；扩展接收者是 Base 类型的，所以回调用 DerivedCaller 的 override fun Base.printFunctionInfo() 函数，打印 "Base extension function in DerivedCaller"。

DerivedCaller().call(Derived()) 这行，是子类分发接收者 DerivedCaller 对象来分发，所以回调用 DerivedCaller 重写的成员扩展函数；虽然扩展接收者实际上是一个 Derived 对象，但是它的静态类型是 Base 类型的，所以还是会回调用 DerivedCaller 的 override fun Base.printFunctionInfo() 函数，打印 "Base extension function in DerivedCaller"。

看一下反编译后的 Java 代码：

```java
package com.kotlin.lib._2_memberextensionfunction.decompiled;

class Base {
}
final class Derived extends Base {
}

class BaseCaller {
   public void printFunctionInfo(@NotNull Base base) {
      System.out.println("Base extension function in BaseCaller");
   }

   public void printFunctionInfo(@NotNull Derived derived) {
      System.out.println("Derived extension function in BaseCaller");
   }

   public final void call(@NotNull Base b) {
      this.printFunctionInfo(b);
   }
}

final class DerivedCaller extends BaseCaller {
   public void printFunctionInfo(@NotNull Base base) {
      System.out.println("Base extension function in DerivedCaller");
   }

   public void printFunctionInfo(@NotNull Derived derived) {
      System.out.println("Derived extension function in DerivedCaller");
   }
}

public final class CallerKt {
   public static void main(String[] var0) {
      new BaseCaller().call(new Base());
      new DerivedCaller().call(new Base());
      new DerivedCaller().call(new Derived());
   }
}
```

Java 代码是把 Kotlin 代码成员扩展函数的接收者，作为第一个参数传入了。

**成员扩展函数与顶层扩展函数的区别是什么？**

| 比较项          | 顶层扩展函数    | 成员扩展函数            |
| ------------ | --------- | ----------------- |
| 编译后的 Java 函数 | 静态函数      | 成员函数              |
| 作用域          | 可以在任何地方调用 | 只可以在声明它的类中调用      |
| 函数引用         | 可以引用      | 不可以引用             |
| 可以重写？        | 不可以       | 可以                |
| 接收者          | 存在一个扩展接收者 | 存在一个分发接收者和一个扩展接收者 |

**成员扩展函数不能被引用**：顶层扩展函数用类名::函数名引用，成员扩展函数使用类名还是接收者类名是有歧义的，所以 Kotlin 就干脆不需引用即是成员函数还是扩展函数的函数了。

## 扩展函数类型

已接收一个 String 类型参数和一个函数类型参数的高阶函数为例：

```kotlin
fun printChar1(str: String, block: (String) -> Char) {
    println(block(str))
}
```

这个函数的作用是：把字符串作为参数传递给一个函数类型对象，返回一个字符，并打印这个字符。

可以传入一个扩展函数的引用：

```kotlin
package com.kotlin.lib._1_topextensionfunction

fun String.lastChar(): Char {
    return this.get(this.length - 1)
}
```

调用：

```kotlin
fun main() {
    val lastChar: (String) -> Char = String::lastChar
    printChar1("Kotlin") {
        lastChar(it)
    }
}
```

打印：

```
n
```

这里获取扩展函数的引用是获取了函数类型。

实际上，可以将函数类型转换成扩展函数类型，或者说可以将 lambda 转换成带接收者的 lamdba。

AS 是支持这种转换的，把光标放在 printChar1 函数的 (String) 位置上，按下 Alt+Enter 快捷键，会弹出一个菜单：

![](https://i-blog.csdnimg.cn/blog_migrate/62ee3d2884c05c95857ce5fea2887fa2.png)

选择第二项：Convert '(String) -> Char' to 'String.() -> Char '，得到 printChar1 为：

```kotlin
fun printChar1(str: String, block: String.() -> Char) {
    println(str.block())
}
```

为了比较这两个类型，把转换后的写成 printChar2，printChar1 仍保持转换前的形式：

```kotlin
fun printChar2(str: String, block: String.() -> Char) {
    println(str.block())
}
```

调用：

```kotlin
fun main() {
    val lastChar: (String) -> Char = String::lastChar
    printChar1("Kotlin") {
        lastChar(it)
    }

    printChar2("Android") {
        // 可以看到，这里就可以和扩展函数一样的方式调用了。
        this.lastChar()
    }
}
```

打印：

```kotlin
n
d
```

这样的函数类型，如 （String）-> Char，称为普通函数类型；把这样的函数类型，如 String.() -> Char，称为扩展函数类型。

普通函数类型是如何转换成扩展函数类型的呢？

将普通函数类型参数列表中的第一个参数移到括号外边，并用一个 .(点) 与其他的参数分隔开，这样就得到了对应的扩展函数类型。

```kotlin
// 使用普通函数类型
val greeting2: (String) -> Unit = String::greetings2
greeting2.invoke("Kotlin")
greeting2("Android") // 以普通函数的方式来调用
// 使用扩展函数类型
val greeting22: String.() -> Unit = String::greetings2
"Kotlin".greeting22() // 以扩展函数的方式来调用
```

## 指向扩展函数的引用

普通函数可以被指向，扩展函数同样也是可以被指向的：

```kotlin
fun String.method1(i: Int) {

}
...

String::method1
```

不过如果这个扩展函数不是 Top-Level 的，也就是说如果它是某个类的成员函数，它就不能被引用了：

```kotlin
class Extensions {

  fun String.method1(i: Int) {
    ...
  }

  ...

  String::method1 // 报错
}
```

为什么呢？一个成员函数通过类名加双冒号加函数名进行引用，扩展函数也是类名加双冒号加函数名引用，这不过这次是 Receiver 的类名。成员扩展函数还是用类名加双冒号加函数名，但是用谁的类名？是这个函数所属的类名，还是它的 Receiver 的类名，这时有歧义的，所以 Kotlin 就干脆不许引用即是成员函数还是扩展函数的函数了。

同样，跟普通函数的引用一样，扩展函数的引用也可以被调用，直接调用或者用 invoke() 都可以，不过要记得把 Receiver 也就是接收者或者说调用者填成第一个参数：

```kotlin
(String::method1)("rengwuxian", 1)
String::method1.invoke("rengwuxian", 1)

// 以上两句都等价于：
"rengwuxian".method1(1)
```

## 把扩展函数的引用赋值给变量

同样的，扩展函数的引用也可以赋值给变量：

```kotlin
val a: String.(Int) -> Unit = String::method1
```

然后再拿这个变量去调用，或者再次传递给别的变量，都是可以的：

```kotlin
"rengwuxian".a(1)
a("rengwuxian", 1)
a.invoke("rengwuxian", 1)
```

## 有无 Receiver 的变量的互换

当拿着一个函数的引用（引用未绑定调用者）去调用的时候，不管是一个普通的成员函数还是扩展函数，都需要把 Receiver 也就是接收者或者调用者作为第一个参数填进去。

```kotlin
(String::method1)("rengwuxian", 1)  // 等价于 "rengwuxian".method1(1)
(Int::toFloat)(1) // 等价于 1.toFloat()
```

因为拿到的是函数引用而不是调用者的对象，所以没办法在左边写上调用者。所以 Kotlin 要想支持让拿着函数的应用去调用，就必须给个途径让提供调用者。Kotlin 给的方案就是：在这种调用方式下，增加一个函数参数，让把第一个参数的位置填上调用者。这样，就可以用函数的引用来调用成员函数和扩展函数了。

既然有 Receiver 的函数可以以无 Receiver 的方式来调用，那他可以赋值给无 Receiver 的函数类型的变量吗？

```kotlin
val b: (String, Int) -> Unit = String::method1 // 这样可以吗？
```

答案是可以的。在 Kotlin 里，每一个有 Receiver 的函数 -- 其实就是成员函数和扩展函数 -- 它的引用都可以赋值给两种不同的函数类型变量：一种是有 Receiver 的，一种是没有 Recevier 的：

```kotlin
val a: String.(Int) -> Unit = String::method1
val b: (String, Int) -> Unit = String::method1
```

这两种写法都是合法的。

而且同样的，这两种类型的变量也可以互相赋值来转换：

```kotlin
val a: String.(Int) -> Unit = String::method1 // 有接收者的引用
val b: (String, Int) -> Unit = String::method1 // 无接收者的引用
val c: String.(Int) -> Unit = b // 无接收者的扩展函数被赋值给有接收者的变量
val d: (String, Int) -> Unit = a // 有接收者的扩展函数被赋值给无接收者的变量
```

既然这两种类型的变量可以互相赋值来转换，那不就是说无 Receiver 的函数引用也可以赋值给有 Receiver 的变量？

这样的话，是不是一个普通的无 Receiver 的函数也可以直接赋值给有 Recevier 的变量？

```kotlin
fun method3(s: String, i: Int) {

}

...

val e: (String, Int) -> Unit = ::method3
val f: String.(Int) -> Unit = ::method3 // 这种写法也行哦
```

是的，这样赋值也是可以的。

通过这些类型的互相转换，可以把一个本来没有 Receiver 的函数变得可以通过 Receiver 来调用：

```kotlin
fun method3(s: String, i: Int) {

}

...

val f: String.(Int) -> Unit = ::method3
"rengwuxian".method3(1) // 不允许调用，报错
"rengwuxian".f(1) // 可以调用
```

当然也可以反向操作，去把一个有 Receiver 的函数变得不能用 Receiver 调用：

```kotlin
fun String.method1(i: Int) {

}

...

val b: (String, Int) -> Unit = String::method1
"rengwuxian".method1(1) // 可以调用
"rengwuxian".b(1) // 不允许调用，报错
```

收窄功能。

## 最后

在实际开发中，对于顶层扩展函数的使用比较多；对于成员扩展函数来说，它比顶层扩展函数有了作用域的限制，也带来一些弊端，它主要的作用是引用在 <mark>DSL </mark>中。扩展函数虽然看起来很好用，但是不应该盲目使用。

DSL 是领域特定语言（Domain-Specific Language）。在 Kotlin 里，它通常指用 Kotlin 语法写出来的一小套专用写法，读起来像在用一种专门描述某件事的语言。

构建 HTML 就是一个典型例子。调用处写出来是这样：

```
html {
    body {
        p {
            +"Hello"
        }
    }
}
```

html、body、p 都是普通的 Kotlin 函数，嵌套的大括号是带接收者的 lambda。它们被设计成只能在对应的层级里调用，所以这段代码看起来像一份 HTML，而不是一般的函数调用。

成员扩展函数适合做这种分层，把 p 定义成 BODY 的成员扩展后，只有当前接收者是 BODY 时才能调用它，也就是写在 body {...} 里面。顶层扩展函数一旦导入，项目里任何地方的 BODY 都能调用，构建器外面也会看到这些专用函数。

```kotlin
class HTML {
    fun body(init: BODY.() -> Unit) { /* 创建 BODY 并执行 init */ }
}

class BODY {
    fun p(init: P.() -> Unit) { /* 创建 P 并执行 init */ }
}
```

成员扩展函数少了顶层扩展函数随处可调用的灵活性，换来的是一套旨在构建块内部有效的词汇。Gradle 脚本、界面布局、配置构建器都常用这种写法，。

## 原理

```kotlin
fun String.addTo(s: String): String{
    return this + s
}
```

反编译：

```kotlin
@Metadata(
   mv = {1, 6, 0},
   k = 2,
   d1 = {"\u0000\n\n\u0000\n\u0002\u0010\u000e\n\u0002\b\u0002\u001a\u0012\u0010\u0000\u001a\u00020\u0001*\u00020\u00012\u0006\u0010\u0002\u001a\u00020\u0001¨\u0006\u0003"},
   d2 = {"addTo", "", "s", "app_releaseFlavorDebug"}
)
public final class TestKt {
   @NotNull
   public static final String addTo(@NotNull String $this$addTo, @NotNull String s) {
      Intrinsics.checkNotNullParameter($this$addTo, "$this$addTo");
      Intrinsics.checkNotNullParameter(s, "s");
      return $this$addTo + s;
   }
}
```

可以看出扩展函数实际上是生成了一个静态方法，并且将呗扩展的类的对象传进了函数中。

由此可以知道：

在扩展函数中可以访问被扩展的函数域属性，但是不能访问私有的函数与属性，其访问权限与对象权限一致。

## 扩展属性

扩展属性是给现有类型增加一个用点号访问的属性，同时不修改这个类型的源码。

它和扩展函数遵守同样的规则，调用哪一个由变量的声明类型在编译期决定。类里如果已经有同名的成员属性，成员属性优先。

扩展属性提供了一种方法能通过属性语法进行访问的 API 来扩展。尽管它们被叫做属性，但是它们不能拥有任何状态，它不能添加额外的字段到现有的 Java 对象实例。

```kotlin
val String.lastChar: Char 
    get() = this[length -1]
```

读 String.lastChar 时，程序就去执行 get()，拿到最后一个字符。所以扩展属性必须手写访问器，不能直接给初始值。

它和类的普通属性有一个根本区别：没有字段，也不能写初始值。每次读取都要执行 get()，结果不会保存在对象里。

可写的扩展属性用 var， 另外提供 setter 更改对象里原来就有的数据，不能给它新增一块存储：

```kotlin
var StringBuilder.lastChar: Char
    get() = this[length - 1]
    set(value) {
        setCharAt(length - 1, value)
    }
```

这里的 set(value) 改的是 StringBuilder 里已有的字符，不是给这个对象添加一个名为 lastChar 的字段。

原理与扩展函数类似。顶层扩展属性会变成文件生成类里的静态方法，接收者作为第一个参数：

```kotlin
public static final char getLastChar(@NotNull String $this$lastChar) {
      Intrinsics.checkNotNullParameter($this$lastChar, "$this$lastChar");
      return $this$lastChar.charAt($this$lastChar.length() - 1);
   }
```

解析时机也和扩展函数一样：编译器看变量的声明类型，在编译期决定调用哪个扩展属性，不看运行时的实际类型。

成员扩展属性稍有不同：它会编译成外层类上的实例方法，仍然把扩展接收者当作参数传入，同样也不会给被扩展的那个类型增加字段。

## 伴生对象的扩展函数

```kotlin
class Test{
    companion object{

    }
}

fun Test.Companion.get() {

}
```

## 总结

1. 顶层扩展函数可以访问被扩展类的公开的属性与函数（同模块里还有 internal），不能访问私有与protected 属性与函数；成员扩展函数可以访问外层类的公开、私有、protected 、internal 的属性与函数，但是只能访问被扩展类的公开的属性与函数（同模块里还有 internal）。

2. 扩展函数不能被子类重写。

3. 扩展函数实际上是一个静态函数处于类的外部，而成员函数是类的内部函数。

4. 编译器会对被扩展类的调用转换为对生成的静态方法的调用。

## 参考知识

* [Android筑基——Kotlin 扩展函数详解](https://blog.csdn.net/willway_wang/article/details/124536172)

* [会写「18.dp」只是个入门——Kotlin 的扩展函数和扩展属性（Extension Functions Properties）](https://rengwuxian.com/kotlin-extensions/)

* [Kotlin 扩展函数](https://cn.linkedin.com/pulse/kotlin-extension-functions-jitendra-kumar-vrj0c?tl=zh)

* [Kotlin扩展函数与属性原理解析 - 妖久 - 博客园](https://www.cnblogs.com/tangZH/p/17994684)
