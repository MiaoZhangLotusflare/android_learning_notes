# Kotlin == 与 ===

> Kotlin 中 == 比较的是数值是否相等，而 === 比较的是两个对象的地址是否相等。
> 
> 对基本类型, 对基本类型 === 和 == 生成同一套指令，都是比数值。
> 
> - 非空 Int：是值，没有对象，没有地址，=== 只能比大小。
> - 可空 Int？：是对象，有地址，=== 比是不是同一个实例；-128~127 才可能装上 IntegerCache。
> 
> 在 Java 平台上，数值被 JVM 虚拟机以字节码的方式物理存储的，除非需要做可空标识（比如说 Int？)或者涉及范型。在后者中数值是装箱的。
> 
> ```kotlin
> val a: Int = 999 // a 就是个数值，不是个对象，不涉及装箱的问题。
> val b: Int? = a // b 是个对象，涉及装箱问题
> val c: Int? = a
> println(b == c) // true
> println(b === c) // false
> 
> val d: Int = a
> val e: Int = a
> println(d == e) // true
> println(d === e) // true
> 
> val f: Int? = 999
> val g: Int? = f
> val h: Int? = f
> println(g == h) // true
> println(g === h) // true
> ```
> 
> Integer、Long 等包装类型会有缓存，如果Int a 数值是在 -128 到 127 之间，数值是不会重新包装成新对象，而是使用 IntegerCache 里缓存的数据。那么 b===c 也是 true。
> 
> 会缓存的类型有：Byte、Short、Int、Long、Char、Boolean。
> 
> 字符串走的是字符串常量池+intern：
> 
> 1. 源码里的字面量 “hello”，编译进 class 的常量池。
> 2. 类加载时，JVM 保证池里只有一份 "hello"。
> 3. 所有写 “hello”的地方，都拿到同一个 String 对象。
> 
> intern() 是把这个字符串换成常量池里的那一份。字面量已经换过了；new 出来的默认不换。它只影响是不是同一个对象（===），不影响内容是否相等（==）。
> 
> - 与 Java 的差异
>   - 判断值相等，使用 equals()
>   - 判断地址相等，使用 ==
> 
> Kotlin 里面的对象、String 的 == 会调用 equals() 方法。
> 
> data class 会生成 equals()，所以 == 比的是主构造属性，不是地址。
> 
> 自己写的类如果没有重写 equals()，继承的是 Any.equals()，那就是比引用，这时 == 和 ===结果一样。如果某个类有比较属性是否相等的需求，需要自行覆盖 equals() 方法。
> 
> 基本类型的 == 不会调用 equals() 方法，可空类型会装箱，才走包装类型的 equals()。
> 
> Kotlin 的 == 和 === 有一定的类型判断能力。Kotlin 编译期会检查操作符两边对象的类型，如果发现两边数据不存在相等的可能时，会直接出现编译错误。
> 
> 满足下面两点之一，Kotlin 编译期才不会出现编译错误：
> 
> - 两边对象类型相同
> - 左右对象存在父子关系（继承或实现）

总体来说 Kotlin 中 == 比较的是数值是否相等，而 === 比较的是两个对象的地址是否相等。

比较数值用 ==，=== 比的是引用，只在装箱后、且落在缓存区间时才可能为 true。

code1:

```kotlin
val a: Int = 999
val b: Int? = a
val c: Int? = a
println(b == c) // true
println(b === c) // false
```

这个和 java 中运行结果是在意料之中，== 比较数值，而 === 比较的是地址，前者为 true，后者为 false。

code1 代码对应的 java 代码为：

```java
short a = 999;
Integer b = Integer.valueOf(a);
Integer c = Integer.valueOf(a);
boolean var4 = Intrinsics.areEqual(b, c);
System.out.println(var4);
var4 = b == c;
System.out.println(var4);
```

code2:

```kotlin
val a: Int = 999
val b: Int = a
val c: Int = a
println(b == c) // true
println(b === c) // true
```

code3:

```kotlin
val a: Int? = 999
val b: Int? = a
val c: Int? = a
println(b == c) // true
println(b === c) // true
```

code3 对应的 Java 代码：

```java
Integer a = Integer.valueOf(999);
boolean var4 = Intrinsics.areEqual(a, a);
System.out.println(var4);
var4 = a == a;
System.out.println(var4);
```

code3 代码和 code1 代码的区别就是第一行的 Int? 和 Int 的区别，而运行出来的结果 === 也是 true，这是为什么呢？

注意一点：在 Java 平台上，数值被 JVM 虚拟机以字节码的方式物理存储的，除非我们需要做可空标识（比如说 Int？）或者涉及范型。在后者中数值是装箱的。

也就是说如果我们使用的是 val a:Int = 999，这种方式，其实 a 就是个数值，不涉及装箱的问题，也就是不是个对象，而如果我们使用的是 val a:Int? = 999，这样的话，a 是一个 Int 型对象，涉及到装箱问题，这样就很好解释为什么第一段代码的 === 是 false，因为 a 是数值，而 b,c 是装箱后的对象，而第二段代码 a,b,c 都是数值，第三段代码a,b,c 都是同一个对象。

code4: 

```kotlin
val a: Int = 100
val b: Int? = a
val c: Int? = a
println(b == c) // true
printlb(b === c) //true
```

code4 对应 java 代码：

```java
short a = 100;
Integer b = Integer.valueOf(a);
Integer c = Integer.valueOf(a);
boolean var4 = Intrinsics.areEqual(b, c);
System.out.println(var4);
var4 = b == c;
System.out.println(var4);
```

code4 与 code1 的代码除了数值不一样，意外对应的 java 源码也是类似的，源码中就只有数值的类型不一样，看看 valueOf 的源码：

```java
public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
}


private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }
```

在 -128 到 127 之间的数值没有重新包装成新对象，而是使用的 IntegerCache 里缓存的数据，所以 b 和 c 的地址和值都是同一个对象。

* 会缓存的类型

| Kotlin 类型        | JVM 装箱类型            | 默认缓存范围                               |
| ---------------- | ------------------- | ------------------------------------ |
| Byte/Byte?       | java.lang.Byte      | -128～127（整个 Byte 范围，所以任意同值都是同一个对象）   |
| Short/Short?     | java.lang.Short     | -128～127                             |
| Int/Int?         | java.lang.Integer   | -128～127(可用-XX:AutoBoxCacheMax 调高上限) |
| Long/Long?       | java.lang.Long      | -128～127                             |
| Char/Char?       | java.lang.Character | '\u0000' ~ '\u007F'(0~127)           |
| Boolean/Boolean? | java.lang.Boolean   | 只有 true/false 两个实例（永远缓存）             |

Float?、Double？没有这种缓存，每次装箱几乎都是新对象。

非空 Int、Long 等会被变成 JVM 基本类型，没有对象，也没有地址。=== 这时和 == 一样，比较的是值。（非空的 Int/Long 在 JVM 上根本不是对象，所以没有“是不是同一个对象”这回事，=== 只能退化成比数值。）

Kotin 的 Int 在 JVM 上会落成两种形态：

| 写法                | 编译结果              | 内存里是什么            |
| ----------------- | ----------------- | ----------------- |
| val a：Int = 127   | Java 的 int        | 一个 32 位整数，在栈/寄存器里 |
| val a: Int? = 127 | java.lang.Integer | 堆上的对象，有引用、有地址     |

int 就是数字本身，例如二进制里的 000...01111111。没有对象头，没有堆地址，不能问“这两个int 是不是同一个对象”。

Integer 才是对象：堆上有一块内存，里面装着那个数字。变量里存的是指向这块内存的应用。

对非空 Int 没有引用可比。编译器就规定：对基本类型，=== 和 == 生成同一套指令，都是比数值。

* 非空 Int：是值，没有对象，没有地址，=== 只能比大小。

* 可空 Int?：是对象，有地址，=== 比是不是同一个实例；-128~127 才可能装上 IntegerCache。

只有发生装箱时才会看到缓存：

* 可空：Int？、Long？、Byte？、Short？、Char?、Boolean?

* 当成 Any / 泛型：listOf(127)、Map 的 Key、传给 Java 的 Object

Kotlin.Any 类与 java.lang.Object 类相互映射，但它本身只定义了三个函数用来覆盖：toString()、equals() 和 hashCode()。

**String**

字符串走的是字符串常量池+intern()：

1. 源码里的字面量 “hello”，编译进 class 的常量池。

2. 类加载时，JVM 保证池里只有一份 “hello”。

3. 所有写 "hello" 的地方，都拿到同一个 String 对象。

所以：

```kotlin
val a = "hello"
val b = "hello"
println(a === b) //true，都是常量池里那一份

val c = String("hi".toCharArray())  // 运行时 new 出来的
println(a === c)  // false，值相同但不是池里那个

val d = c.intern()
println(a === d)  // true，intern() 显式放进/取回池里那份

val e = "hello"
val f = "hel" + "lo"   // 编译期就变成 "hello"
println(e === f)        // true

val x = "hel"
val y = x + "lo"       // 运行时 new 的 String
println("hello" === y)  // false
println("hello" == y)   // true
```

intern() 是 String 的实例方法：把当前字符串放进（或从）字符串常量池里取出那一份唯一引用。

JVM 上就是 Java 的 String.intern()，Kotlin 没有自己再实现一套。

常量池可以理解成 JVM 里一张”字符串去重表“：同样的字符序列，池里只留一个对象。

调用 s.instern() 时：

1. 池里已有内容相同的字符串 -> 返回池里那个对象（不是 s 自己，除非 s 本来就在池里的）

2. 池里没有 -> 把 s 放进去（或放入一份等价内容），再返回池里的引用

intern() 不改内容，只改“你拿到的是哪一个对象”。影响的是 ===，不是 ==。

字面量“hello” 会在类加载时自动 intern，不必手写。

* 什么时候用
  
  几乎只在这两种情况：
  
  * 大量重复字符串要省内存（比如同一批单词出现几百万次，intern 后只留一份）
  
  * 你故意要用 === 比字符串（极少数性能技巧；一般不该怎么干）
  
  日常比较字符用 == 就够，不必 intern。

* 不要随便用
  
  池的生命周期很长（和 JVM/类加载器相关），intern 进去的字符串不容易被回收。
  
  用户输入、请求 url、日志、UUID 这类几乎不重复或无限增长的内容拿去 intern，等于往一个全局表里堆垃圾，可能撑大原空间、拖慢 intern 本身。
  
  字面量已经在池里了，再 intern 没有收益。

intern() 是把这个字符串换成常量池里的那一份。字面量已经换过了；new 出来的默认不换。它只影响是不是同一个对象（===），不影响内容是否相等（==）。

**Java**

Java 里面 == 对对象就是比引用，所以 Java 比字符串必须写 equals。

- 判断值相等，使用 equals()

- 判断地址相等，使用 ==

**Kotlin 的 == 会走 equals**

Kotlin 的 == 会走 equals()。但不是所有类型都会调用 equals()。

```kotlin
a == b
→ a?.equals(b) ?: (b === null)
```

对象不为空会调用 equals()，String 也会调用 equals()。

data class 会生成 equals，所以 == 比的是主构造属性，不是地址。

自己写的类如果没重写 equals，继承的是 Any.equals，那就是比引用，这时 == 和 === 结果一样。

基本类型不走 equals，非空 Int、Long、Boolean 等会编成 JVM 基本类型，== 直接变成 icmp 这类指令，不会装箱再调 equals()。可空数字会装箱，这时才走包装类型的 equals()。

* 例外
  
  * Array 没有按内容重写 equals
    
    ```kotlin
    arrayOf(1,2) == arrayOf(1,2) // false
    ```
    
    这里确实走了 equals，但 Array.equals 就是对象身份。要比内容用 contentEquals()。List 重写了 equals，所以 listOf(1,2) == listOf(1,2) 是 true。
  
  * 浮点：非空和可空可能不一致
    
    ```kotlin
    Double.NaN == Double.NaN      // false，IEEE 规则
    （Double.NaN as Double?） == Double.NaN // true，走了 Double.equals
    ```
    
    非空 Double 的 == 不走 equals；装箱后才走。
    
    IEEE 规则：指的是 IEEE 754 ，也就是现在几乎所有语言、CPU 都在用的浮点数标准。Float/Double 怎么存、怎么加减、怎么比较，都按它来。IEEE 754 规定：任何涉及 NaN 的比较都是 false。NaN 不等于任何值，包括它自己，NaN (Not a Number) 表示“这不是一个合法数字”。

**重载操作符**

Any.equals() 函数定义如下：

```kotlin
open operator fun equals(other: Any?): Boolean
```

它重载了 == 操作符。Kotlin 中的 == 等同于调用 equals() 函数，比较两个对象引用是否相等要用 === 操作符。

但这么说其实不准确，kotlin 并不会直接把 == 编译为调用 equals() 函数，而是用 kotlin.jvm.internal.Intrinsics.java 的 areEqual() 方法包装一下，这个方法有多个重载形式，看一下最通用的一个：

```java
public static boolean areEqual(Object first, Object second) {
    return first == null ? second == null : first.equals(second);
}
```

这就保证了空安全。所以说，用 == 操作符时不需要担心空安全，a == b 并不等同于 a.equals(b)，而是 a?.equals(b)?:b==null。

**通用 equals() 写法**

与 Java 相同，Any.equals() 函数比较的是两个对象的引用是否相等。也就是说，如果没有覆盖 equals() 函数，== 与 === 的结果是相同的。如果某个类有比较属性是否相等的需求，需要自行覆盖 equals() 方法。

```kotlin
class Person(val name: String, var age: Int = 0)
```

equals() 方法：

```kotlin
override fun equals(other: Any?): Boolean {
  return when (other) {
    !is Person -> false
    else -> this === other ||
            name == other.name && age == other.age
  }
}
```

一般覆盖 equals() 的同时要覆盖 hashCode()，这里就直接套用 Java 写法了：

```kotlin
override fun hashCode(): Int {
  var result = 17
  result = result * 31 + name.hashCode()
  result = result * 31 + age
  return result
}
```

Kotlin 的 == 和 === 有一定的类型判断能力。Kotlin 编译期会检查操作符两边对象的类型，如果发现两边数据不存在相等的可能时，会直接出现编译错误。

具体来说，只有两个对象满足下面两点之一时，Kotlin 编译期才不会出现编译错误：

* 左右对象类型相同

* 左右对象存在父子关系（继承或实现）

比如 Int 与 String 不满足上面两条之一，下面的例子就会报错：

```kotlin
println(1 == "1")
println(1 === "1")
```

编译器错误提示：

```kotlin
Error:(29, 13) Kotlin: Operator '==' cannot be applied to 'Int' and 'String'
Error:(30, 13) Kotlin: Operator '===' cannot be applied to 'Int' and 'String'
```

就算是基本数据类型之间相互比较，也会报错。比如下面三句，都会出现编译错误：

```kotlin
1 == 1L // Int 与 Long
1 == 1.0 // Int 与 Double
1.0 == 1.0F // Double 与 Float
```

Kotlin 并没有给基本数据类型重写 equals() 方法，因此基本数据类型之间不能直接使用 == 操作比较数值是否相等，必须进行显式的类型转换，上面三句需要改成下面这样：

```kotlin
1.toLong() == 1L
1.toDouble() == 1.0
1.0 == 1.0F.toDouble()
```

将数值范围比较小类型转换为范围比较大的类型。

## 参考内容

* [Kotlin ==和===的区别](https://blog.csdn.net/jdsjlzx/article/details/106350713)
* [详解 Kotlin 的 equals() 和 ==](https://zhuanlan.zhihu.com/p/27214002)
