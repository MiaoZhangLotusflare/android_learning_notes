# Android 知识结构图

|    Android    | Java  | 软件基础 |   开源框架   |    技术栈     |      计划       | 读书笔记 |
| :-----------: | :---: | :------: | :----------: | :-----------: | :-------------: | :------: |
| :monkey_face: | :tea: | :house:  | :open_hands: | :atom_symbol: | :bookmark_tabs: | :books:  |

# :monkey_face: Android

## :one: 四大组件

* Activity
  * [Activity 的生命周期](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.md)
  * [验证 Activity 生命周期的问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81Activity%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%9A%84%E9%97%AE%E9%A2%98.md)
  * [Activity 的启动模式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%90%AF%E5%8A%A8%E6%A8%A1%E5%BC%8F.md)
  * [验证 Activity 四种 launchMode ](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81Activity%E5%9B%9B%E7%A7%8DlaunchMode.md)
  * [验证 onNewIntent 方法](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81%20onNewIntent%20%E6%96%B9%E6%B3%95.md)
  * [验证 Intent 的 flags ](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81Intent%E7%9A%84flags.md)
  * [关于 Intent 的全部 flags 介绍](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E5%85%B3%E4%BA%8EIntent%E7%9A%84%E5%85%A8%E9%83%A8flags%E4%BB%8B%E7%BB%8D.md)
  * [Activity 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E4%BD%BF%E7%94%A8.md)
  * [Activity 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98.md)
  * [Activity 的状态](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%9B%9B%E7%A7%8D%E7%8A%B6%E6%80%81.md)
* Service
  * [Service 的基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Service/Service%E7%9A%84%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md)
  * [Service 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Service/Service%E7%9A%84%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98.md)
* ContentProvider
  * [ContentProvider 的知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider%E7%9A%84%E7%9F%A5%E8%AF%86.md)
  * [ContentProvider 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider%E7%9A%84%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98.md)
* BroadcastReceiver
  * [BroadcaseReceiver 的基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/BroadcastReceiver/BroadcastRecevier的基础知识.md)
  * [BroadcaseReceiver 的实现原理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/BroadcastReceiver/BroadcastRecevier的实现原理.md)
  * [BroadcaseReceiver 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/BroadcastReceiver/BroadcastRecevier的常见问题.md)
* [守护进程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/守护进程.md)
* [IntentFilter 的匹配规则](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/IntentFilter的匹配规则.md)

## :two: 视图

* Fragment
  * [Fragment 的基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/fragment/Fragment的基础知识.md)
  * [Fragment 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/fragment/Fragment的使用.md)
  
* 列表控件
  * ListView
    * [ListView 原理解析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/ListView/ListView%E5%8E%9F%E7%90%86%E8%A7%A3%E6%9E%90.md)
    * [Android中ListView的几种常见优化方式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/ListView/Android%E4%B8%ADListView%E7%9A%84%E5%87%A0%E7%A7%8D%E5%B8%B8%E8%A7%81%E4%BC%98%E5%8C%96%E6%96%B9%E5%BC%8F.md)
  * RecyclerView
    * [RecyclerView的getLayoutPosition和getAdapterPosition](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclerView%E7%9A%84getLayoutPosition%E5%92%8CgetAdapterPosition.md)
    * [Recycler 优化技术](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclewView%E4%BC%98%E5%8C%96%E6%8A%80%E6%9C%AF.md)
    * [RecyclerView 的使用总结以及常见问题解决方案](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclewView%E7%9A%84%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93%E4%BB%A5%E5%8F%8A%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88.md)
    * RecyclerView 原理解析
      * [RecyclerView 的基本设计结构](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclewView%E7%9A%84%E5%9F%BA%E6%9C%AC%E8%AE%BE%E8%AE%A1%E7%BB%93%E6%9E%84.md)
      * [RecyclerView 的刷新机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclewView%E5%88%B7%E6%96%B0%E6%9C%BA%E5%88%B6.md)
      * [RecyclerView 的复用机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclewView%E5%A4%8D%E7%94%A8%E6%9C%BA%E5%88%B6.md)
      * [RecyclerView 动画源码浅析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclewView%E5%8A%A8%E7%94%BB%E6%BA%90%E7%A0%81%E6%B5%85%E6%9E%90.md)
      * [Recycler 原理分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/%E5%88%97%E8%A1%A8/RecyclerView/RecyclewView%E5%8E%9F%E7%90%86%E5%88%86%E6%9E%90.md)
  * ListView 与 RecyclerVide 的区别
* View
  * 绘制
    * [Activity 的布局绘制过程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/Activity的布局绘制过程.md)
    * [View 绘制流程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/View绘制流程.md)
    * [视图状态与重绘流程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/视图状态与重绘流程.md) 
  * View 的事件分发机制
    * [View 事件分发机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/View事件分发机制.md)
    * [ViewGroup 事件分发机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/ViewGroup事件分发机制.md)
  * [自定义View的实现方式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/自定义View的实现方式.md)
  * [ConstraintLayout](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/ConstraintLayout.md)
  * [getDimensionxxx方法区别]
* View 应用
  * [Android实现卡片翻转的动画（翻牌动画）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/view%E7%9A%84%E5%AE%9E%E8%B7%B5/Android%E5%AE%9E%E7%8E%B0%E5%8D%A1%E7%89%87%E7%BF%BB%E8%BD%AC%E7%9A%84%E5%8A%A8%E7%94%BB%EF%BC%88%E7%BF%BB%E7%89%8C%E5%8A%A8%E7%94%BB%EF%BC%89.md)
  * [系统控件的常用使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/view%E7%9A%84%E5%AE%9E%E8%B7%B5/%E7%B3%BB%E7%BB%9F%E6%8E%A7%E4%BB%B6%E7%9A%84%E5%B8%B8%E7%94%A8%E4%BD%BF%E7%94%A8.md) 
  * [GridView嵌套在ScrollView里展示不全](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/GridView%E5%B5%8C%E5%A5%97%E5%9C%A8ScrollView%E9%87%8C%E5%B1%95%E7%A4%BA%E4%B8%8D%E5%85%A8.md)
  * [WebView 遇到的 Exception]
  * [EditText 的 imeOptions 与多行输入的问题]

## :three: Android framework

* [Activity 的启动流程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B.md)
* [ActivityManagerService](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ActivityManagerService.md)
* [WindowManagerService](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/WindowManagerService.md)
* [PackageManagerService之启动解析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/PackageManagerService之启动解析.md)
* [ContentProvider 之文件储存](https://blog.csdn.net/kaiwii/article/details/7780593)
* [ContentProvider 运行过程源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider运行过程源码分析.md)
* [ContentProvider 的共享数据更新通知机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider的共享数据更新通知机制.md)
* 安装
  * 

## :four: 开发框架

* [设计模式选择](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E9%80%89%E6%8B%A9.md)
* [MVVM 设计模式与 ViewModel、LiveData](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/MVVM%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8EViewModel%E4%B8%8ELiveData.md)
* [MutableLiveData 详解](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/MutableLiveData%E8%AF%A6%E8%A7%A3.md)
* [LiveDataBus 使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/LiveDataBus%E4%BD%BF%E7%94%A8.md)
* [MVI](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/MVI.md)
* 组件化 
  * [组件化](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E7%BB%84%E4%BB%B6%E5%8C%96/%E7%BB%84%E4%BB%B6%E5%8C%96.md)

## :five: 优化

* [ANR 相关知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/ANR%E7%9B%B8%E5%85%B3%E7%9F%A5%E8%AF%86.md)
* [发生 ANR 条件的源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/%E5%8F%91%E7%94%9F%20ANR%20%E6%9D%A1%E4%BB%B6%E7%9A%84%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90.md)
* [性能优化](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96.md)
* [Android 内存知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/Android内存知识.md)
* [内存泄漏](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/内存泄漏.md)
* [内存溢出](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/内存溢出.md)
* [布局优化之 include](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/布局优化之include.md)
* [布局优化之 merge](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/布局优化之merge.md)
* [布局优化之 ViewStub](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/布局优化之ViewStub.md)
* [性能优化工具](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%E5%B7%A5%E5%85%B7.md)

## :six: 适配

* [Android 4.4.4 的 setResult 失效的问题适配](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/adaptation/Android4.4.4%E7%9A%84setResult%E5%A4%B1%E6%95%88%E7%9A%84%E9%97%AE%E9%A2%98%E9%80%82%E9%85%8D.md)
* [Android 4.4.4 支持分包后找不到类的问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/adaptation/Android%204.4.4支持分包后找不到类的问题.md)

## :seven: 数据存储

* [缓存机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/cache/缓存机制.md)
* [LruCache知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/cache/LruCache知识.md)
* [本地缓存ACache](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/cache/%E6%9C%AC%E5%9C%B0%E7%BC%93%E5%AD%98ACache.md)

## :eight: 进程与线程

* Handler
  * [Handler 机制分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/Handler机制分析.md)
  * [Handler 的延伸](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/Handler的延伸.md)
  * [ThreadLocal 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/ThreadLocal知识.md)
  * [AsyncTask 的知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/AysncTask的知识.md)
* AIDL
  * [AIDL 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/IPC/AIDL/AIDL%E7%9A%84%E4%BD%BF%E7%94%A8.md)
  * [AIDL 的工作原理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/IPC/AIDL/AIDL%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.md)

## :nine: 存储

* [安卓存储空间](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E5%AE%89%E5%8D%93%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.md)
* [安卓 RAM 与 ROM](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E5%AE%89%E5%8D%93RAM%E4%B8%8EROM.md)
* [Android 存储空间监控](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/Android%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4%E7%9B%91%E6%8E%A7.md)
* [空间清理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E7%A9%BA%E9%97%B4%E6%B8%85%E7%90%86.md)
* [应用大小计算](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E7%A9%BA%E9%97%B4%E5%A4%A7%E5%B0%8F%E8%AE%A1%E7%AE%97.md)

## :keycap_ten: 其他

* 单元测试
  * [Android单元测试之一：基本概念](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E4%B8%80%EF%BC%9A%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5.md)
  * [Android单元测试之二：本地测试](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E4%BA%8C%EF%BC%9A%E6%9C%AC%E5%9C%B0%E6%B5%8B%E8%AF%95.md)
  * [Android单元测试之三：使用模拟框架模拟依赖](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E4%B8%89%EF%BC%9A%E4%BD%BF%E7%94%A8%E6%A8%A1%E6%8B%9F%E6%A1%86%E6%9E%B6%E6%A8%A1%E6%8B%9F%E4%BE%9D%E8%B5%96.md)
  * [Android单元测试之四：仪器化测试](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E5%9B%9B%EF%BC%9A%E4%BB%AA%E5%99%A8%E5%8C%96%E6%B5%8B%E8%AF%95.md)
* [api 与 implementation 的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/gradle/api%E4%B8%8Eimplementation%E7%9A%84%E5%8C%BA%E5%88%AB.md)
* [多渠道打包](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/Software/%E5%A4%9A%E6%B8%A0%E9%81%93%E6%89%93%E5%8C%85.md)
* [jar 包和 aar 包的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/jar%E5%8C%85%E4%B8%8Eaar%E5%8C%85%E7%9A%84%E5%8C%BA%E5%88%AB.md)
* [日常问题记录](https://github.com/ZhangMiao147/android_learning_notes/tree/master/Android/question)
* [Android 中 Home 键的监听](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/Android中Home键的监听.md)
* [shape 布局文件的替换方案](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/shape布局文件的替换方案.md)
* [Android 编码规范](https://github.com/ZhangMiao147/android_learning_notes/tree/master/Android/Android编码规范)
* [uses-permission 和 permission 区别及使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/uses-permission%E5%92%8Cpermission%E5%8C%BA%E5%88%AB%E5%8F%8A%E4%BD%BF%E7%94%A8.md)
* [类加载器 ClassLoader](https://github.com/ZhangMiao147/android_learning_notes/tree/master/Android/other/classLoader)
* https://www.jianshu.com/p/d182479c7a44 - @CallSuper 注解

# :tea: Java

## :one: 基础

* [Java 泛型](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/基础/Java泛型.md)

* [Java 代理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/基础/Java代理.md)

* [Java 反射](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/基础/Java反射.md)

  * [在Java反射中Class.forName和ClassLoader的区别.md](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/基础/在Java反射中Class.forName和ClassLoader的区别.md)

* [Java 注解](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/基础/Java注解.md)

* IO

  * NIO

* Java 中字符串 String switch 的实现原理

  [Java中字符串String Switch的实现原理](https://blog.csdn.net/MOLIILOM/article/details/51166697)

* Java 线程的创建

* Java 数据类型

* java 读写文件的几种方式

* Java 注解

  [Java 注解（Annotation）](https://www.runoob.com/w3cnote/java-annotation.html)

* Vector

* ConcurrentModificationException 异常

* 内存模型

* [Long 的 parseLong 与 valueof 的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Long%E7%9A%84parseLong%E4%B8%8Evalueof%E7%9A%84%E5%8C%BA%E5%88%AB.md)

## :two: 集合

* [Collection 与 Collections](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Colletion%E4%B8%8EColletions.md)

* List
  * [ArrayList 与 LinkedList](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/ArrayList%E4%B8%8ELinkedList.md)
  * SkipList
  * 线程安全
  * [list的remove问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/list%E7%9A%84remove%E9%97%AE%E9%A2%98.md)
* Map
  * [HashMap](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Map/HashMap.md)
  * [HashMap 不是线程安全的分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Map/HashMap不是线程安全的分析.md)
  * [HashMap 和 HashTable 的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Map/HashMap和HashTable的区别.md)
  * [ConcurrentHashMap](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Map/ConcurrentHashMap.md)
  * [JDK1.7ConcurrentHashMap的源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Map/JDK1.7ConcurrentHashMap的源码分析.md)
  * [JDK1.8ConcurrentHashMap的源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Map/JDK1.8ConcurrentHashMap的源码分析.md)
  * LinkedHashMap
  * TreeMap
* Tree
* Queue
  * [Queue的add与offer的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/Queue%E7%9A%84add%E4%B8%8Eoffer%E7%9A%84%E5%8C%BA%E5%88%AB.md)
  * PriorityQueue
  * LinkedList
  * ArrayDeque
  * 线程安全
* Stack

## :three: 线程

* [AtomicBoolean](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/%E7%BA%BF%E7%A8%8B/AtomicBoolean.md)
* [Executor、Executors、ExecutorService 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/%E7%BA%BF%E7%A8%8B/Executor%E3%80%81Executors%E3%80%81ExecrtorService%E7%9F%A5%E8%AF%86.md)
* [同步的知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/同步的知识.md)
* [volatile 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/volatile知识.md)
* [synchronized 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/Synchronized知识.md)
* [锁的知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/锁的知识.md)
* [Java 中常见的锁](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/Java中常见的锁.md)
* [死锁](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/死锁.md)
* [ReentrantLock 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/ReentrantLock知识.md)
* [Condition 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/Condition知识.md)
* [使用synchronzied、wait、notifyAll实现生产者-消费者模式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/使用synchronzied-wait-notifyAll实现生产者-消费者模式.md)
* [使用信号量实现生产者-消费者模式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/使用信号量实现生产者-消费者模式.md)
* [使用管程实现生产者-消费者模式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/使用管程实现生产者-消费者模式.md)
* [哲学家就餐问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/哲学家就餐问题.md)
* [读者写者问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/线程/读者写者问题.md)
* Semaphore
* countdownlatch
* 线程池
* 线程

## :four: JVM

* GCRoot
* 类加载
* 类的初始化实例化

## :five: 其他

* [String、StringBuffer 与 StringBuilder 的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Java/DataStructure/String%E3%80%81StringBuilder%E4%B8%8EStringBuffer%E7%9A%84%E5%8C%BA%E5%88%AB.md)
* hashCode
* treeMap
* String
* 动态代理类 Proxy
* PriorityQueue
* transient
* CopyOnWriteArrayList
* SortedMap / TreeMap
* ArrayBlockingQueue 阻塞队列

# :house: 软件基础

## :one: 操作系统

* [操作系统概述](https://github.com/ZhangMiao147/android_learning_notes/blob/master/system/操作系统概述.md)
* [进程与线程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/system/进程与线程.md)
* [死锁](https://github.com/ZhangMiao147/android_learning_notes/blob/master/system/死锁.md)
* [内存管理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/system/内存管理.md)
* [设备管理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/system/设备管理.md)
* [文件管理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/system/文件管理.md)
* [作业管理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/system/作业管理.md)

## :two: 网络

* [计算机网络基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/计算机网络基础知识.md)
* [TCP 和 UDP](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/TCP与UDP/TCP和UDP.md)
* [TCP 粘包](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/TCP与UDP/TCP粘包.md)
* [Socket 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/Socket/Socket知识.md)
* [Socket 常用函数接口详解](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/Socket/Socket常用函数接口详解.md)
* [HTTP](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/HTTP/HTTP.md)
* [HTTPS](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/HTTPS/HTTPS.md)
* [IP 协议](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/IP/IP协议.md)
* [IPv4 和 IPv6](https://github.com/ZhangMiao147/android_learning_notes/blob/master/network/IP/IPv4和IPv6.md)
* KCP

## :three: 数据结构与算法

* 数据结构
  * [线性表顺序存储](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/线性表顺序存储.md) 
  * [线性表链式存储](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/线性表链式存储.md)
  * [堆](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/堆.md)
  * [Set 集合](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/Set集合.md)
  * 图
  * 并查集
  * 栈
  * [树](https://github.com/ZhangMiao147/android_learning_notes/tree/master/DataStructure/数据结构/树)
    * [树的基本知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/树的基本知识.md)
    * [二叉树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/二叉树.md)
    * [二分搜索树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/二分搜索树.md)
    * [平衡二叉树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/平衡二叉树.md)
    * [线段树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/线段树.md)
    * [字典树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/字典树.md)
    * [Huffman 树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/Huffman树.md)
    * [红黑树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/红黑树.md)
    * [B 树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/B树.md)
    * [B+ 树](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/数据结构/树/B%2B树.md)
* 算法
  * [递归](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/递归.md)
  * 回溯算法
  * [分治算法](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/分治算法.md)
  * [分支定界法](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/分支定界法.md)
  * [贪心算法](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/贪心算法.md)
  * [动态规划（Dynamic Programming）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/动态规划.md)
  * [查找算法](https://github.com/ZhangMiao147/android_learning_notes/tree/master/DataStructure/算法/查找算法)
    * 查找算法知识
    * [顺序查找](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/顺序查找.md)
    * [二分查找](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/二分查找.md)
    * [插值查找](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/插值查找.md)
    * [斐波那契查找](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/斐波那契查找.md)
    * [二叉树查找](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/二叉树查找.md)
    * [平衡查找树之2-3查找树（2-3 Tree）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/平衡查找树之2-3查找树.md)
    * [平衡查找树之红黑树（Red-Black Tree）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/平衡查找树之红黑树.md)
    * [B 树和 B+ 树（B Tree/B+ 树）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/B树和B%2B树.md)
    * [分块查找](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/分块查找.md)
    * [哈希查找](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/查找算法/哈希查找.md)
  * [排序算法](https://github.com/ZhangMiao147/android_learning_notes/tree/master/DataStructure/算法/排序算法)
    * 排序算法总结
    * [直接插入排序](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/排序算法/直接插入排序.md)
    * [希尔排序](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/排序算法/希尔排序.md)
    * [简单选择排序](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/排序算法/简单选择排序.md)
    * [堆排序](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/排序算法/堆排序.md)
    * [冒泡排序](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/排序算法/冒泡排序.md)
    * 快速排序
    * [归并排序](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/排序算法/归并排序.md)
    * [基数排序](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DataStructure/算法/排序算法/基数排序.md)

## :four: 设计模式

* [UML类图](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/UML%E7%B1%BB%E5%9B%BE.md)
* 创建型模式
  * [简单工厂模式（Simple Factory）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/%E7%AE%80%E5%8D%95%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F.md)
  * [工厂方法模式（Factory Method）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/%E5%B7%A5%E5%8E%82%E6%96%B9%E6%B3%95%E6%A8%A1%E5%BC%8F.md)
  * [抽象工厂模式（Abstract Method）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/%E6%8A%BD%E8%B1%A1%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F.md)
  * [单例模式（Singleton）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F.md)
* 结构型模式
  * [适配器模式（Adapter）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F.md)
  * [代理模式（Proxy）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F.md)
* 行为型模式
  * [职责链模式（Chain of Responsibility）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/责任链模式.md)
  * [观察者模式（Observer）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/DesignPatterns/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F.md)

## :five: 数据库



# :open_hands: ​开源框架

## :one: 异步处理

* [RxJava](https://github.com/ZhangMiao147/android_learning_notes/tree/master/OpenSourceLibrary/RxJava)
  * [RxJava 1 源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/RxJava/RxJava1源码分析.md)
  * [RxJava 1 源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/RxJava/RxJava1源码分析.md)
  * [RxJava 1 操作符源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/RxJava/RxJava1操作符源码分析.md)
  * [RxJava 2 与 RxJava 1 的对比](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/RxJava/RxJava2与RxJava1的对比.md)
  * [RxJava 2 背压源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/RxJava/RxJava2背压源码分析.md)
* RxAndroid
* [EventBus](https://github.com/ZhangMiao147/android_learning_notes/tree/master/OpenSourceLibrary/EventBus)
  * [EventBus 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/EventBus/EventBus%E7%9A%84%E4%BD%BF%E7%94%A8.md)
  * [EventBus 的原理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/EventBus/EventBus%E7%9A%84%E5%8E%9F%E7%90%86.md)

## :two: 网络请求

* [OkHttp](https://github.com/ZhangMiao147/android_learning_notes/tree/master/OpenSourceLibrary/OkHttp)
  * [OkHttp 基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/OkHttp/OkHttp基础知识.md)
  * [OkHttp 设置自定义拦截器](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/OkHttp/OkHttp设置自定义拦截器.md)
  * [OkHttp 源码解析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/OkHttp/OkHttp源码分析.md)
* [Retrofit](https://github.com/ZhangMiao147/android_learning_notes/tree/master/OpenSourceLibrary/Retrofit)
  * [Retrofit 官方文档翻译](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/Retrofit/Retrofit官方文档翻译.md)
  * [Retrofit 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/Retrofit/Retrofit知识.md)
  * [Retrofit 源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/Retrofit/Retrofit源码分析.md)

## :three: 图片加载

* Glide
* Picasso

## :four: 依赖注入

* ButterKnife
* Dagger2

## :five: 性能优化

* LeakCanary
* BlockCanary

## :six: 消息队列

* [MessageQueue](https://github.com/ZhangMiao147/android_learning_notes/tree/master/OpenSourceLibrary/MessageQueue)
  * RabbitMQ
    * [RabbitMQ 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/MessageQueue/RabbitMQ知识.md)
    * [提高 RabbitMQ 传输消息数据的可靠性途径](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/MessageQueue/提高RabbitMQ传输消息数据的可靠性途径.md)
    * [RabbitMQ 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/MessageQueue/RabbitMQ的使用.md)
    * [RabbitMQ 消息幂等性](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/MessageQueue/RabbitMQ消息幂等性.md)

## :seven: Jetpack

* ViewModel
* Livedata
* Lifecycles
* Navigation
* Room
  * [Room 数据库操作](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/Jetpack/room/Room%E6%95%B0%E6%8D%AE%E5%BA%93%E6%93%8D%E4%BD%9C.md)
  * [Room 原理解析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/OpenSourceLibrary/Jetpack/room/Room%E6%95%B0%E6%8D%AE%E5%BA%93%E5%8E%9F%E7%90%86.md)
* Paging
* WorkManager
* DataBinding

## :eight: 其他

* XWalkView

* Okio

  * 解析 html内容

    [在 Android 应用中使用 HTML Parser 便捷的解析 html 内容](https://www.ibm.com/developerworks/cn/opensource/os-cn-android-hp/index.html)

# :atom_symbol: ​技术栈

## :one: Kotlin 语言

* 扩展函数
* 构造函数
* Latent、by lazy
* 协程
* [动脑学院 kotlin 学习笔记]
* [Kotlin 之 String]
* [kotlin 协程]
* [利用 Kotlin 特点]

## :two: 模块化

## :three: 插件化

## :four: 组件化

* [组件化](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E7%BB%84%E4%BB%B6%E5%8C%96/%E7%BB%84%E4%BB%B6%E5%8C%96.md)

## :five: 热更新

## :six: Flutter

## :seven: React Native

## :eight: ​Fiddler

参考资料： [fiddler4使用教程](https://blog.csdn.net/chaoyu168/article/details/51065644)

## :eight: 架构

* 类图、时序图的绘制
* [高焕堂 Android 从程序员到架构师之路学习笔记](https://github.com/ZhangMiao147/android_learning_notes/tree/master/技术栈/架构/高焕堂架构视频学习记录)

## :nine: 前端

* JS

# :bookmark_tabs: ​计划

## :one: 简历

## :two: 计划

* 2019 年计划
* 2020 年计划
* 2021 年计划
* 2022 年计划

## :three: 总结

* 2019 年总结，2020 年计划
* 2020 年总结，2021 年计划
* 2021 年总结，2022 年计划

# :books: ​读书笔记

## :one: 技术书籍

* [图解 HTTP](https://github.com/ZhangMiao147/android_learning_notes/tree/master/BookNote/图解HTTP)
* [第一行代码](https://github.com/ZhangMiao147/android_learning_notes/tree/master/BookNote/第一行代码)
* [深入理解 Java 虚拟机](https://github.com/ZhangMiao147/android_learning_notes/tree/master/BookNote/深入理解%20Java%20虚拟机)
* [Java 多线程编程核心技术](https://github.com/ZhangMiao147/android_learning_notes/tree/master/BookNote/Java多线程编程核心技术)
* [Java 核心技术卷一：基础知识](https://github.com/havenBoy/JavaBook-Notes/tree/master/Java核心技术卷一基础知识)
* [React Native 移动开发实战](https://github.com/ZhangMiao147/android_learning_notes/tree/master/BookNote/React%20Native%20移动开发实战)
* [重构改善既有代码的设计](https://github.com/ZhangMiao147/android_learning_notes/tree/master/BookNote/重构改善既有代码的设计)
* [Effective Java](https://github.com/ZhangMiao147/android_learning_notes/blob/master/BookNote/Effective%20Java/EffectiveJavaReadRecord.txt)
* [代码整洁之道](https://github.com/ZhangMiao147/android_learning_notes/tree/master/BookNote/%E4%BB%A3%E7%A0%81%E6%95%B4%E6%B4%81%E4%B9%8B%E9%81%93)
* [设计模式之禅]
* [Android Jetpack 应用指南]

## :two: 非技术书籍

* [每天演好一个情绪稳定的成年人](https://github.com/ZhangMiao147/android_learning_notes/blob/master/BookNote/非技术书籍/每天演好一个情绪稳定的成年人.md)
* [思维导图完整手册](https://github.com/ZhangMiao147/android_learning_notes/blob/master/BookNote/非技术书籍/思维导图完整手册.md)
* [指数基金投资指南](https://github.com/ZhangMiao147/android_learning_notes/blob/master/BookNote/非技术书籍/指数基金投资指南.md)
* [山茶文具店](https://github.com/ZhangMiao147/android_learning_notes/blob/master/BookNote/非技术书籍/山茶文具店.md)
* [明朝那些事儿](https://github.com/ZhangMiao147/android_learning_notes/blob/master/BookNote/非技术书籍/明朝那些事儿.md)
* [今天也要认真穿](https://github.com/ZhangMiao147/android_learning_notes/blob/master/BookNote/非技术书籍/今天也要认真穿.md)

