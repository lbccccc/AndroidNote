https://www.jianshu.com/p/57ba91db3705

### 为什么使用binder

在上面这些可供选择的方式中，Android使用得最多也最被认可的还是Binder机制。

因为Binder更加简洁和快速，消耗的内存资源更小吗？不错，这些也正是Binder的优点。

当然，也还有很多其他原因，比如传统的进程间通信可能会增加进程的开销，而且有进程过载和安全漏洞等方面的风险，Binder正好能解决和避免这些问题。

**Binder主要能提供以下一些功能：**

用驱动程序来推进进程间的通信。

通过共享内存来提高性能。

为进程请求分配每个进程的线程池。

针对系统中的对象引入了引用计数和跨进程的对象引用映射。

进程间同步调用。



### 什么是binder

1. 通常意义下, Binder指的是一种通信机制
2. 对于 Server进程来说, Binder指的是 Binder本地对象，对于Client来说, Binder指的是 Binder代理对象
3. 对于传输过程而言, Binder是可以进行跨进程传递的对象

### 3、binder通信模型

Binder的通信模型有4个角色：Binder Client、Binder Server、Binder Driver（Binder驱动）、ServiceManager。（图片点开链接）

可以看到，ServiceManager、Binder Client、Binder Server处于不同的进程，他们三个都在用户空间，而Binder驱动在内核空间。

那先来简述一下这个通信模型：

首先是有一个ServiceManager，刚开始这个通讯录是空白的，然后Server进程向ServiceManager注册一个映射关系表，之后Client进程想要和Server进程通信，首先向ServiceManager查询地址，ServiceManager收到查询的请求之后，返回查询结果给Client。

注意到这里不管是Server进程注册，还是Client查询，都是经过Binder驱动的，这也真是Binder驱动的作用所在，

1． Server进程向ServiceManager注册，告诉ServiceManager我是谁，我有什么，我能做什么。就好比徐同学（Server进程）有一台笔记本（computer对象），这台笔记本有个add方法。这时映射关系表就生成了。

2． Client进程向ServiceManager查询，我要调用Server进程的computer对象的add方法，可以看到这个过程经过Binder驱动，这时候Binder驱动就开始发挥他的作用了。当向ServiceManager查询完毕，是返回一个computer对象给Client进程吗？其实不然，Binder驱动将computer对象转换成了computerProxy对象，并转发给了Client进程，因此，Client进程拿到的并不是真实的computer对象，而是一个代理对象，即computerProxy对象。很容易理解这个computerProxy对象也是有add方法，（如果连add方法都没有，岂不是欺骗了Client？），但是这个add方法只是对参数进行一些包装而已。

3． 当Client进程调用add方法，这个消息发送给Binder驱动，这时驱动发现，原来是computerProxy，那么Client进程应该是需要调用computer对象的add方法的，这时驱动通知Server进程，调用你的computer对象的add方法，将结果给我。然后Server进程就将计算结果发送给驱动，驱动再转发给Client进程，这时Client进程还蒙在了鼓里，他以为自己调用的是真实的computer对象的add方法，其实他只是调用了代理而已。不过Client最终还是拿到了计算结果。

好了，一个通信过程就完成了。我们发现，其实Binder驱动就是一个中转。

2020 10.7 16.51

# binder机制

https://www.zhihu.com/question/39440766/answer/89210950

\1. **管道：**在创建时分配一个page大小的内存，缓存区大小比较有限；
\2. **消息队列**：信息复制两次，额外的CPU消耗；不合适频繁或信息量大的通信；
\3. **共享内存**：无须复制，共享缓冲区直接付附加到进程虚拟地址空间，速度快；但进程间的同步问题操作系统无法实现，必须各进程利用同步工具解决；
\4. **套接字**：作为更通用的接口，传输效率低，主要用于不通机器或跨网络的通信；
\5. **信号量**：常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。
\6. **信号**: 不适用于信息交换，更适用于进程中断控制，比如非法内存访问，杀死某个进程等；



，每种Linux的IPC机制都有存在的价值，同时在Android系统中也依然采用了大量Linux现有的IPC机制，根据每类IPC的原理特性，因时制宜，不同场景特性往往会采用其下最适宜的。比如在**Android OS中的Zygote进程的IPC采用的是Socket（套接字）机制**，Android中的**Kill Process采用的signal（信号）机制**等等。而**Binder更多则用在system_server进程与上层App层的IPC交互**。

# Zygote进程

https://www.jianshu.com/p/3dbe46439359

Zygote进程, 一个在Android系统中扮演重要角色的进程. 我们知道Android系统中的两个重要服务PackageManagerService和ActivityManagerService, 都是由SystemServer进程启动的, 而这个SystemServer进程本身是Zygote进程在启动的过程中fork出来的. 这样一来, 想必我们就知道Zygote进程在Android系统中的重要地位了.

#### PackageManagerService

PackageManagerService在Android中的非常重要，主要负责的功能如下：
 1.解析AndroidManifest.xml，主要包括AndroidManifest中节点信息的解析和target-name的分析和提炼，这部分和ActivityManagerService和WindowManagerService都有紧急的联系。关于AndroidManifest.xml中的属性设置，会单独拎出来讲解，本文不扩展讲解。
 2.扫描本地文件，主要针对apk，主要是系统应用、本地安装应用等等。这部分会在下面仔细讲解。
 3.管理本地apk，主要包括安装、删除等等

什么是Zygote? Zygote是Android系统中特有的进程, 不过说白了也只是名字特有而已, 言外之意, 不多说了. 总之我们先搞清楚一件事, Zygote一个最主要的作用, 就是加快Android应用程序启动和运行速度. 

为什么这么说? 这就要知道Zygote进程在启动时做了什么. Zygote进程运行时, 会初始化Dalvik虚拟机, 并运行它. Android的应用程序是由Java编写的, 它们不能直接运行在Linux上, 只能运行在Dalvik虚拟机中. 并且, 每个应用程序都运行在各自的虚拟机中, 应用程序每次运行都要重新初始化并启动虚拟机, 这个过程会消耗相当长时间, 是拖慢应用程序的原因之一. 因此, 在Android中, 应用程序运行前, 通过Zygote进程共享已运行的虚拟机的代码与内存信息, 缩短应用程序运行所耗费的时间. 也就是说, Zygote进程会事先将应用程序要使用的Android Framework中的类与资源加载到内存中, 并组织形成所用资源的链接信息. 这样, 新运行的Android应用程序在使用所需资源时不必每次形成资源的链接信息, 这样就大大提升了程序的运行时间.



# 总结:

1. 系统启动时init进程会创建Zygote进程，Zygote进程负责后续Android应用程序框架层的其它进程的创建和启动工作。
2. Zygote进程会首先创建一个SystemServer进程，SystemServer进程负责启动系统的关键服务，如包管理服务PackageManagerService和应用程序组件管理服务ActivityManagerService。
3. 
4. 当我们需要启动一个Android应用程序时，ActivityManagerService会通过Socket进程间通信机制，通知Zygote进程为这个应用程序创建一个新的进程。





2020 10.25 10.42
