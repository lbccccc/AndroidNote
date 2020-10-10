https://www.jianshu.com/p/4dd4ccb806e6

## 1.1 注册Service需要注意什么

Service还是运行在主线程当中的，所以如果需要执行一些复杂的逻辑操作，最好在服务的内部手动创建子线程进行处理，否则会出现UI线程被阻塞的问题

## 1.2 Service与Activity怎么实现通信(不要死记硬背，结合文章)

相关文章

[Service和Activity的相互通信](https://www.jianshu.com/p/4b0cbe068555)

- 方法一：
  - 1. 添加一个继承Binder的内部类，并添加相应的逻辑方法
  - 1. 重写Service的onBind方法，返回我们刚刚定义的那个内部类实例
  - 1. 重写ServiceConnection，onServiceConnected时调用逻辑方法 绑定服务
- 方法二
  - 通过接口Iservice调用Service方法，使用接口调用service和直接调用其实本质都是一样的，只不过多了借口一个步骤

## 1.3 介绍源码中Binder机制（了解）

面试问到这个，其实就是让你说一下binder是干什么的，Service Manager是如何成为一个守护进程的

[Binder机制](https://www.jianshu.com/p/da76ffa0c859)

## 1.4 IntentService与Service的区别（intentservice的优点）

> IntentService是Service的子类，是一个异步的，会自动停止的服务，很好解决了传统的Service中处理完耗时操作忘记停止并销毁Service的问题

- 会创建独立的worker线程来处理所有的Intent请求；
- 会创建独立的worker线程来处理onHandleIntent()方法实现的代码，无需处理多线程问题；
- 所有请求处理完成后，IntentService会自动停止，无需调用stopSelf()方法停止Service；
- 为Service的onBind()提供默认实现，返回null；
- 为Service的onStartCommand提供默认实现，将请求Intent添加到队列中；
- IntentService不会阻塞UI线程，而普通Serveice会导致ANR异常
- Intentservice若未执行完成上一次的任务，将不会新开一个线程，是等待之前的任务完成后，再执行新的任务，等任务完成后再次调用stopSelf()

## 1.5 Service 是否在 main thread 中执行, service 里面是否 能执行耗时的操作?

默认情况,如果没有显示的指 service 所运行的进程, Service 和 activity 是运 行在当前 app 所在进程的 main thread(UI 主线程)里面。

service 里面不能执行耗时的操作(网络请求,拷贝数据库,大文件 )

特殊情况 ,可以在清单文件配置 service 执行所在的进程 ,让 service 在另 外的进程中执行



```java
<service android:name="com.baidu.location.f" android:enabled="true" android:process=":remote" >
</service>
```

## 1.6 Service的生命周期

Service 有绑定模式和非绑定模式,以及这两种模式的混合使用方式。不同 的使用方法生命周期方法也不同。

- 非绑定模式:当第一次调用 startService 的时候执行的方法依次为 onCreate()、onStartCommand(),当 Service 关闭的时候调用 onDestory 方 法。
- 绑定模式:第一次 bindService()的时候,执行的方法为 onCreate()、 onBind()解除绑定的时候会执行 onUnbind()、onDestory()。

上面的两种生命周期是在相对单纯的模式下的情形。我们在开发的过程中还 必须注意 Service 实例只会有一个,也就是说如果当前要启动的 Service 已经存 在了那么就不会再次创建该 Service 当然也不会调用 onCreate()方法。

一个 Service 可以被多个客户进行绑定,只有所有的绑定对象都执行了

onBind()方法后该 Service 才会销毁,不过如果有一个客户执行了 onStart() 方法,那么这个时候如果所有的 bind 客户都执行了 unBind()该 Service 也不会 销毁。

Service 的生命周期图如下所示,帮助大家记忆。

#### 只使用startService启动服务的生命周期

![img](https:////upload-images.jianshu.io/upload_images/7508328-62d4693d07c97246.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### 只使用BindService绑定服务的生命周期

![img](https:////upload-images.jianshu.io/upload_images/7508328-fa644a8e06d52847.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

#### 同时使用startService()启动服务、BindService()绑定服务的生命周期

![img](https:////upload-images.jianshu.io/upload_images/7508328-91e94e11578a7775.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

## 1.7 Activity、Intent、Service 是什么关系（了解）

他们都是 Android 开发中使用频率最高的类。其中 Activity 和 Service 都是 Android 四大组件之一。他俩都是 Context 类的子类 ContextWrapper 的子类, 因此他俩可以算是兄弟关系吧。不过兄弟俩各有各自的本领,Activity 负责用户 界面的显示和交互,Service 负责后台任务的处理。Activity 和 Service 之间可 以通过 Intent 传递数据,因此可以把 Intent 看作是通信使者。

## 1.8 Service 和 Activity 在同一个线程吗？

对于同一 app 来说默认情况下是在同一个线程中的,main Thread (UI Thread)。

## 1.9 在 service 的生命周期方法 onstartConmand()可不可以执行网络操作?如何在 service 中执行网络操作?（在service如何执行耗时操作）

可以直接在 Service 中执行网络操作,在 onStartCommand()方法中可以执行网络操作

如果需要在服务中进行耗时操作，可以选择IntentService，  IntentService是Service的子类，用来处理异步请求。

IntentService在onCreate()方法中通过HandlerThread单独开启一个线程来处理Intent请求对象所对应的任务，这样可以避免事务处理阻塞主线程。

onHandleIntent()函数针对Intent的不同进行不同的事务处理就可以，执行完一个Intent请求对象所对应的工作之后，如果没有新的Intent请求达到，则自动停止Service； 否则ServiceHandler会取得下一个Intent请求
 传入该函数来处理其所对应的任务。

## 2.0 如何提高service的优先级？ （理解，说出两三条就很nB了）

[参考文章](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fbfboys%2Farticle%2Fdetails%2F53957396)

- 1、在AndroidManifest.xml文件中对于intent-filter可以通过android:priority = “1000”这个属性设置最高优先级，1000是最高值，如果数字越小则优先级越低，同时实用于广播。
- 2、在onStartCommand里面调用 startForeground()方法把Service提升为前台进程级别，然后再onDestroy里面要记得调用stopForeground ()方法。
- 3、onStartCommand方法，手动返回START_STICKY。
- 4、 在onDestroy方法里发广播重启service。
   service +broadcast 方式，就是当service走ondestory的时候，发送一个自定义的广播，当收到广播的时候，重新启动service。（第三方应用或是在setting里-应用-强制停止时，APP进程就直接被干掉了，onDestroy方法都进不来，所以无法保证会执行）
- 5、监听系统广播判断Service状态。
   通过系统的一些广播，比如：手机重启、界面唤醒、应用状态改变等等监听并捕获到，然后判断我们的Service是否还存活。
- 6、Application加上Persistent属性。

## 2.1 Service 的 onStartCommand 方法有几种返回值?各代表什么意思?

有四种返回值,不同值代表的意思如下:

- **START_STICKY:**如果 service 进程被 kill 掉,保留 service 的状态为开始状态,但不保留递送的 intent 对象。随 后系统会尝试重新创建 service,由于服务状态为开始状态,所以创建服务后一定会调用 onStartCommand(Intent,int,int)方法。如果在此期间没有任何启动命令被传递到 service,那么参数 Intent 将为 null。
- **START_NOT_STICKY:**“非粘性的”。使用这个返回值时,如果在执行完 onStartCommand 后,服务被异常 kill 掉,系统不会自动重启该服务。
- **START_REDELIVER_INTENT:**重传 Intent。使用这个返回值时,如果在执行完 onStartCommand 后,服务被异 常 kill 掉,系统会自动重启该服务,并将 Intent 的值传入。
- **START_STICKY_COMPATIBILITY:** START_STICKY 的兼容版本,但不保证服务被 kill 后一定能重启。

## 2.2 Service 的 onRebind(Intent)方法在什么情况下会执行?

如果在 onUnbind()方法返回 true 的情况下会执行,否则不执行。

## 2.3 Activity 调用 Service 中的方法都有哪些方式?

- **Binder：**
   通过 Binder 接口的形式实现,当 Activity 绑定 Service 成功的时候 Activity 会在 ServiceConnection 的类 的 onServiceConnected()回调方法中获取到 Service 的 onBind()方法 return 过来的 Binder 的子类，然后通过对象调用方法。
- **Aidl:**
   aidl 比较适合当客户端和服务端不在同一个应用下的场景。
- **Messenger：**
   它引用了一个Handler对象，以便others能够向它发送消息(使用mMessenger.send(Message msg)方法)。该类允许跨进程间基于Message的通信(即两个进程间可以通过Message进行通信)，在服务端使用Handler创建一个Messenger，客户端持有这个Messenger就可以与服务端通信了。一个Messeger不能同时双向发送，两个就就能双向发送了

这里画了一个粗浅的图帮助大家理解。

![img](https:////upload-images.jianshu.io/upload_images/7508328-45ceb2ea3a551e76.png?imageMogr2/auto-orient/strip|imageView2/2/w/704/format/webp)

image.png

## Service里面可以弹Toast么？

**面试经验：**

面试官问这个问题就是看看你对Service是否深入理解，Service不仅可以弹Toast还能弹出对话框，把Service看成Activity就行，所有的界面都是展示到Window窗体上面的，大家可以回顾下小卫士里面来点归属地不就是在服务里面进行监听归属地进行展示的吗。

**答案解析：**

可以的。弹吐司有个条件就是得有一个Context上下文，而Service本身就是Context的子类，因此在Service里面弹Toast是完全可以的。比如我们在Service中完成下载任务后可以弹一个Toast通知用户。

## 你一般在什么情况下会使用Service？

**经验总结：**

Service其实就是背地搞事情，又不想让别人知道，举一个生活当中的例子，陪女友去做胃镜，管子才入口十五公分女友就恶心想吐，那么你就要担心了，这婚后日子可咋过啊！看完这个，大家应该就理解了，你想知道一件事情不需要直接去问，你可以通过侧面了解。这就是Service设计的初衷

**Service为什么被设计出来？**

根据Service的定义，我们可以知道需要长期在后台进行的工作我们需要将其放在Service中去做。说得再通熟易懂一点，就是不能放在Activity中来执行的工作就必须得放到Service中去做。如：音乐播放、下载、上传大文件、定时关闭应用等功能。这些功能如果放到Activity中做的话，那Activity退出被销毁了的话，那这些功能也就停止了，这显然是不符合我们的设计要求的，所以要将他们放在Service中去执行。

## Activity怎么和service绑定，怎么在activity中启动自己对应的service？

**答案解析：**

1. Activity通过bindService(Intent service, ServiceConnection conn, int flags)跟Service进行绑定。

1. 绑定成功以后，Service会将代理对象通过回调的形式传递给MyServiceConnection，这样我们就获取Service提供的代理对象

1. 在Activity中可以通过startService和bindService方法启动Service。

注意： 如果想获取Service中提供的代理对象，那么必须通过bindService方法，进行绑定服务。
 使用场景比如：音乐播放器，第三方支付等。

如果仅仅只是为了开启一个后台任务，那么可以使用startService方法。





2020 10.10 10.37
