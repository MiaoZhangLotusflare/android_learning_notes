# Android 知识目录

## 四大组件

* Activity
  * [Activity 的生命周期](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.md)
  * [验证 Activity 生命周期的问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81Activity%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%9A%84%E9%97%AE%E9%A2%98.md)
  * [Activity 的启动模式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%90%AF%E5%8A%A8%E6%A8%A1%E5%BC%8F.md)
  * [验证 Activity 四种 launchMode ](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81Activity%E5%9B%9B%E7%A7%8DlaunchMode.md)
  * [验证 onNewIntent 方法](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81%20onNewIntent%20%E6%96%B9%E6%B3%95.md)
  * [验证 Intent 的 flags ](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E9%AA%8C%E8%AF%81Intent%E7%9A%84flags.md)
  * [关于 Intent 的全部 flags 介绍](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/%E5%85%B3%E4%BA%8EIntent%E7%9A%84%E5%85%A8%E9%83%A8flags%E4%BB%8B%E7%BB%8D.md)
  * [Activity 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E4%BD%BF%E7%94%A8.md)
  * [Activity 的启动流程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B.md)
  * [Activity 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98.md)
  * [Activity 的状态](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Activity/Activity%E7%9A%84%E5%9B%9B%E7%A7%8D%E7%8A%B6%E6%80%81.md)
* Service
  * [Service 的基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Service/Service%E7%9A%84%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md)
  * [Service 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/Service/Service%E7%9A%84%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98.md)
  * 进程保活
  * JobService、JobScheduler
  * Messenger
  * IntentService
* BroadcaseReceiver
  * [BroadcaseReceiver 的基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/BroadcastReceiver/BroadcastRecevier的基础知识.md)
  * [BroadcaseReceiver 的实现原理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/BroadcastReceiver/BroadcastRecevier的实现原理.md)
  *  [BroadcaseReceiver 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/BroadcastReceiver/BroadcastRecevier的常见问题.md)
  *  LocalBroadcastManager 
* ContentProvider
  * [ContentProvider 的知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider%E7%9A%84%E7%9F%A5%E8%AF%86.md)
  * [ContentProvider 的常见问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider%E7%9A%84%E5%B8%B8%E8%A7%81%E9%97%AE%E9%A2%98.md)
  * [ContentProvider 运行过程源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider运行过程源码分析.md)
  * [ContentProvider 的共享数据更新通知机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ContentProvider/ContentProvider的共享数据更新通知机制.md)
* [IntentFilter 的匹配规则](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/IntentFilter的匹配规则.md)
* AMS、WMS 与 PMS
* [ActivityManagerService](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/ActivityManagerService.md)
* [WindowManagerService](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/WindowManagerService.md)
* [PackageManagerService之启动解析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/PackageManagerService之启动解析.md)
* [守护进程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/components/守护进程.md)



* https://www.bbsmax.com/A/n2d9eL06dD/ Android 开发之：Intent.createChooser() 妙用

## 进程与线程

* Handler
  * [Handler 机制分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/Handler机制分析.md)
  * [Handler 的延伸](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/Handler的延伸.md)
  * [ThreadLocal 知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/ThreadLocal知识.md)
  * [AsyncTask 的知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/handler/AysncTask的知识.md)
  * HandlerThread
  * IdleHandler
* 进程间通信
  * IPC 多线程通信方式
  * Binder
  * 序列化
    * Serializable
    * Parcelable
  * AIDL
    * [AIDL 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/IPC/AIDL/AIDL%E7%9A%84%E4%BD%BF%E7%94%A8.md)
    * [AIDL 的工作原理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/IPC/AIDL/AIDL%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.md)

## Fragment

* [Fragment 的基础知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/fragment/Fragment的基础知识.md)
* [Fragment 的使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/fragment/Fragment的使用.md)
* Fragment+ViewPager 的懒加载
* Android系列之Fragment（一）----Fragment加载到Activity当中 http://www.cnblogs.com/smyhvae/p/3978989.html)

##  View

* [Activity 的布局绘制过程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/Activity的布局绘制过程.md)
* [View 绘制流程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/View绘制流程.md)
* [视图状态与重绘流程](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/视图状态与重绘流程.md) 
* [View 事件分发机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/View事件分发机制.md)
* [ViewGroup 事件分发机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/ViewGroup事件分发机制.md)
* [自定义View的实现方式](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/自定义View的实现方式.md)
* GestureDetector
* ActionBarDrawerToggle 
* 滑动冲突
* dispatch
* Window
  * ViewRoot
  * PhoneWindow
  * Window、Activity、DecorView 以及 ViewRoot 之间的关系
* 动画
  * 帧动画
  * View 动画
  * 属性动画
  * FrameAnimation 逐帧动画
  * TweenAnimation 补间动画
  * PropertyAnimation 属性动画
  * 第三方动画库
    * Loto
* View 的实践
	* [Android实现卡片翻转的动画（翻牌动画）](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/view%E7%9A%84%E5%AE%9E%E8%B7%B5/Android%E5%AE%9E%E7%8E%B0%E5%8D%A1%E7%89%87%E7%BF%BB%E8%BD%AC%E7%9A%84%E5%8A%A8%E7%94%BB%EF%BC%88%E7%BF%BB%E7%89%8C%E5%8A%A8%E7%94%BB%EF%BC%89.md)
	* [系统控件的常用使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/view%E7%9A%84%E5%AE%9E%E8%B7%B5/%E7%B3%BB%E7%BB%9F%E6%8E%A7%E4%BB%B6%E7%9A%84%E5%B8%B8%E7%94%A8%E4%BD%BF%E7%94%A8.md)
	* [GridView嵌套在ScrollView里展示不全](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/GridView%E5%B5%8C%E5%A5%97%E5%9C%A8ScrollView%E9%87%8C%E5%B1%95%E7%A4%BA%E4%B8%8D%E5%85%A8.md)
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
* 布局
  * [ConstraintLayout](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/view/ConstraintLayout.md)
  * LinearLayout 为什么比 RelativeLayout 性能更好
* WebView
  * js 通信
  * 优化
* View
  * [WebView 遇到的 Exception]
  * [EditText 的 imeOptions 与多行输入的问题]
  * [getDimensionxxx方法区别]
  * Snackbars
    * [Snackbars 常见问题]
* Materal Design
  * Toolbar
  * 沉浸式状态栏
  * CardView
  * AppBarLayout
  * DrawerLayout
  * NavigationView
  * FloatingActionButton
  * Snackbar
  * 下拉刷新

## 网络

* 长链接

  * https://blog.csdn.net/qq_23547831/article/details/51690047 Android产品研发（十二）-->App长连接实现

  * https://www.jianshu.com/p/6ff879706d9c Android WebSocket长连接的实现

## Framework

* 系统启动流程
* 应用启动流程
* apk 打包流程
* SystemServer
  * ActivityManagerService
  * PackageManagerService
  * WindowManagerService
* Binder
* Intent
* pms

## 缓存

* [缓存机制](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/cache/缓存机制.md)
* [LruCache知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/cache/LruCache知识.md)
* [本地缓存ACache](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/cache/%E6%9C%AC%E5%9C%B0%E7%BC%93%E5%AD%98ACache.md)
* 文件存储
* SharedPreferences
* SQLite
* http://www.javashuo.com/article/p-ytiohkrr-hc.html Android 存储优化 —— MMKV 集成与原理

## 优化

- [ANR 相关知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/ANR%E7%9B%B8%E5%85%B3%E7%9F%A5%E8%AF%86.md)
- [发生 ANR 条件的源码分析](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/%E5%8F%91%E7%94%9F%20ANR%20%E6%9D%A1%E4%BB%B6%E7%9A%84%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90.md)
- [性能优化](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96.md)
- [Android 内存知识](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/Android内存知识.md)
- [内存泄漏](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/内存泄漏.md)
- [内存溢出](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/内存溢出.md)
- [布局优化之 include](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/布局优化之include.md)
- [布局优化之 merge](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/布局优化之merge.md)
- [布局优化之 ViewStub](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/布局优化之ViewStub.md)
- [性能优化工具](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/optimize/%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%E5%B7%A5%E5%85%B7.md)
- android 大图加载显示 https://blog.csdn.net/wenzhi20102321/article/details/123972710

## 存储

* [安卓存储空间](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E5%AE%89%E5%8D%93%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4.md)
* [安卓 RAM 与 ROM](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E5%AE%89%E5%8D%93RAM%E4%B8%8EROM.md)
* [Android 存储空间监控](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/Android%E5%AD%98%E5%82%A8%E7%A9%BA%E9%97%B4%E7%9B%91%E6%8E%A7.md)
* [空间清理](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E7%A9%BA%E9%97%B4%E6%B8%85%E7%90%86.md)
* [应用大小计算](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/storage/%E7%A9%BA%E9%97%B4%E5%A4%A7%E5%B0%8F%E8%AE%A1%E7%AE%97.md)

## 适配

* [Android 4.4.4 的 setResult 失效的问题适配](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/adaptation/Android4.4.4%E7%9A%84setResult%E5%A4%B1%E6%95%88%E7%9A%84%E9%97%AE%E9%A2%98%E9%80%82%E9%85%8D.md)
* [Android 4.4.4 支持分包后找不到勒的问题](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/adaptation/Android%204.4.4支持分包后找不到类的问题.md)
* Android 6 权限适配
* Android 8 适配
  * apk 下载失败
  * 通知栏不显示
  * 无法安装 APK（权限）
* 屏幕适配

## Gradle

* [api 与 implementation 的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/gradle/api%E4%B8%8Eimplementation%E7%9A%84%E5%8C%BA%E5%88%AB.md)



## 测试

* 单元测试
	* [Android单元测试之一：基本概念](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E4%B8%80%EF%BC%9A%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5.md)
	* [Android单元测试之二：本地测试](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E4%BA%8C%EF%BC%9A%E6%9C%AC%E5%9C%B0%E6%B5%8B%E8%AF%95.md)
	* [Android单元测试之三：使用模拟框架模拟依赖](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E4%B8%89%EF%BC%9A%E4%BD%BF%E7%94%A8%E6%A8%A1%E6%8B%9F%E6%A1%86%E6%9E%B6%E6%A8%A1%E6%8B%9F%E4%BE%9D%E8%B5%96.md)
	* [Android单元测试之四：仪器化测试](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/test/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/Android%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95%E4%B9%8B%E5%9B%9B%EF%BC%9A%E4%BB%AA%E5%99%A8%E5%8C%96%E6%B5%8B%E8%AF%95.md)



## 架构与设计模式

* [设计模式选择](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E9%80%89%E6%8B%A9.md)
* [MVVM 设计模式与 ViewModel、LiveData](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/MVVM%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B8%8EViewModel%E4%B8%8ELiveData.md)
* [MutableLiveData 详解](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/MutableLiveData%E8%AF%A6%E8%A7%A3.md)
* [LiveDataBus 使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/LiveDataBus%E4%BD%BF%E7%94%A8.md)
* [MVI](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/MVI.md)
* 组件化 
  * [组件化](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/%E6%9E%B6%E6%9E%84%E4%B8%8E%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/%E7%BB%84%E4%BB%B6%E5%8C%96/%E7%BB%84%E4%BB%B6%E5%8C%96.md)

## 其他

* [多渠道打包](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/Software/%E5%A4%9A%E6%B8%A0%E9%81%93%E6%89%93%E5%8C%85.md)

* [jar 包和 aar 包的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/jar%E5%8C%85%E4%B8%8Eaar%E5%8C%85%E7%9A%84%E5%8C%BA%E5%88%AB.md)

* [Android 中 Home 键的监听](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/Android中Home键的监听.md)

* [shape 布局文件的替换方案](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/shape布局文件的替换方案.md)

* [Android 编码规范](https://github.com/ZhangMiao147/android_learning_notes/tree/master/Android/Android编码规范)

* [uses-permission 和 permission 区别及使用](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/other/uses-permission%E5%92%8Cpermission%E5%8C%BA%E5%88%AB%E5%8F%8A%E4%BD%BF%E7%94%A8.md)

* [类加载器 ClassLoader](https://github.com/ZhangMiao147/android_learning_notes/tree/master/Android/other/classLoader)

* ArrayDeque

* ArrayMap

* SpareArray

* nano ptotobufs

* 协程

* Android Dex 分包 https://www.jianshu.com/p/e96f345e822f

* SurfaceView 

* WindowManager

  [Android解析WindowManager（一）WindowManager体系](https://blog.csdn.net/itachi85/article/details/77888668)

* DiskLruCache

  [Android DiskLruCache完全解析，硬盘缓存的最佳方案](https://blog.csdn.net/guolin_blog/article/details/28863651)

* Protocal buffers 

* 混淆

  * ProGuard

* 多媒体

  * 拍照
  * 录像
  * 选择照片或视频
  * 音频
  * 视频

* 测试

* Gradle

  * gradle
  * [api 与 implementation 的区别](https://github.com/ZhangMiao147/android_learning_notes/blob/master/Android/gradle/api%E4%B8%8Eimplementation%E7%9A%84%E5%8C%BA%E5%88%AB.md)
  * Gradle 插件
  * Gradle 多渠道打包
  * gradle 混淆瘦身
  * Lint
  * 加固

* json & Gson

* 图片

  * Bitmap
    * 记载效率
    * 缓存策略
    * 内存计算
  * Drawable

* git

  * 基本使用
  * 分支
  * 代码合并与冲突处理

* Android studio

  * 使用和调试
  * 常用插件
    * 代码规范检测插件
  * 快捷键
  
* Android 逆向

  https://blog.csdn.net/shulianghan/category_11396146.html

## Question 平时开发遇到的问题记录

* [Bitmap too larget to be uploaded into a texture 的解决方法](https://github.com/ZhangMiao147/android_learning_notes/blob/master/question/Bitmap%20too%20larget%20to%20be%20uploaded%20into%20a%20texture%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95.md)

* [Conflict with dependency 'com.android.supportsupport-annotations' in project 'xxx'. Resolved versions for app (25.4.0) and test app (27.1.1) differ 问题解决](https://github.com/ZhangMiao147/android_learning_notes/blob/master/question/Conflict%20with%20dependency%20'com.android.supportsupport-annotations'%20in%20project%20'xxx'.%20Resolved%20versions%20for%20app%20(25.4.0)%20and%20test%20app%20(27.1.1)%20differ%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3.md)

* [You need to use a Theme.AppCompat theme (or descendant) with this activity 问题解决](https://github.com/ZhangMiao147/android_learning_notes/blob/master/question/You%20need%20to%20use%20a%20Theme.AppCompat%20theme%20(or%20descendant)%20with%20this%20activity%20%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3.md)

* [Animator 动画第一次播放正常，之后播放都不正常的问题解决](https://github.com/ZhangMiao147/android_learning_notes/blob/master/question/Animator%E5%8A%A8%E7%94%BB%E7%AC%AC%E4%B8%80%E6%AC%A1%E6%92%AD%E6%94%BE%E6%AD%A3%E5%B8%B8%EF%BC%8C%E4%B9%8B%E5%90%8E%E9%83%BD%E4%B8%8D%E6%AD%A3%E5%B8%B8%E7%9A%84%E8%A7%A3%E5%86%B3.md)



