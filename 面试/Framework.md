https://blog.csdn.net/mountain_hua/article/details/81301299

Android系统的构成如下，从上到下依次是

- Application应用层
- Framework框架层
- LIbrary系统库层
- Linux内核层

Android的Framework是直接应用之下的一层，叫做应用程序框架层。这一层是核心应用程序所使用的API框架，为应用层提供各种API，提供各种组件和服务来支持我们的Android开发，包括ActivityManager,WindowManager,ViewSystem等。 

从图中可以看到

1. ActivityManagerService(Ams):负责管理所有应用程序中的Activity,它掌握所有Activity的情况，具有所有调度Activity生命周期的能力，简单来说，ActivityManagerService是管理和掌控所有的Activity.
2. WindowManagerService(Wms):控制窗口的显示、隐藏以及窗口的层序，简单来说，它就是管理窗口的，大多数和View有关系的都要和它打交道。
3. 内容提供器(Content Providers)：可以让一个应用访问"另一个应用"的数据(比如联系人数据库),或者共享他们的数据
4. 视图系统(View System):丰富且可拓展,包括:列表(lists),网络(grids),文本框(text boxes),按钮(buttons)等等.
5. 通知管理器(Notification Manager):可以在"状态栏中"显示自定义的提示信息
6. 包管理器(Package Manger):对Android系统内的程序管理
7. Telephony Manager主要提供了一系列用于访问与手机通讯相关的状态和信息的方法，查询电信网络状态信息，sim卡的信息等。
8. Resource Manager提供非代码资源的访问，如本地字符串，图形，和布局文件（Layout files )。
9. Location Manager提供设备的地址位置的获取方式。很显然，GPS导航肯定能用到位置服务。
10. XMPP可扩展通讯和表示协议。前身为Jabber，提供即时通信服务。例如推送功能,Google Talk。

> Android Framework的三大核心功能：
>
> 1、View.java:View工作原理，实现包括绘制view、处理触摸、按键事件等。
>
> 2、ActivityManagerService.java:Ams 管理所有应用程序的Activity等。
>
> 3、WindowManagerService.java:Wms 为所有应用程序分配窗口，并管理这些窗口。





2020 10.9 11.46
