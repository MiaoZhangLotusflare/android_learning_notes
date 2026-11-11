# Kotlin 函数参数

Kotlin 规定函数参数默认是 val 修饰的不可变变量，这个规则的约束范围仅限参数本身这个局部变量的引用/值重赋值，完全不约束参数指向的堆内存对象的内部修改。

* 该特性不会带来额外的空间复杂度开销

* 该特性不会阻碍原地修改操作，完全可以实现 O(1) 常数级空间复杂度。

## Kotlin 函数参数为什么是 val 不能修改？

核心回答：函数参数默认是 val，因为参数不是你的，是调用方传进来的。如果允许修改参数，可能会让调用者感到困惑，而且 Kotlin 的设计哲学是不可变优先。

这样设计主要有三个原因：

1. 避免把 “改参数”理解成按引用传参。Kotlin 是按值传递。对参数重新赋值只影响函数内部那个局部绑定，调用方手里的变量不会变。如果允许 var 参数，读代码的人很容易以为外面的变量也被改了。真正的按引用传参运行时成本也更高，语言不支持。
2. 和主构造函数的 val/var 区分开。在主构造函数里写 val 或 var 会生成属性；在普通函数、catch、for 里写同样的关键字，含义完全不同。禁止在这些地方写 val/var，就没有这层歧义。
3. 重新赋值参数本身就不是好写法。参数名应该一直表示“调用时传进来的那个值”。中途改掉它，后面的代码很难判断这个名字现在代表什么。

参数不能被重新赋值，还有一个附带好处：类型检查之后可以安全地智能转换，因为编译器知道这个绑定不会再指向别的对象。

```kotlin
fun lengthOf(value: Any): Int {
    if (value is String) {
        return value.length   // value 已被智能转换成 String
    }
    return 0
}
```

## 为何无法修改函数参数中的变量值？

核心原因：Kotlin 函数参数默认是只读的。

Kotlin 的函数参数默认被声明为 val，不管你传入的是 var 还是 val 类型的变量，函数内部的参数本身都是不可重新赋值的只读变量。

**如何实现修改原始变量的需求？**

如果想真正修改，可以用以下两种方式：

1. 让函数返回新值，再赋值给原始变量

```kotlin
fun main(args: Array<String>) {
    var number = 3
    number = changeNumber(number)
}

fun changeNumber(number: Int): Int {
    return 4
}
```

2. 用引用类型包装（比如自定义类）

因为引用类型传递的是对象引用，修改对象内部的属性会影响原始对象：

```kotlin
class Wrapper(var value: Int)

fun main(args: Array<String>) {
    val numberWrapper = Wrapper(3)
    changeNumber(numberWrapper)
    println(numberWrapper.value) // 输出4
}

fun changeNumber(wrapper: Wrapper) {
    wrapper.value = 4
}
```

Kotlin 构造函数里面的参数可以用 var 或者 val，这个是因为构造函数的参数是对类中变量的一种声明，类中的变量可以是 var 或者 val。普通函数却不可以，普通函数的参数只能用 val。

## 常见认知误区

很多人混淆了两个完全不同的概念，才会产生“必须拷贝参数才能修改”的误解：

* 不被允许的操作：给参数变量本身重新赋值，比如在函数内写 nums = intArrayOf(1,2,3)，这种试图让函数内的 nums 变量指向另一个新数组的操作会编译报错，因为参数是 val 的，不能被重新赋值。

* 完全合法的操作：修改参数指向对象的全部内容，比如修改数组元素、修改可变对象的公开属性、调用可变对象的修改类方法（比如 MutaleList.add()），这些操作和参数的 val 修饰没有任何冲突，不会触发对象拷贝。

## 特殊场景的解决方案

只有两种场景会感受到这个特性的约束，两种场景都不需要付出 O(n) 的拷贝空间代价，完全可以保持 O(1) 空间复杂度：

* 需要在函数内对参数本身做重赋值操作（比如循环、递归中移动指针、覆盖参数值做中间计算）
  
  解决方案：定义一个局部 var 变量承接参数值即可，这个操作只会拷贝一个引用（引用类型仅占几字节的指针大小）或基本类型值，额外空间开销是常数级。
  
  示例代码：
  
  ```kotlin
  fun sumArray(nums: IntArray): Int {
      // 局部可变变量，没有拷贝整个数组，仅拷贝了数组引用
      var index = 0
      var total = 0
      while (index < nums.size) {
          total += nums[index]
          index++
      }
      return total
  }
  ```

* 传入的参数类型本身是只读/不可变的（比如 Kotlin 默认的只读 List<T>、String 这类设计上就不支持原地修改的类型）
  
  解决方案：如果需要做原地修改，直接在定义函数参数时使用可变类型，比如 MutableList<T>、StringBuilder、基础类型数组（IntArray/CharArray 等）即可。这种场景下无法原地修改是类型本身的特性导致的，和函数参数不可变的规则没有关系。

## 参考知识

* [为何Kotlin中无法通过方法修改var变量？](https://www.volcengine.com/article/2461319)
* [Kotlin不可变参数需复制修改时对空间复杂度的影响及解决方案](https://www.volcengine.com/article/2840275)
