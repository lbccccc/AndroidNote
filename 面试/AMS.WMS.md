https://www.jianshu.com/p/47eca41428d6
https://blog.csdn.net/AndroidStudyDay/article/details/95166849

**Activity与WIndow：**

- Activity只负责生命周期和事件处理
- Window只控制视图
- 一个Activity包含一个Window，如果Activity没有Window，那就相当于Service

**AMS与WMS：**

- AMS统一调度所有应用程序的Activity
- WMS控制所有Window的显示与隐藏以及要显示的位置



在视图层次中，Activity在WIndow之上，如下图

Android应用程序窗口模型

下面进入到对WMS的介绍。

# WMS

## 基础了解

WindowManagerService服务的实现是相当复杂的，毕竟它要管理的整个系统所有窗口的UI，而在任何一个系统中，窗口管理子系统都是极其复杂的。

### 作用

- 为所有窗口分配Surface。客户端向WMS添加一个窗口的过程，其实就是WMS为其分配一块Suiface的过程，一块块Surface在WMS的管理下有序的排布在屏幕上。Window的本质就是Surface。
- 管理Surface的显示顺序、尺寸、位置
- 管理窗口动画
- 输入系统相关：WMS是派发系统按键和触摸消息的最佳人选，当接收到一个触摸事件，它需要寻找一个最合适的窗口来处理消息，而WMS是窗口的管理者，系统中所有的窗口状态和信息都在其掌握之中，完成这一工作不在话下。

### 什么是Window

“Window”表明它是和窗口相关的，“窗口”是一个抽象的概念，从用户的角度来讲，它是一个“界面”；从SurfaceFlinger的角度来看，它是一个Layer，承载着和界面有关的数据和属性；从WMS角度来看，它是一个WIndowState，用于管理和界面有关的状态。

在《深入理解Android内核设计思想》一书中看到一个比喻非常好，整个界面就像由N个演员参与的话剧：SurfaceFling是摄像机，它只负责客观的捕捉当前的画面，然后真实的呈现给观众；WMS就是导演，它要负责话剧的舞台效果、演员站位；ViewRoot就是各个演员的长相和表情，取决于它们各自的条件与努力。可见，WMS与SurfaceFling的一个重要区别就是——后者只做与“显示”相关的事情，而WMS要处理对输入事件的派发。

Android支持的窗口类型很多，统一可以分为三大类，另外各个种类下还细分为若干子类型，且都在`WindowManager.java`中有定义。

- **Application Window**
- **SystemWindow**
- **Sub Window**

# AMS

## 基础了解

### 作用

- 统一调度所有应用程序的Activity的生命周期
- 启动或杀死应用程序的进程
- 启动并调度Service的生命周期
- 注册BroadcastReceiver，并接收和分发Broadcast
- 启动并发布ContentProvider
- 调度task
- 处理应用程序的Crash
- 查询系统当前运行状态

## 类间关系

### 设计模式

- **代理模式**



### 工作流程

AMS的工作流程，其实就是Activity的启动和调度的过程，所有的启动方式，最终都是通过Binder机制的Client端，调用Server端的AMS的`startActivityXXX()`系列方法。所以可见，工作流程又包括Client端和Server端两个。

#### Client端流程

- Launcher主线程捕获`onClick()`点击事件后，调用`Launcher.startActivitySafely()`方法。`Launcher.startActivitySafely()`内部调用了Launcher.startActivity()方法，`Launcher.startActivity()`内部调用了Launcher的父类Activity的`startActivity()`方法。
- `Activity.startActivity()`调用`Activity.startActivityForResult()`方法，传入该方法的requestCode参数若为-1，则表示Activity启动成功后，不需要执行`Launcher.onActivityResult()`方法处理返回结果。
- 启动Activity需要与系统ActivityManagerService交互，必须纳入Instrumentation的监控，因此需要将启动请求转交instrumentation，即调用`Instrumentation.execStartActivity()`方法。
- `Instrumentation.execStartActivity()`首先通过ActivityMonitor检查启动请求，然后调用`ActivityManagerNative.getDefault()`得到ActivityManagerProxy代理对象，进而调用该代理对象的`startActivity()`方法。
- ActivityManagerProxy是ActivityManagerService的代理对象，因此其内部存储的是BinderProxy，调用`ActivityManagerProxy.startActivity()`实质是调用`BinderProxy.transact()`向Binder驱动发送START_ACTIVITY_TRANSACTION命令。Binder驱动将处理逻辑从Launcher所在进程切换到ActivityManagerService所在进程。

#### Server端流程

启动Activity的请求从Client端传递给Server端后，便进入了启动应用的七个阶段，这里也是整理出具体流程，

 2020 10.9  12.46
