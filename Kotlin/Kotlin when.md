# Kotlin when

> Java Switch 与 kotlin when 都是处理多分支条件判断的常用工具。但是 kotlin 中是不支持 switch 的。
> 
> 而 Java Switch 有三个痛点：1. 语法繁琐，每个分支都需要写 case 关键字定义匹配条件，还要加 break 语句防止穿透。2. 穿透风险，这是 Switch 最容易踩的坑，一旦忘记写 case，程序就会从匹配到的分支开始，依次执行后续所有分支的代码，导致逻辑错误。3. 类型局限，Java 7 之前仅支持 byte、short、int、char 等基本类型，后续虽支持 String 和枚举，但仍无法匹配范围、类型判断等复杂场景。比如想判断一个数字是否在某个区间，或者判断一个对象的具体类型，switch 完全无能为力，智能转而使用繁琐的 if-else 嵌套。
> 
> Kotlin 的 when 相比较 switch，语法更简洁（无 case、break）、匹配能力更强（支持单值、多值、范围、类型、无参数匹配）、更安全（无穿透风险）；相比 if-else，更适合多分支场景，可消除嵌套缩进。还支持智能类型转换（is 匹配后自动转类型）。
> 
> 1. 极致简洁，摈弃了 case 和 break 关键字，直接用匹配条件->执行逻辑的结果，多值匹配用逗号分隔即可，代码量大幅减少。
> 
> 2. 匹配能力更强大，支持单值（when(fruit)，匹配条件可以是字面量、常量变量或返回常量的表达式）、多值(1,2,3,4->)、范围(in 1..9)、类型（is Int）、表达式等多种匹配方式，甚至可以无参数适用，实现自定义复杂条件判断。比如判断数字区间、检测对象类型、匹配表达式结果等场景，when 表达式都能轻松应对。
> 
> 3. 无穿透风险，when 表达式的每个分支都是独立的，默认不会发生穿透。
> 
> when 是表达式而非语句，会返回匹配分支的结果，可直接赋值给变量或作为函数返回值，进一步简化代码。
> 
> when 可用在简化 if-else 多层嵌套上，消除嵌套缩进，提升代码可读性。
> 
> 对于 sealed class、enum、boolean 类型，编译器可以检查是否穷举了所有可能，可以不加 else，漏掉任何一项仍然会报错。
> 
> 无参数 when 的分支按照“异常场景->普通场景->默认场景”排序，符合日常逻辑习惯，便于理解。（UI中观察 statu 然后根据状态显示页面的代码就是这样的）。
> 
> - when 与 if-else 链是什么区别？
> 1. when 支持值/多值/区间/类型/集合多形态匹配，if 只能写布尔条件
> 2. when 对于 menu、sealed class、boolean 有穷举性检查，if 没有
> 
> 什么时候用 if?如果条件之间有复杂逻辑组合，if 反而更清楚。
> 
> - when 的集合成员匹配是 o(n) 还是 o(1)？
> 
> 取决于集合自己，集合匹配走的是 contains，hashset 就是 o(1)，treeset 就是 o(log n), array/list 就是 o(n)。如果是常量，编译器可以编译成 switch，不经过 contains。
> 
> - sealed 的穷举性检查是怎么实现的？
> 
> 编译器做的是先列出这个 sealed 类型所有直接的非 sealed 子类型，再看 when 的分支有没有把他们都盖住。中间还套着 sealed 时，会先把那一层展开，缺任何一项又没有 else，就报错。从 Kotlin 1.7 起，当表达式和当语句用都做这个检查。
> 
> 普通类的子类可以写在任何地方，编译器列不出完整名单。当表达式用时必须有 else；当语句用时可以不写，没匹配上就什么都不做。
> 
> - 使用中遇到过什么问题？
> 
> 案例一：网络状态用 sealed 建模，但 when 写了 else -> {}，新增一种 Timeout 状态后没补分支，页面卡在空白。修复为删掉 else，让编译器把遗漏点暴露出来。
> 
> 案例二：is 分支漏了 null，接口返回空值时走了“其他”分支，展示的是类型名而不是默认文案。
> 
> - when 在 Android 上最常用的三个位置：
>   - 状态渲染 - 把 sealed 的 UiState 映射成界面动作（显示骨架屏/显示列表/显示错误），集中在一处便于审查。
>   - 错误码映射 - when(code) {in 500..599 -> "服务异常"...} 给用户可读文案，同时补日志。
>   - 多类型分支的差异化处理 - 比如分享渠道不同类型时用 is 分支分别处理。共同点是把“一堆 if 散在各处”收敛成一个可枚举的决策点，这本身就是价值。
> 
> when 的收益不只是语法糖，而是把决策集中成一处可审查的结构。

Java 的 switch 是语句、能穿透、case 里必须 break；Kotlin 的 when 是表达式、不会穿透、且在穷举时能编译期查错。

when 的核心价值是把分支逻辑表达成可赋值的表达式。五种常用形态：1. 值匹配（when(code) { 200 -> OK}）；2. 区间匹配（in 1..9 -> 正常）；3. 类型匹配（is String ->）；4. 集合匹配成员（in listOf(...) ->）；5. 任意条件（else 或 !in）。when 能否有返回值，取决于它是否被当作表达式使用 -- 被赋值或 return 时，编译器要求所有路径都有结果，这时不写 else 就必须依赖 sealed 类型的穷举性。

当 when 没有 else 时，编译器会检查是否穷举了所有可能 - 对 sealed class、enum、以及基本类型 Boolean，Kotlin 知道取值全集，如果这三个可为 null，则对 null 需要显式 null -> 分支，其他的则要求有 else。

类型匹配的智能转换（if (v is String)）有前提-变量 v 必须是稳定的（val 或不可被并发修改的成员），若变量 v 是自定义 getter 的 var，智能转换失败，需要先赋给局部变量。

常量分支会编成 switch，例如 1,2,3 -> 或 "a","b" ->。集合都是 contains。

## 1. 前言

### 1.1. Java Switch 的使用痛点（语法繁琐、穿透风险、类型局限）

Java Switch 是处理多分支条件判断的常用工具，但在实际使用中，有很多痛点。

首先是语法繁琐，每个分支都需要写 case 关键字定义匹配条件，还得在分支末尾加 break 语句防止穿透，哪怕是简单的返回值逻辑，也得写一堆重复的结构。比如一个根据月份获取季节的逻辑，Java switch 写法就得嵌套多层 case 和 break。

其次是穿透风险，这是 Switch 最容易踩的坑 - 一旦忘记写 break，程序就会从匹配到的分支开始，依次执行后续所有分支的代码，导致逻辑错误。而且这种错误在编译时不会报错，只能在运行时发现，排查起来十分麻烦。

最后是类型局限，Java 7 之前的 Switch 仅支持 byte、short、int、char 等基本类型，后续虽支持 String 和枚举，但仍无法匹配范围、类型判断等复杂场景。比如想判断一个数字是否在某个区间，或者判断一个对象的具体类型，Switch 完全无能为力，只能转而使用繁琐的 if-else 嵌套。

```java
// Java Switch 痛点示例：根据月份获取季节
public static String getSeason(int month) {
    String season;
    switch (month) {
        case 3:
        case 4:
        case 5:
            season = "春季";
            break; // 忘记写就会穿透到夏季
        case 6:
        case 7:
        case 8:
            season = "夏季";
            break;
        case 9:
        case 10:
        case 11:
            season = "秋季";
            break;
        case 12:
        case 1:
        case 2:
            season = "冬季";
            break;
        default:
            season = "月份无效";
    }
    return season;
}
```

### 1.2. Kotlin when 表达式的核心亮点（简洁、灵活、无穿透）

Kotlin 的 when 表达式正是为解决 Java Switch 的痛点而生，它不仅是对 Switch 的替代，更是一次全面的升级，核心亮点体现在三个方面。

第一是极致简洁，摈弃了 case 和 break 关键字，直接用“匹配条件->执行逻辑”的结果，多值匹配用逗号分隔即可，代码量大幅减少。

第二是高度灵活，突破了 Switch 的类型局限，支持单值、多值、范围、类型、表达式等多种匹配方式，甚至可以无参数使用，实现自定义复杂条件判断。比如判断数字区间、检测对象类型、匹配表达式结果等场景，when 表达式都能轻松应对。

第三是无穿透风险，when 表达式的每个分支都是独立的，默认不会发生穿透，无需像Switch 那样手动加 break，从语法层面避免了穿透错误。同时，when 还是表达式，能直接返回结果赋值给变量或作为函数返回值，进一步简化代码。

同样是“根据月份获取季节”，用 Kotlin when 表达式改写后，逻辑清晰且简洁：

```kotlin
// Kotlin when 表达式示例：根据月份获取季节
fun getSeason(month: Int): String {
    return when (month) {
        3, 4, 5 -> "春季"
        6, 7, 8 -> "夏季"
        9, 10, 11 -> "秋季"
        12, 1, 2 -> "冬季"
        else -> "月份无效"
    }
}
```

## 2. When 表达式基础入门

### 2.1. 什么是 when 表达式？

when 表达式是 Kotlin 中用于处理多分支条件判断的核心语法结构，是对 Java Switch 语句的彻底优化和升级。从本质上讲，when 是一个表达式（而非语句），这意味着它会计算并返回一个结果，这个结果可以直接赋值给变量、作为函数返回值，或者嵌入到其他表达式中。

when 表达式的核心思想是“匹配分支并执行对应逻辑”，它会依次判断当前值与各个分支的匹配条件，找到第一个匹配的分支后执行相应逻辑，并返回该分支的结果（若为代码块则返回最后一行表达式的结果），之后不再执行后续分支（无穿透）。相比 switch，when 不仅语法更简洁，匹配能力也更强大，是 Kotlin 流程控制中不可或缺的工具。

### 2.2. 基本语法格式（简单条件匹配+直接赋值示例）

#### 2.2.1. 基本语法

when 表达式的基本语法有两种形式：一种是带参数的形式（最常用），用于匹配指定参数的值；另一种是无参数形式，用于自定义复杂条件判断，带参数的基本语法如下：

```kotlin
// 带参数的 when 表达式
when (参数) {
    匹配条件1 -> 执行逻辑1 // 单值匹配
    匹配条件2, 匹配条件3 -> 执行逻辑2 // 多值匹配
    匹配条件4..匹配条件5 -> 执行逻辑3 // 范围匹配（后续讲解）
    is 数据类型 -> 执行逻辑4 // 类型匹配（后续讲解）
    else -> 默认执行逻辑 // 类似 Switch 的 default，匹配所有未覆盖的情况
}
```

说明：1. 匹配条件可以是常量、变量、表达式等；2. 执行逻辑若为单条语句，可直接写在箭头后，无需花括号；若为多条语句，需要花括号包裹，此时分支结果为最后一行语句的结果；3. else 分支可选，但建议加上，避免遗漏为覆盖的情况（若编译器能确定所有情况都已覆盖，可省略）。

#### 2.2.2. 直接赋值示例

由于 when 是表达式，其结果可直接赋值给变量，这是 Java Switch 无法做到的。比如“根据星期几获取对应的中文名称”：

```kotlin
fun main() {
    val day = 3 // 1-7 对应周一到周日

    // when 表达式结果直接赋值给变量
    val dayName = when (day) {
        1 -> "星期一"
        2 -> "星期二"
        3 -> "星期三"
        4 -> "星期四"
        5 -> "星期五"
        6 -> "星期六"
        7 -> "星期日"
        else -> "无效日期"
    }

    println("今天是：$dayName") // 输出：今天是：星期三
}
```

再比如执行逻辑为多条语句的场景，此时用花括号包裹，返回最后一行结果：

```kotlin
fun getScoreDesc(score: Int): String {
    return when (score) {
        in 90..100 -> {
            println("成绩优异")
            "优秀" // 分支结果，作为整个 when 表达式的结果
        }
        in 80..89 -> {
            println("成绩良好")
            "良好"
        }
        else -> "其他"
    }
}

fun main() {
    println(getScoreDesc(95)) // 输出：成绩优异  优秀
}
```

### 2.3. 与 Switch 的核心区别（无需 break、支持表达式返回值）

when 表达式与 Java Switch 相比，有多个核心区别，正是这些区别让 when 更简洁、更灵活、更安全。

#### 2.3.1. 无需 break，无穿透风险

Java Switch 的分支默认会穿透，必须在每个分支末尾加 break 才能终止执行，而 when 表达式的分支默认不穿透，找到第一个匹配的分支执行后就会终止，无需手动加 break，从语法层面避免了穿透错误。

```java
// Java Switch 穿透风险示例
public static void main() {
    int num = 2;
    switch (num) {
        case 1:
            System.out.println("一");
            // 忘记加 break，会穿透到 case 2
        case 2:
            System.out.println("二");
        case 3:
            System.out.println("三");
    }
    // 输出：二  三（发生穿透）
}
```

```kotlin
// Kotlin when 无穿透示例
fun main() {
    val num = 2
    when (num) {
        1 -> println("一")
        2 -> println("二")
        3 -> println("三")
    }
    // 输出：二（无穿透）
}
```

#### 2.3.2. 支持表达式返回值，可直接赋值

Java Switch 是语句，不返回值，无法直接赋值给变量，若要实现类似逻辑，需先定义变量再在分支中赋值；而 when 是表达式，会返回匹配分支的结果，可直接赋值给变量或作为函数返回值，代码更简洁。

```kotlin
// Java Switch 赋值需先定义变量
public static String getLevel(int score) {
    String level;
    switch (score / 10) {
        case 10, 9 -> level = "A";
        case 8 -> level = "B";
        default -> level = "C";
    }
    return level;
}
```

```kotlin
// Kotlin when 直接赋值
fun getLevel(score: Int): String {
    return when (score / 10) {
        10, 9 -> "A"
        8 -> "B"
        else -> "C"
    }
    // 更简洁的单表达式函数写法
    // fun getLevel(score: Int) = when (score / 10) { 10,9 -> "A"; 8->"B"; else->"C" }
}
```

#### 2.3.3. 匹配能力更强大，支持多种匹配方式

Java Switch 仅支持单值匹配（出了枚举和 String），而  when 表达式支持单值、多值、范围、类型、表达式等多种匹配方式，能应对更复杂的场景。

#### 2.3.4. 语法更简洁，无需 case 关键字

Java Switch 需要用 case 定义每个分支的匹配条件，而 when 直接用 “匹配条件 -> 执行逻辑” 的结构，省去了大量冗余的 case 关键字，代码更简洁直观。

## 3. when 表达式灵活匹配场景

when 表达式的最大优势在于其灵活的匹配能力，除了类似 Switch 的单值匹配，还支持多种复杂的匹配场景，覆盖实际开发中的绝大多数需求。

### 3.1. 单值匹配（直接匹配具体数值/字符串）

单值匹配是 when 最基础的用法，与 Java Switch 的单值匹配类似，用于匹配指定的常量值（如数值、字符串、枚举等）。匹配条件可以是字面量、常量变量或返回常量的表达式。

```kotlin
// 字符串单值匹配
fun getFruitColor(fruit: String): String {
    return when (fruit) {
        "苹果" -> "红色"
        "香蕉" -> "黄色"
        "葡萄" -> "紫色"
        else -> "未知颜色"
    }
}

// 枚举单值匹配
enum class Direction { UP, DOWN, LEFT, RIGHT }
fun getDirectionDesc(direction: Direction): String {
    return when (direction) {
        Direction.UP -> "向上"
        Direction.DOWN -> "向下"
        Direction.LEFT -> "向左"
        Direction.RIGHT -> "向右"
    }
    // 枚举匹配可省略枚举类名（需导入或在同一作用域）
    // return when (direction) { UP -> "向上"; DOWN -> "向下"; ... }
}

fun main() {
    println(getFruitColor("苹果")) // 输出：红色
    println(getDirectionDesc(Direction.LEFT)) // 输出：向左
}
```

### 3.2. 多值匹配（用逗号分隔多个匹配条件）

当多个匹配条件对应相同的执行逻辑时，可在一个分支中用逗号分隔多个匹配条件，实现“多值对应同一结果”的逻辑，无需像 Java Switch 那样写多个重复的 case 分支。

```kotlin
// 多值匹配：判断是否为工作日
fun isWorkDay(day: Int): Boolean {
    // 1-5 对应周一到周五，都返回 true
    return when (day) {
        1, 2, 3, 4, 5 -> true
        6, 7 -> false
        else -> throw IllegalArgumentException("无效的星期数")
    }
}

// 多值匹配：根据考试等级获取奖励
fun getReward(level: String): String {
    return when (level) {
        "A", "A+" -> "500元奖学金"
        "B", "B+" -> "200元奖学金"
        "C" -> "学习用品"
        else -> "鼓励信"
    }
}

fun main() {
    println(isWorkDay(3)) // 输出：true
    println(getReward("A+")) // 输出：500元奖学金
}
```

### 3.3. 范围匹配（in 关键字匹配区间）

when 表达式支持通过 in 关键字匹配“值是否在指定范围内”，范围可以是数值范围、字符范围等，这是 Java Switch 完全不具备的能力，能极大简化区间判断的逻辑。

```kotlin
// 数值范围匹配：根据分数评级
fun getScoreLevel(score: Int): String {
    require(score in 0..100) { "分数必须在 0-100 之间" }
    return when (score) {
        in 90..100 -> "优秀"
        in 80..89 -> "良好"
        in 60..79 -> "及格"
        else -> "不及格" // 0-59 范围
    }
}

// 字符范围匹配：判断字符类型
fun getCharType(c: Char): String {
    return when (c) {
        in 'a'..'z', in 'A'..'Z' -> "字母"
        in '0'..'9' -> "数字"
        else -> "特殊字符"
    }
}

fun main() {
    println(getScoreLevel(85)) // 输出：良好
    println(getCharType('5')) // 输出：数字
    println(getCharType('@')) // 输出：特殊字符
}
```

除了匹配“在范围内”，还可以用 !in 匹配“不在范围内”的情况：

```kotlin
// !in 匹配不在范围内的值
fun checkAge(age: Int): String {
    return when (age) {
        !in 0..120 -> "年龄无效"
        in 0..17 -> "未成年"
        else -> "成年"
    }
}
```

### 3.4. 类型匹配（is 关键字判断数据类型）

when 表达式支持通过 is 关键字判断变量的具体数据类型，实现 “类型匹配”逻辑，且匹配成功后会自动进行类型转换（智能类型转换），无需手动墙砖，这在处理多类型对象时非常实用。

```kotlin
// 类型匹配：处理不同类型的数据
fun handleData(data: Any): String {
    return when (data) {
        is Int -> "整数类型，值为：$data" // 自动转换为 Int 类型
        is String -> "字符串类型，长度为：${data.length}" // 自动转换为 String 类型
        is Double -> "浮点数类型，值为：$data" // 自动转换为 Double 类型
        is List<*> -> "列表类型，元素个数为：${data.size}" // 自动转换为 List 类型
        else -> "未知类型"
    }
}

fun main() {
    println(handleData(100)) // 输出：整数类型，值为：100
    println(handleData("Kotlin")) // 输出：字符串类型，长度为：6
    println(handleData(listOf(1, 2, 3))) // 输出：列表类型，元素个数为：3
}
```

智能类型转换的优势在于，匹配成功后可直接调用该类型的方法和属性（如示例中的 data.length、data.size），无需手动强转，既简洁又安全。

### 3.5. 无参数匹配（自定义复杂条件判断）

when 表达式还支持无参数的形式，此时每个分支的匹配条件是一个布尔表达式，当表达式结果为 true 时，执行对应的逻辑。这种方式适用于需要自定义复杂条件判断的场景，相当于多个独立的 if-else 分支，但语法更简洁。

```kotlin
// 无参数 when：自定义复杂条件判断（判断学生是否符合奖学金条件）
fun isEligibleForScholarship(score: Int, attendance: Double, isExcellent: Boolean): Boolean {
    return when {
        // 条件1：成绩优秀且出勤率≥95%
        score >= 90 && attendance >= 0.95 -> true
        // 条件2：成绩良好且是优秀学生
        score >= 80 && isExcellent -> true
        // 条件3：其他情况不满足
        else -> false
    }
}

// 无参数 when：替代多层 if-else
fun getLoginStatus(username: String?, password: String?): String {
    return when {
        username.isNullOrEmpty() -> "用户名不能为空"
        password.isNullOrEmpty() -> "密码不能为空"
        username == "admin" && password == "123456" -> "登录成功"
        else -> "用户名或密码错误"
    }
}

fun main() {
    println(isEligibleForScholarship(92, 0.96, false)) // 输出：true
    println(getLoginStatus("admin", "")) // 输出：密码不能为空
}
```

无参数 when 的本质是 “多个布尔条件的依次判断”，找到第一个结果为 true 的条件分支执行，适用于各分支条件无共同参数的场景，比多层 if-else 更简洁易读。

## 4. when 表达式实用场景举例

在实际开发中，when 表达式的应用场景非常广泛，无论是简单的多分支判断，还是复杂的条件判断，都能发挥其优势。

### 4.1. 替代多分支 Switch 逻辑

这是 when 表达式最基础的应用场景，用于替代 Java 中繁琐的多分支 Switch 逻辑，尤其适合匹配枚举、字符串、数值等场景，代码更简洁且无穿透风险。

示例：根据支付方式处理支付逻辑（替代 Java Switch）

```kotlin
// Java Switch 处理支付逻辑
public enum PaymentMethod { ALIPAY, WECHAT, CARD }
public static String processPayment(PaymentMethod method, double amount) {
    switch (method) {
        case ALIPAY:
            return "支付宝支付 " + amount + " 元，已完成";
        case WECHAT:
            return "微信支付 " + amount + " 元，已完成";
        case CARD:
            return "银行卡支付 " + amount + " 元，已完成";
        default:
            throw new IllegalArgumentException("不支持的支付方式");
    }
}
```

```kotlin
// Kotlin when 替代 Switch 处理支付逻辑
enum class PaymentMethod { ALIPAY, WECHAT, CARD }
fun processPayment(method: PaymentMethod, amount: Double): String {
    return when (method) {
        PaymentMethod.ALIPAY -> "支付宝支付 $amount 元，已完成"
        PaymentMethod.WECHAT -> "微信支付 $amount 元，已完成"
        PaymentMethod.CARD -> "银行卡支付 $amount 元，已完成"
    }
    // 省略枚举类名的写法（需导入或同一作用域）
    // return when (method) { ALIPAY -> "..."; WECHAT -> "..."; CARD -> "..." }
}

fun main() {
    println(processPayment(PaymentMethod.ALIPAY, 100.0))
    // 输出：支付宝支付 100.0 元，已完成
}
```

**When 无法替代 swicth 的场景**只有一种：分支里已经写了代码，还要继续掉进下一个分支（数据库升级）。when 匹配到一个分支就结束，没有穿透。

```java
switch (version) {
    case 1:
        migrateFrom1();
        // 没有 break，继续往下
    case 2:
        migrateFrom2();
        break;
    default:
        break;
}
```

### 4.2. 变量赋值（直接通过匹配结果赋值）

由于 when 是表达式，其结果可直接赋值给变量，这是非常常用的场景，能替代“定义变量+多分支赋值”的繁琐写法，让变量赋值逻辑更集中、更清晰。

示例：根据用户等级赋值对应的权限列表

```kotlin
// 用户等级枚举
enum class UserLevel { ADMIN, MANAGER, USER, GUEST }

fun getPermissions(level: UserLevel): List<String> {
    // when 表达式结果直接赋值给变量
    val permissions = when (level) {
        UserLevel.ADMIN -> listOf("增", "删", "改", "查", "管理")
        UserLevel.MANAGER -> listOf("增", "删", "改", "查")
        UserLevel.USER -> listOf("查", "改")
        UserLevel.GUEST -> listOf("查")
    }
    return permissions
}

// 更简洁的单表达式函数写法
fun getPermissionsSimpler(level: UserLevel) = when (level) {
    UserLevel.ADMIN -> listOf("增", "删", "改", "查", "管理")
    UserLevel.MANAGER -> listOf("增", "删", "改", "查")
    UserLevel.USER -> listOf("查", "改")
    UserLevel.GUEST -> listOf("查")
}

fun main() {
    println(getPermissions(UserLevel.MANAGER))
    // 输出：[增, 删, 改, 查]
}
```

### 4.3. 函数返回值（作为函数主体直接返回结果）

when 表达式可直接作为函数的返回值，此时函数可简化为单表达式函数（省略花括号和 return 关键字），让函数定义更简洁，尤其适合逻辑简单的工具函数。

示例：1. 计算图形面积（根据图形类型和参数计算）；2. 转换日期格式（将数字星期转换为中文）

```kotlin
// 图形类型枚举
enum class Shape { CIRCLE, RECTANGLE, SQUARE }

// when 作为函数返回值（计算图形面积）
fun calculateArea(shape: Shape, vararg params: Double): Double {
    return when (shape) {
        Shape.CIRCLE -> {
            val radius = params[0]
            Math.PI * radius * radius // 圆的面积公式
        }
        Shape.RECTANGLE -> {
            val width = params[0]
            val height = params[1]
            width * height // 矩形面积公式
        }
        Shape.SQUARE -> {
            val side = params[0]
            side * side // 正方形面积公式
        }
    }
}

// 单表达式函数：when 直接作为返回值
fun convertWeekToChinese(week: Int) = when (week) {
    1 -> "星期一"
    2 -> "星期二"
    3 -> "星期三"
    4 -> "星期四"
    5 -> "星期五"
    6 -> "星期六"
    7 -> "星期日"
    else -> "无效星期"
}

fun main() {
    println(calculateArea(Shape.CIRCLE, 5.0)) // 输出：78.5398...
    println(calculateArea(Shape.RECTANGLE, 3.0, 4.0)) // 输出：12.0
    println(convertWeekToChinese(5)) // 输出：星期五
}
```

### 4.4. 简化复杂条件判断（替代多层 if-else）

当存在多层 if-else 嵌套的复杂条件判断时，用无参数 when 表达式可将其转换为线性分支，消除嵌套缩进，提升代码可读性。这是 when 表达式比 if-else 更有优势的场景之一。

示例：用户注册信息校验（多层 if-else 嵌套 vs 无参数 when）

```kotlin
// 多层 if-else 嵌套：校验注册信息
fun checkRegisterInfoOld(username: String, password: String, phone: String): String {
    if (username.isEmpty()) {
        return "用户名不能为空"
    } else {
        if (username.length < 3) {
            return "用户名长度不能小于3位"
        } else {
            if (password.isEmpty()) {
                return "密码不能为空"
            } else {
                if (password.length < 6) {
                    return "密码长度不能小于6位"
                } else {
                    if (!phone.matches("^1[3-9]\\d{9}$".toRegex())) {
                        return "手机号格式错误"
                    } else {
                        return "注册信息校验通过"
                    }
                }
            }
        }
    }
}

// 无参数 when 简化：校验注册信息（无嵌套）
fun checkRegisterInfoNew(username: String, password: String, phone: String): String {
    return when {
        username.isEmpty() -> "用户名不能为空"
        username.length < 3 -> "用户名长度不能小于3位"
        password.isEmpty() -> "密码不能为空"
        password.length < 6 -> "密码长度不能小于6位"
        !phone.matches("^1[3-9]\\d{9}$".toRegex()) -> "手机号格式错误"
        else -> "注册信息校验通过"
    }
}

fun main() {
    println(checkRegisterInfoNew("kotlin", "123456", "13800138000"))
    // 输出：注册信息校验通过
    println(checkRegisterInfoNew("ko", "12345", "123456"))
    // 输出：用户名长度不能小于3位
}
```

可以看到，无参数 when 将 5 层嵌套的 if-else 简化为线性分支，逻辑一目了然，后续维护和修改也更方便。

## 5. 总结与使用建议

### 5.1. 核心知识点回顾（when 表达式的灵活性与优势）

* 本质特性：when 是表达式而非语句，会返回匹配分支的结果，可直接赋值给变量或作为函数返回值；默认无穿透，无需手动加 break。

* 核心优势：相比 Java Switch，语法更简洁（无 case、break）、匹配能力更强（支持单值、多值、范围、类型、无参数匹配）、更安全（无穿透风险）；相比 if-else，更适合多分支场景，可消除嵌套缩进。

* 关键能力：支持智能类型转换（is 匹配后自动转类型）、范围匹配（in 关键字）、多值匹配（逗号分隔）、无参数自定义条件匹配，覆盖多种复杂场景。

### 5.2. 适用场景与避坑点（何时优先用 when、避免过度复杂匹配）

#### 5.2.1. 适用场景

1. 多分支条件判断场景：当存在 3 个及以上条件分支时，优先使用 when，比 if-else 更简洁；替代 Java 的 Switch 语句（所有 Switch 场景都可用 when 替代）。

2. 特定匹配场景：需要进行范围匹配（如数值区间）、类型匹配（如判断对象类型）、多值匹配（多个值对应同一逻辑）。

3. 变量赋值或函数返回值场景：需要根据条件动态赋值变量，或函数逻辑为多分支判断并返回结果的场景，可利用 when 表达式的返回值特性简化代码。

4. 复杂条件嵌套场景：存在多层 if-else 嵌套时，用无参数 when 可将嵌套拉平，提升可读性。

#### 5.2.2. 避坑点

* 避免遗漏 else 分支：除了编译器能确定所有情况都已覆盖的场景（如枚举匹配），建议加上 else 分支处理未覆盖的情况，避免运行时异常。

* 不要过度复杂单个分支：若某个分支的执行逻辑过于复杂（如包含大量循环、条件判断），应将其抽离为单独的函数，避免 when 表达式臃肿不堪，影响可读性。

* 区分带参数与无参数场景：当各分支有共同的匹配参数时，用带参数 when；当各分支条件无共同参数（为独立布尔表达式）时，用无参数 when，不要混用。

* 注意智能类型转换的范围：is 匹配的智能类型转换仅在当前分支内有效，若需在分支外使用转换后的类型，需手动定义变量。

### 5.3. 代码简洁性提升技巧（简化匹配逻辑、提高可读性）

* 利用单表达式函数简化：当函数逻辑仅为 when 表达式时，用单表达式函数写法（省略花括号和 return），代码更简洁。
  
  例如：fun getLevel(score: Int) = when (score) {...}

* 合并重复逻辑的分支：多个匹配条件对应相同逻辑时，用逗号分隔合并为一个分支，避免代码重复。例如: 1,2,3 -> "春季"

* 优先处理简单或高频分支：将简单的分支或高频匹配的分支放在前面，复杂分支放在后面，提升代码执行效率和可读性。

* 用常量或枚举优化匹配条件：避免在匹配条件中写字面量（如魔法数字、魔法字符串），改用常量或枚举，提升代码可维护性。例如：用 UserLevel.ADMIN 替代 "admin"。

* 无参数 when 的条件排序：无参数 when 的分支按“异常场景->普通场景->默认场景”排序，符合日常逻辑习惯，便于理解。(compose 中观察 livedate 或 flow 然后显示错误信息或正常页面的代码就是这样)

when 表达式是 Kotlin 中最具代表性的语法特性之一，它的灵活性和简洁性能极大提升开发效率和代码质量。掌握 when 的各种匹配方式，并在何时的场景灵活运用，能让你的 Kotlin 代码更优雅、更易读、更易维护。建议在开发中主动用 when 替代 Switch 和复杂的 if-else，逐步养成简洁的编码习惯。

## 6. 坑的正确绕法

最常见的坑是 when 作为表达式必须穷举，缺 else 编译报错，但对非密封类型穷举只是形式完备。表现有两面：好的一面是编译器强制不全（这正是 sealed 的价值）；坏的一面是对普通类写了 else -> {} 空分支后，编译器不再报错，而运行期落进空分支静默什么都不做。这比编译报错危险得多 -- 问题被推迟到线上。修法是：when 用于业务分支时优先匹配 sealed 状态并删掉 else；确实需要对未知情况兜底时，else 分支里要显式抛异常或记日志，不要写空实现。

其次是 when 里写复杂条件做线性匹配，条件顺序有依赖。表现是把 in 1..9 写在 in 1..5 -> 高级 之后，1~5 会先命中第一个分支，第二个分支形同虚设。根因是 when 是顺序求值，不是最长匹配也不是最具体匹配。修法是把范围更窄的分支放前面，或者该用显式的条件表达式让顺序变成显式可读的逻辑。

还有一个更隐蔽的坑：把 when 的 is 分支用在可空值上，智能转换后仍可能为空。表现是 when(v) { is String -> v.length } 在 v 被判定为 String 时安全，但把 v:Any? 传进来时 null 分支缺失导致 else 兜底，业务上 "null 应该走默认值"这个语义被淹没。另一个更常见的坑是 is 判断不匹配 null -- is String 对 null 返回 false，所以 null 必须单独写 null -> 分支。Android 上从 Intent 取数据、从 Bundle 取值时最容易踩这个。

```kotlin
// 1) 五种分支形态一次看全
fun describe(code: Int, name: String?, level: Int): String = when {

    code in 200..299 -> "成功"                      // 区间
    code in listOf(408, 502, 503) -> "可重试"      // 集合成员
    code !in 100..599 -> "非法状态码"               // 任意条件
    else -> "未知"
}

fun typeName(v: Any?): String = when (v) {
           // 类型 + null
    null -> "空"
    is Int -> "整型 ${v * 2}"                      // 分支内智能转换生效
    is String -> "文本 ${v.length}"
    is List<*> -> "列表 ${v.size}"
    else -> "其他"
}

// 2) 作为表达式：每条路径都必须有值
val label: String = when (level) {
                     // 不写 else 也能过
    1 -> "初级"
    2 -> "中级"
    3 -> "高级"
    else -> "未知"
}

// 3) 顺序依赖：窄的分支必须放前面
fun grade(score: Int) = when {

    score >= 90 -> "A"                              // 放前面
    score >= 60 -> "B"
    else -> "C"
}

// 4) sealed 状态 + 不写 else：漏分支编译报错
sealed interface NetState {
    data object Idle : NetState; data class Done(val n: Int) : NetState }
fun render(s: NetState) = when (s) {
                   // 新增状态忘了补 → 编译失败
    NetState.Idle -> "加载中"
    is NetState.Done -> "完成 ${s.n}"
}

// 5) 反例：else 空实现，问题被推迟到线上
fun renderSilent(s: NetState): String = when (s) {

    NetState.Idle -> "加载中"
    is NetState.Done -> "完成 ${s.n}"
    else -> ""                                       // 静默吞掉
}

// 6) when 不穿透，无需 break
fun kind(n: Number) = when (n) {

    1 -> "一"        // 命中即返回，不会继续往下匹配
    2 -> "二"
    else -> "多"
}
```

第一处，第一段把五种分支形态写在两个函数里，区间/集合/任意条件/类型/_null 都覆盖到；第二处，render 与 renderSilent 的一字之差 -- 去掉 else 编译期强制补全，加上空 else 就把问题推给线上；第三处，grade 里用 when{} 无参形式表达从高到低的顺序依赖，注视明确标出 “窄的放前面”。

## 7. 面试问题

* when 与 if-else 链是什么区别？
  
  1. when 支持值/多值/区间/类型/集合多形态匹配，if 只能写布尔条件
  
  2. when 对于 menu、sealed class、Boolean 有穷举性检查，if 没有
  
  什么时候用 if？如果条件之间有复杂逻辑组合（a || b && c），if 反而更清楚。

* when 的集合成员匹配是 o(n) 还是 o(1)？
  
  取决于集合自己，如果是 hashSet 按哈希找，平均是 o(1)，如果是 treeset 按树找，则是 o(log n)，如果是 list、array 则是 o(n)。如果是常量，编译器可以编译成 switch，不经过 contains。
  
  vararg 是函数参数上的修饰符，表示这个参数可以接收任意多个值。函数内部把它当数组使用。listof、arratof、setof 的参数都是 vararg。

* sealed 的穷举性检查是怎么实现的？
  
  编译器做的是先列出这个 sealed 类型所有直接的非 sealed 子类型，再看 when 的分支有没有把他们都盖住。中间还套着 sealed 时，会先把那一层展开，缺任何一项又没有 else，就报错。从 Kotlin 1.7 起，当表达式和当语句用都做这个检查。
  
  普通类的子类可以写在任何地方，编译器列不出完整名单。当表达式用时必须有 else；当语句用时可以不写，没匹配上就什么都不做。

* 使用中遇到过什么问题？
  
  案例一：网络状态用 sealed 建模，但 when 写了 else -> {}，新增一种 Timeout 状态后没补分支，页面卡在空白；修复为删掉 else，让编译器把遗漏点暴露出来。
  
  案例二：is 分支漏了 null，接口返回空值时走了“其他”分支，展示的是类型名而不是默认文案。

再补一个工程上值得讲清的一点：when 在 Android 上最实用的三个位置。

1. 状态渲染 - 把 selaed 的 UiState 映射成界面动作（显示骨架屏/显示列表/显示错误），集中在一处便于审查；

2. 错误码映射 - when（code）{in 500..599 -> "服务异常" ...} 给用户可读文案，同时补日志；

3. 多类型分支的差异化处理 - 比如分享渠道返回不同类型时用 is 分支分别处理。共同点是把“一堆 if 散在各处”收敛成一个可枚举的决策点，这本身就是价值。

面试里能说出“when 的收益不只是语法糖，而是把决策集中成一处可审查的结构”，说明理解到位了。

## 参考内容

* [4.Kotlin 流程控制：强大的 when 表达式：取代 SwitchKotlin的when表达式正是为解决Java - 掘金](https://juejin.cn/post/7574601160981659663)
* [第075篇 when 表达式：比 switch 强在哪里-阿里云开发者社区](https://developer.aliyun.com/article/1768755)
