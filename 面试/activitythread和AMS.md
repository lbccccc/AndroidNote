https://www.cnblogs.com/awkflf11/p/12617091.html

https://blog.csdn.net/hzwailll/article/details/85339714

ActivityThread框架是基于Binder通信的C/S结构，从图可知Server端是ActivityThread、ApplicationThread，Client是AMS（ActivityManagerService），而ApplicationThreadProxy可以看作AMS中Server代表

system_server进程是系统进程，java framework框架的核心载体，里面运行了大量的系统服务，比如这里提供ApplicationThreadProxy，ActivityManagerService，这个两个服务都运行在system_server进程的不同线程中，由于ApplicationThreadProxy和ActivityManagerService都是基于IBinder接口，都是binder线程，binder线程的创建与销毁都是由binder驱动来决定的。 
App进程则是我们常说的应用程序，主线程主要负责Activity/Service等组件的生命周期以及UI相关操作都运行在这个线程； 另外，每个App进程中至少会有两个binder线程 ApplicationThread和ActivityManagerProxy，除了图中画的线程，其中还有很多线程，比如signal catcher线程等，这里就不一一列举。

Binder用于不同进程之间通信，由一个进程的Binder客户端向另一个进程的服务端发送事务，比如图中线程2向线程4发送事务；而handler用于同一个进程中不同线程的通信，比如图中线程4向主线程发送消息。

例如:

需要暂停一个Activity

线程1的AMS中调用线程2的ApplicationThreadProxy；（由于同一个进程的线程间资源共享，可以相互直接调用，但需要注意多线程并发问题）
线程2通过binder传输到App进程的线程4；
线程4通过handler消息机制，将暂停Activity的消息发送给主线程；
主线程在looper.loop()中循环遍历消息，当收到暂停Activity的消息时，便将消息分发给ActivityThread.H.handleMessage()方法，再经过方法的调用，最后便会调用到Activity.onPause()，当onPause()处理完后，继续循环loop下去。
ActivityThread介绍：

ActivityThread在Android中它就代表了Android的主线程，但是并不是一个Thread类。

严格来说，UI主线程不是ActivityThread。ActivityThread类是Android APP进程的初始类，它的main函数是这个APP进程的入口。APP进程中UI事件的执行代码段都是由ActivityThread提供的。也就是说，Main Thread实例是存在的，只是创建它的代码我们不可见。ActivityThread的main函数就是在这个Main Thread里被执行的。

1.Java程序初始类中的main()方法，将作为该程序初始线程的起点，任何其他的线程都是由这个初始线程启动的。这个线程就是程序的主线程。
2.在Thread.java文件头部的说明中，有这样的介绍：Each application has at least one thread running when it is started, the main thread, in the main {@link ThreadGroup}.

2020 10.19 21.34
