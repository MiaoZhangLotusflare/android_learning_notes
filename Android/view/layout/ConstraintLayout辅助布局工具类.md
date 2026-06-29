# ConstraintLayout辅助布局工具类

![](image/ConstraintLayout.png)

> **辅助布局工具类**
> 
> 1. GuideLine
>    
>    参考线，帮助定位
> 
> 2. Barrier
>    
>    屏障，将 a和 b 控件组合（a和b横向排列），设置 c 显示在 a 和 b 的右边。
> 
> 3. Group
>    
>    组，控制组内控件同时显示或隐藏。
> 
> 4. Placeholder
>    
>    占位符，在布局中占好位置，通过app:content="" 或 setContent() 来让某个控件移动到该占位符。
> 
> 5. Flow
>    
>    Flow 流式布局组件，批量管理多个 view，实现自动换行/换列的标签流，多行多列排版。
>    Flow 是虚拟布局，不会将视图添加到视图层次结构中，仅仅引用其他视图来辅助它们在布局系统中完成各自的布局功能，
>    **app:flow_wrapMode** 设置排列方式：
>    
>    * none：默认值，所有引用的 view 形成一条链，水平局中，超出屏幕两侧的 view 不可见
>    
>    * chian：所引用的 view 形成一条链，超出部分会自动换行，同行的 view 会评分宽度
>    
>    * aligned：所引用的 view 形成一条链，但 view 会在同行同列。
>    
>    当 flow_wrap 属性为 aligned 或 chain 时，通过 **flow_maxElementsWrap** 属性控制每行最大的子 view 数量。
>    
>    **app: flow_verticalAlign** 设置对齐约束，top 顶对齐，bottom 底对齐，center 中心对齐，baseline 基线对齐
>    **app:flow_horizantalAlign** 设置对齐约束，start 开始对齐，end 结束对齐，center 中心对齐
> 
> 6. Layer
>    
>    Layer 是一个约束助手，常用来增加背景或共同动画，Layer 在布局期间会调整大小，其大小会根据其引用的所有视图进行调整。

ConstraintLayout 为了解决嵌套问题还提供了一系列的辅助控件帮助开发者布局，这些工具十分的方便，在日常开发工作中也是使用的非常频繁。

这类工具主要用于辅助布局，自身通常不可见。虽然是随 ConstraintLayout 库一起提供的，但技术上可以被任何 ViewGroup 使用。

### 1. Guideline（参考线）

Guideline 是一条参考线，可以帮助开发者进行辅助定位，并且实际上它并不会真正显示在布局中，像是数据几何中的辅助线一样，使用起来非常方便，出场率很高，Guideline 也可以用来做一些百分比分割之类的需求，有着很好的屏幕适配效果，Guideline 有水平和垂直方向之分，位置可以使用针对父级的百分比或者针对父级位置的距离

```xml
android:orientation="horizontal|vertical"  辅助线的对齐方式
app:layout_constraintGuide_percent="0-1"   距离父级宽度或高度的百分比(小数形式)
app:layout_constraintGuide_begin=""        距离父级起始位置的距离(左侧或顶部)
app:layout_constraintGuide_end=""          距离父级结束位置的距离(右侧或底部)
```

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1b44LZFQZGdfoOQGX7mI9JQhmg6e219ymbNbDMT5Z7n8KXpoWyheAKQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#DAF3FE"
    tools:context=".MainActivity"
    tools:ignore="HardcodedText">

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/Guideline"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        app:layout_constraintGuide_percent="0.5" />

    <TextView
        android:id="@+id/A"
        android:layout_width="120dp"
        android:layout_height="80dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="A"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="@id/Guideline" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

上图中设置了一条水平方向位置在父级垂直方向 0.5(50%) 的 Guideline，控件 A 的顶部依赖于 Guideline，这样无论布局如何更改，Guideline 的位置始终都会是父级垂直方向 50% 的位置，控件 A 的位置也不会偏离预设。

### 2. Barrier（屏障）

这个 Barrier 和 Guideline 一样，也不会实际出现在布局中，它的作用如同其名，形成一个屏蔽、障碍，使用也非常多。

当创建布局时，有时会遇到布局可以根据本地化而更改的情况。这里借助有一个非常简单的例子：

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT11fP48nZicMEZxyGyk8M6HXhyCQDbicJqhJ0icFhGMdxsYicGVubTdbPsZw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

这里有三个文本视图：左边的 textView1 和 textView2；右边的 textView3。textView3 被限制在 textView1 的末尾，这工作得很好 -- 它完全根据我们需要来定位和大小 textView3。

然而，如果我们需要支持多种语言，事情会变得更加复杂。如果我们添加德语翻译，那么我们就会遇到一个问题，因为在英文版本中，textView1 中的文本比 textView2 中的文本长，而在德语中，textView2 的文本比 textView2 长：

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1ISGpqQtv3FvrvXxflVc6d6Ericx6dVkCsnEwn6lNepoicdfpn88VJ1cg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

这样的问题在于 textView3 仍然是相对于 textView1 的，所以 textView2 直接插入了 textView3 中。在设计视图里看起来更明显（白色背景的那个）。比较直接的解决办法是使用 TabLayout，或者把 textView1 & textView2 包裹在一个垂直的，android:layout_width="wrap_content" 的 LinearLayout 中。然后让 textView3 约束在这个 LinearLayout 的后面。但是有更好的办法：Barriers。Barriers 的配置属性如下：

```xml
<!--  用于控制Barrier相对于给定的View的位置  -->
app:barrierDirection="top|bottom|left|right|start|end"  

<!--  取值是要依赖的控件的id，Barrier将会使用ids中最大的一个的宽/高作为自己的位置  -->
app:constraint_referenced_ids="id,id"
```

修改过后的代码如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/textView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:layout_marginTop="16dp"
        android:text="@string/warehouse"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/textView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:layout_marginTop="8dp"
        android:text="@string/hospital"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textView1" />

    <androidx.constraintlayout.widget.Barrier
        android:id="@+id/barrier7"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="end"
        app:constraint_referenced_ids="textView2,textView1" />

    <TextView
        android:id="@+id/textView3"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="8dp"
        android:text="@string/lorem_ipsum"
        app:layout_constraintStart_toEndOf="@+id/barrier7"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1qTMhA3fD6IWa0hIZbKda3v0raVfHGAZ6JLGjTVuPK0rU1o6GRiba1lg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

为了看到整体的效果，可以切换语言，此时你会看到 Barrier 会自动位于较宽的那个 textView 后面，也就间接让 textView3 也位于了正确的位置。

### 3. Group（组）

工作当中常常会有很多个控件同时隐藏或者显示的场景，传统做法要么是进行嵌套，对父布局进行隐藏或显示，要么就是一个一个设置，这显然都不是很好的办法，ConstraintLayout 中的 Group 就是来解决这个问题的。Group 的作用就是可以对一组控件同时隐藏或显示，没有其他的作用，它的属性如下：

```xml
app:constraint_referenced_ids="id,id"  加入组的控件id
```

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1nWJnzAbSeU1j9TV3u7lX6jic7LdHfOiaoSPzZ10nibe4oztpor27VsibrA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#DAF3FE"
    tools:context=".MainActivity"
    tools:ignore="HardcodedText">

    <TextView
        android:id="@+id/A"
        android:layout_width="100dp"
        android:layout_height="60dp"
        android:layout_marginTop="56dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="A"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.115"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
    <TextView
        android:id="@+id/B"
        android:layout_width="100dp"
        android:layout_height="60dp"
        android:layout_marginTop="280dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="B"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.758"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/C"
        android:layout_width="100dp"
        android:layout_height="60dp"
        android:layout_marginTop="164dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="C"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.437"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
    <androidx.constraintlayout.widget.Group
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:visibility="visible"
        app:constraint_referenced_ids="A,B,C" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

A、B、C 三个 view，受 Group 控件，当 Group 的 visibility 为 visible 时，它们搜是正常显示的，设置为 gone 时，它们都会隐藏：

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT152FEXHKRu7ZDkiagCKIxW9nU2K4ojl0be4QANJUBpwbtj2oqFeB4yug/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 4. Placeholder（占位符）

Placeholder 的作用就是占位，它可以在布局中占好位置，通过 app:content="" 属性，或者动态调用 setContent() 设置内容，来让某个控件移动到此占位符中

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1CNtEZ4aqsymkfHHiaFxyhJXkw28rCZrdp3ZhUtjubFpHsOKmGc4tbuw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#DAF3FE"
    tools:context=".MainActivity"
    tools:ignore="HardcodedText">

    <TextView
        android:id="@+id/A"
        android:layout_width="100dp"
        android:layout_height="60dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="A"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <androidx.constraintlayout.widget.Placeholder
        android:layout_width="100dp"
        android:layout_height="60dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

当我们设置 app:content="@+id/A" 或者调用 setContent() 时，控件 A 就会被移动到 placeholder 中，当然在布局中使用 app:content="" 显然就失去了它的作用。

### 5. Flow(流式虚拟布局)

Flow 是新增的虚拟辅助布局（VirtualLayout/ConstraintHelper），官方流式布局组件，用来批量管理多个 View，实现自动换行 / 换列的标签流、多行多列排版。可以使用它来根据应用程序中的动态尺寸（例如旋转时的屏幕宽度）构建布局。

Flow 是一种虚拟布局。在 ConstraintLayout 中，虚拟布局（Virtual layouts）作为 virtual view group 的角色参与约束和布局中，但是它们并不会作为视图添加到视图层次结构中，而是仅仅引用其他视图来辅助它们在布局系统中完成各自的布局功能。

下面使用动画来展示 Flow 创建多个链将布局元素充裕地填充一整行：

![](https://mmbiz.qpic.cn/mmbiz_gif/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1WeBr0VAzjnofJzYBXNSUFwZronRzxxZlsxc08gib4uquicehtxzoj8Aw/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1TojhfaXQOXgdOaUOibOUVsYHFdiaXt85nybRIPjYrGaltWgMVcakia5ug/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#DAF3FE"
    tools:context=".MainActivity"
    tools:ignore="HardcodedText">

    <TextView
        android:id="@+id/A"
        android:layout_width="60dp"
        android:layout_height="60dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="A"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/B"
        android:layout_width="60dp"
        android:layout_height="60dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="B"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/C"
        android:layout_width="60dp"
        android:layout_height="60dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="C"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/D"
        android:layout_width="60dp"
        android:layout_height="60dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="D"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/E"
        android:layout_width="60dp"
        android:layout_height="60dp"
        android:background="@drawable/tv_bg"
        android:gravity="center"
        android:text="E"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold" />

    <androidx.constraintlayout.helper.widget.Flow
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:constraint_referenced_ids="A,B,C,D,E"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

#### 5.1. 链约束

Flow 的 constraint_references_ids 关联的控件是没有设置约束的，这一点和普通的链是不一样的，这种排列方式是 Flow 的默认方式 none，可以使用 app:flow_wrapMode ="" 属性来设置排列方式，并且还可以使用 flow_horizontalGap 和 flow_vertialGap 分别设置两个 view 在水平和垂直方向的间隔，下面再添加几个控件来展示三种排列方式：

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1vlIQWMHq0NT0cEFibqKTQ8upQkVBcuics8oo6v2X8InW0kaA2HlmhO7Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1FfV349C63YziaK5IrjzdkMc0bDqr91DRSLKxkyQgyhgwcWic42eSegNA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1Yh2zdFMIxy6oHwXXsEQNMD64u0CD26maF6mCgxIljtZbqoliaGjWPQA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

下面使用动画来展示三种效果的变化

![](https://mmbiz.qpic.cn/mmbiz_gif/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1XoVdwowG1k3RmeZ3vSbFriaeJuBGnsia8OJQeiczJJ4L1rR9YrGibSUt3g/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

当 flow_wrapMode 的值是 chain 或 aligned 时，还可以针对不同的链进行配置，具体属性如下：

```xml
app:flow_horizontalStyle="packed｜spread｜spread_inside"  所有水平链的配置
app:flow_verticalStyle="packed｜spread｜spread_inside"    所有垂直链的配置

app:flow_firstHorizontalStyle="packed｜spread｜spread_inside" 第一条水平链的配置，其他条不生效
app:flow_firstVerticalStyle="packed｜spread｜spread_inside"   第一条垂直链的配置，其他条不生效
app:flow_lastHorizontalStyle="packed｜spread｜spread_inside"  最后一条水平链的配置，其他条不生效 
app:flow_lastVerticalStyle="packed｜spread｜spread_inside"    最后一条垂直链的配置，其他条不生效
```

#### 5.2. 对齐约束

上面展示的都是相同大小的 view，那么不同大小 view 的对齐方式，Flow 也提供了相应的属性进行配置（flow_wrapMode="aligned"时，我试着没有效果）

```xml
<!--  top:顶对齐、bottom:底对齐、center:中心对齐、baseline:基线对齐  -->
app:flow_verticalAlign="top｜bottom｜center｜baseline"

<!--  start:开始对齐、end:结尾对齐、center:中心对齐  -->
app:flow_horizontalAlign="start|end|center"
```

使用 flow_verticalAlign 时，要求 orinentation 的方向是 horizontal，而使用 flow_horizontalAlign 时，要求 orientation 的方向是 vertical。

下面展示下各个效果：

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1XpP7MtPiaUswuk99v2F8mJVao8ClcjbR9fknKkwXpLJibiaTCVVI8dxlg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<androidx.constraintlayout.helper.widget.Flow
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal"
        app:constraint_referenced_ids="A,B,C,D,E,F,G,H,I,J"
        app:flow_verticalAlign="top"
        app:flow_wrapMode="chain"
        app:layout_constraintTop_toTopOf="parent" />
```

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1hSXmfCysKaJPk2F9vsa96Xf98KRViaEtPWEe229Otp3NSg3hrCDtAxQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<androidx.constraintlayout.helper.widget.Flow
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal"
        app:constraint_referenced_ids="A,B,C,D,E,F,G,H,I,J"
        app:flow_verticalAlign="bottom"
        app:flow_wrapMode="chain"
        app:layout_constraintTop_toTopOf="parent" />
```

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1icPahoM4UnVwT04X8VwXof2wFOt8NdtTW8A8YCHb1pZAVGNr8TOU25g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<androidx.constraintlayout.helper.widget.Flow
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal"
        app:constraint_referenced_ids="A,B,C,D,E,F,G,H,I,J"
        app:flow_verticalAlign="center"
        app:flow_wrapMode="chain"
        app:layout_constraintTop_toTopOf="parent" />
```

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1n3ib70h3tfZiavILGbbknw23GX5NtOicykuP2o5ZpwpEpG7vOqroSYTAQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<androidx.constraintlayout.helper.widget.Flow
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal"
        app:constraint_referenced_ids="A,B,C,D,E,F,G,H,I,J"
        app:flow_verticalAlign="baseline"
        app:flow_wrapMode="chain"
        app:layout_constraintTop_toTopOf="parent" />
```

#### 5.3. 数量约束

当 flow_wrapMode 属性为 aligned 和 chain 时，通过 flow_maxElementsWrap 属性控制每行最大的子 View 数量，例如我们设置为 flow_maxElementsWrap = 4，效果图如下：

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1Ebv1b2JCK1uWXFAiaRoDd2TdAb8k8NcFeW8BzbIQibNk8fXv8sDibjQ5g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 6.Layer（层布局）

Layer 继承自 ConstraintHelper，是一个约束助手，相对于 Flow 来说，Layer 的使用较为简单，常用来增加背景，或者共同动画，图层（Layer）在布局期间会调整大小，其大小会根据其引用的所有视图进行调整，代码的先后顺序也会决定着它的位置，如果代码在所有引用 view  最后面，那么它就会在所有 view 的最上面，反之则是最下面，在最上面的时候如果添加背景，就会把引用的 view 覆盖掉，下面展示下添加背景的例子

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1BKNg2Vx0wyuausu9MKB7ctKOPwtJOQRKFRMEzsZ4w1Qy5EzEx5Ek5Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#DAF3FE"
    tools:context=".MainActivity"
    tools:ignore="HardcodedText">

    <androidx.constraintlayout.helper.widget.Layer
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@drawable/common_rect_white_100_10"
        android:padding="10dp"
        app:constraint_referenced_ids="AndroidImg,NameTv" />

    <ImageView
        android:id="@+id/AndroidImg"
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:src="@drawable/android"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/NameTv"
        android:layout_width="100dp"
        android:layout_height="40dp"
        android:gravity="center"
        android:text="Android"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="@id/AndroidImg"
        app:layout_constraintStart_toStartOf="@id/AndroidImg"
        app:layout_constraintTop_toBottomOf="@id/AndroidImg" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

可以看到，当 Layer 的代码在所有引用 view 的上面时，效果是正常的，因为此时所有的 view 都在 Layer 的上面，下面来看一下 Layer 代码在最后面时的情况：

![](https://mmbiz.qpic.cn/mmbiz_png/v1LbPPWiaSt6Sf5UJpuDjpG03kD1YHMT1r78qBej2MFueyJh51A42Bryb5WzJbSw4g6IGYeGw0WmXceYc0ib3jkA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#DAF3FE"
    tools:context=".MainActivity"
    tools:ignore="HardcodedText">

    <ImageView
        android:id="@+id/AndroidImg"
        android:layout_width="200dp"
        android:layout_height="wrap_content"
        android:src="@drawable/android"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/NameTv"
        android:layout_width="100dp"
        android:layout_height="40dp"
        android:gravity="center"
        android:text="Android"
        android:textColor="@color/black"
        android:textSize="25sp"
        android:textStyle="bold"
        app:layout_constraintEnd_toEndOf="@id/AndroidImg"
        app:layout_constraintStart_toStartOf="@id/AndroidImg"
        app:layout_constraintTop_toBottomOf="@id/AndroidImg" />

    <androidx.constraintlayout.helper.widget.Layer
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@drawable/common_rect_white_100_10"
        android:padding="10dp"
        app:constraint_referenced_ids="AndroidImg,NameTv" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

可以看到，此时 Layer 已经把所有的 view 覆盖住了。

# 参考文章

1. [ConstraintLayout 用法全解析](jianshu.com/p/502127a493fb)
2. [史上最全ConstraintLayout使用详解！（建议收藏）](https://mp.weixin.qq.com/s/HDbPU-fej0L_YtMk41zeYg)
