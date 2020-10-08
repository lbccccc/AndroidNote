https://blog.csdn.net/pgg_cold/article/details/79392652



### BroadcastReceiver两种发送方式

广播BroadcastReceiver被分为两种不同的类型：“**普通广播**（Normal broadcasts）”和“**有序广播**（Ordered broadcasts）”。

普通广播是完全异步的，可以在同一时刻（逻辑上）被所有接收者接收到，消息传递的效率比较高，但缺点是：接收者不能将处理结果传递给下一个接收者，并且无法终止广播Intent的传播；

有序广播是按照接收者声明的优先级别（声明在intent-filter元素的android:priority属性中，数越大优先级别越高,取值范围:-1000到1000。也可以调用IntentFilter对象的setPriority()进行设置），被接收者依次接收广播。如：A的级别高于B,B的级别高于C,那么，广播先传给A，再传给B，最后传给C。A得到广播后，可以往广播里存入数据，当广播传给B时,B可以从广播中得到A存入的数据。

**注意** ：**BroadcastReceiver生命周期很短**
如果需要在onReceiver完成一些耗时操作，应该考虑在Service中开启一个新线程处理耗时操作，**不应该在BroadcastReceiver中开启一个新的线程**，因为BroadcastReceiver生命周期很短，在执行完onReceiver以后就结束，如果开启一个新的线程，可能出现BroadcastRecevier退出以后线程还在，而如果BroadcastReceiver所在的进程结束了，该线程就会被标记为一个空线程。根据Android的内存管理策略，在系统内存紧张的时候，会按照优先级，结束优先级低的线程，而空线程显然优先级最低，这样就可能导致BroadcastReceiver启动的子线程不能完整执行完毕。

AMS：Activity Manager Service ，它是贯穿Android系统组件的核心服务，负责四大组件的启动，运行和调度，应用程序的管理和调度工作；

# Binder机制

Binder是Android进程间通信的核心，整体架构采用C/S架构，客户端进程可以获取服务端的代理，并通过相应的方法去进行进程间通信。关于这个Binder机制，会有一篇专门详细介绍它。

观察者模式：观察者模式（有时又被称为模型-视图（View）模式、源-收听者(Listener)模式或从属者模式）是[软件设计模式](https://baike.baidu.com/item/软件设计模式)的一种。在此种模式中，一个目标物件管理所有相依于它的观察者物件，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实现事件处理系统。

了解了这三个概念之后，就可以来谈一谈广播的实现原理了。

没错，广播的内部实现就是采用观察者模式，将广播的发送者与接收者完全解耦，使得系统易于扩展，方便集成；

（1）模型讲解：

消息订阅者：广播接收者；

消息发送者：广播发送者；

消息中心：AMS；

这里在网上看到一张图，讲的很好，就拷了下来；

![img](https://img-blog.csdn.net/20180227204047918?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGdnX2NvbGQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

看了上面的还有点懵的兄弟，我来通俗的解释一下：

其实这个就像我们生活中订阅报纸是一样的，首先，先问，如果你想每天早上有一份今天的报纸放在你家门口，你会怎么做，你是不得先在某家报纸印刷商订购，交钱之后，人家才会给你每天派送。这就对应了第一步，BroadcastReceiver就相当于你，你订阅报纸，就相当于BroadcastReceiver在AMS上注册，AMS就相当于报纸印刷商，Binder机制就相当于报纸派送员每天骑着自行车往你家门口放报纸。再想一想，报纸印刷商每天的报纸是谁给他的呢，是不是报社给模板资料，所以第二步广播发送者通过Binder机制向AMS发送广播，就相当于，报社通过传真呀，照片呀，pdf文档啥的方式把模板资料发送给印刷商，各种角色都已经搞清楚了，那么下面就好理解了，第三步AMS根据广播发送者要求，在已注册列表中，寻找合适的广播接收者，这就相当于印刷商按照你在他那留下的联系方式，家庭住址，找到你这个人。第四步，AMS将广播发送到合适的广播接收者相应的消息循环队列中，派送员也不可能就送你一家，你得“排队”等着他送过去呢；第五步就轻松了，你拿到报纸了，开始喝着牛奶，看着报纸，开始新的一天了。

看完上面的一大段话，接下来轻松一下，看看下面的实现代码，相信搞懂了上面的兄弟，接下来的代码就很容易理解了；

先创建一个“你”：

 

```html
public class BroadReceiverTest extends BroadcastReceiver {



 



    @Override



    public void onReceive(Context context, Intent intent) {



        String name = intent.getStringExtra("name");



        Log.e("name",name);



    }



}
```

开始订阅：

 

```html
<receiver android:name=".BroadcastFace.BroadReceiverTest">



    <intent-filter>



        <action android:name="com.pgg.haha"/>



    </intent-filter>



</receiver>
```

派送报纸：

 

```html
case R.id.btn_register:



    Intent intent1=new Intent();



    intent1.setAction("com.pgg.haha");



    intent1.putExtra("name","彭港归");



    this.sendBroadcast(intent1);



    break;
```

其实还有一种动态注册的方法，一般都是在Activity的onResume（）方法里调用registerReceiver（BroadReceiver，IntentFilter）注册，然后在onDestroy（）方法里调用unregisterReceiver（BroadcastReceiver）注销。动态注册就是比静态注册多了一个生命周期的问题，它跟随组件的生命周期而变化，而静态注册就是已注册就常驻，不受任何组件影响，就算应用程序关闭，有广播来，依然会接收；

![img](https://img-blog.csdn.net/20180227211306210?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGdnX2NvbGQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**5.本地广播的特点：有大用**

（1）使用它发送的广播将只在自身App内传播，因此你不必担心泄露隐私数据；

（2）其他App无法对你的App发送该广播，因为你的App根本就不可能接收到非自身应用发送的该广播；

（3）比系统广播更加高效；

其高效的主要原因是因为他内部是通过Handler实现的，它的sendBroadcast方法其实是通过handler发送一个message实现的，其内部协作是靠mReceivers,mActions这两个Map集合和一个List集合mPendingBroadcasts，这个list集合存储的就是带接收的广播对象；

也就是因为它是通过Handler实现的，所以别的APP无法对我们的应用发送广播，其他的广播是通过Binder机制实现的，

 

2020 10.7 16.33
