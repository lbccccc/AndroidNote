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

## WMS设计模式：桥接模式

https://www.jianshu.com/p/028be994677f

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



# 启动一个APP

https://zhuanlan.zhihu.com/p/86266649

![img](https://pic2.zhimg.com/80/v2-8419426dda4cdc6bd962237e898f7029_720w.jpg)

1. 点击桌面App图标，Launcher进程采用Binder IPC向system_server进程发起startActivity请求；
2. system_server进程接收到请求后，向zygote进程发送创建进程的请求；
3. Zygote进程fork出新的子进程，即App进程；
4. App进程，通过Binder IPC向sytem_server进程发起attachApplication请求；
5. system_server进程在收到请求后，进行一系列准备工作后，再通过binder IPC向App进程发送scheduleLaunchActivity请求；
6. App进程的binder线程（ApplicationThread）在收到请求后，通过handler向主线程发送LAUNCH_ACTIVITY消息；
7. 主线程在收到Message后，通过反射机制创建目标Activity，并回调Activity.onCreate()等方法。 到此，App便正式启动，开始进入Activity生命周期，执行完onCreate/onStart/onResume方法，UI渲染结束后便可以看到App的主界面。

 2020 10.9  12.46
