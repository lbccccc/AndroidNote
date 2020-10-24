https://zhuanlan.zhihu.com/p/24847247

# context

Context的中文翻译为：语境; 上下文; 背景; 环境，在开发中我们经常说称之为“上下文”，那么这个“上下文”到底是指什么意思呢？在语文中，我们可以理解为语境，在程序中，我们可以理解为当前对象在程序中所处的一个环境，一个与系统交互的过程。比如微信聊天，此时的“环境”是指聊天的界面以及相关的数据请求与传输，Context在加载资源、启动Activity、获取系统服务、创建View等操作都要参与。



那Context到底是什么呢？一个Activity就是一个Context，一个Service也是一个Context。Android程序员把“场景”抽象为Context类，他们认为用户和操作系统的每一次交互都是一个场景，比如打电话、发短信，这些都是一个有界面的场景，还有一些没有界面的场景，比如后台运行的服务（Service）。一个应用程序可以认为是一个工作环境，用户在这个环境中会切换到不同的场景，这就像一个前台秘书，她可能需要接待客人，可能要打印文件，还可能要接听客户电话，而这些就称之为不同的场景，前台秘书可以称之为一个应用程序。

源码中的注释是这么来解释Context的：Context提供了关于应用环境全局信息的接口。它是一个抽象类，它的执行被Android系统所提供。它允许获取以应用为特征的资源和类型，是一个统领一些资源（应用程序环境变量等）的上下文。就是说，它描述一个应用程序环境的信息（即上下文）；是一个抽象类，Android提供了该抽象类的具体实现类；通过它我们可以获取应用程序的资源和类（包括应用级别操作，如启动Activity，发广播，接受Intent等）。既然上面Context是一个抽象类，那么肯定有他的实现类咯，我们在Context的源码中通过IDE可以查看到他的子类最终可以得到如下图：

![img](https://pic2.zhimg.com/80/v2-cbc754d51e79f15df7fd0070c851dfc9_720w.png)



Context.png

Context类本身是一个纯abstract类，它有两个具体的实现子类：ContextImpl和ContextWrapper。其中ContextWrapper类，如其名所言，这只是一个包装而已，ContextWrapper构造函数中必须包含一个真正的Context引用，同时ContextWrapper中提供了attachBaseContext（）用于给ContextWrapper对象中指定真正的Context对象，调用ContextWrapper的方法都会被转向其所包含的真正的Context对象。ContextThemeWrapper类，如其名所言，其内部包含了与主题（Theme）相关的接口，这里所说的主题就是指在AndroidManifest.xml中通过android：theme为Application元素或者Activity元素指定的主题。当然，只有Activity才需要主题，Service是不需要主题的，因为Service是没有界面的后台场景，所以Service直接继承于ContextWrapper，Application同理。而ContextImpl类则真正实现了Context中的所以函数，应用程序中所调用的各种Context类的方法，其实现均来自于该类。一句话总结：Context的两个子类分工明确，其中ContextImpl是Context的具体实现类，ContextWrapper是Context的包装类。Activity，Application，Service虽都继承自ContextWrapper（Activity继承自ContextWrapper的子类ContextThemeWrapper），但它们初始化的过程中都会创建ContextImpl对象，由ContextImpl实现Context中的方法。



一个应用程序有几个Context



其实这个问题本身并没有什么意义，关键还是在于对Context的理解，从上面的关系图我们已经可以得出答案了，在应用程序中Context的具体实现子类就是：Activity，Service，Application。那么Context数量=Activity数量+Service数量+1。当然如果你足够细心，可能会有疑问：我们常说四大组件，这里怎么只有Activity，Service持有Context，那Broadcast Receiver，Content Provider呢？Broadcast Receiver，Content Provider并不是Context的子类，他们所持有的Context都是其他地方传过去的，所以并不计入Context总数。上面的关系图也从另外一个侧面告诉我们Context类在整个Android系统中的地位是多么的崇高，因为很显然Activity，Service，Application都是其子类，其地位和作用不言而喻。

Context作用域



虽然Context神通广大，但并不是随便拿到一个Context实例就可以为所欲为，它的使用还是有一些规则限制的。由于Context的具体实例是由ContextImpl类去实现的，因此在绝大多数场景下，Activity、Service和Application这三种类型的Context都是可以通用的。不过有几种场景比较特殊，比如启动Activity，还有弹出Dialog。出于安全原因的考虑，Android是不允许Activity或Dialog凭空出现的，一个Activity的启动必须要建立在另一个Activity的基础之上，也就是以此形成的返回栈。而Dialog则必须在一个Activity上面弹出（除非是System Alert类型的Dialog），因此在这种场景下，我们只能使用Activity类型的Context，否则将会出错。





https://zhuanlan.zhihu.com/p/143584021

# application

**面试官**：说一下 Application 的初始化流程

大神：Application 的初始化是在应用进程创建完成后：

- ActivityThread 调用 AMS 的 Binder 对象（ IActivityManager ）的 attachApplication 方法
- AMS 收到请求后再去调用 ActivityThread 的 bindApplication 方法
- ActivityThread 这边收到请求再组装一个 AppBindData 对象，把所有参数封装进去，再通过 handler 发到主线程执行
- 主线程 loop 到这条消息，调用 handleBindApplication 来真正处理初始化 Application

handleBindApplication 和我们谈 “Context” 那次，Activity 的初始化差不多。回顾一下：

- ClassLoader 加载 Application 类，实例化
- 初始化 Applicaction 用的 ContextImpl
- 通过 Application.attach( Context ) 方法，调用 `attachBaseContext( Context )` 将 ContextImpl 注入到 Application
- 最后调用 Application.OnCreate()

这样 Application 就初始化完成了

**面试官**：为什么进程创建完成不直接调 handleBindApplication 去创建 Application 呢，又去 AMS 那边绕了一圈

大神：调用 AMS 的 attachApplication 不仅仅是为了创建 Application ，还有在进程创建前可能调用了应用的四大组件却没办法启动；现在进程创建好了，创建好 Application 也要处理这些待启动的组件。所以需要通过 AMS 统一调度，如果 Application 的创建及 onCreate 回调耗时的话，也会影响这些待启动组件的启动时间

**面试官**：可以，我们再来聊聊别的。





2020 10.24 23.04
