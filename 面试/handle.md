https://www.jianshu.com/p/7a64bd724bc6

https://blog.csdn.net/ly502541243/article/details/87475229

要理解handler的实现原理，其实最重要的是理解`Looper`的实现原理，`Looper`才是实现`handler`机制的核心。任何一个`handler`在使用`sendMessage`或者`post`时候，都是先构造一个`Message`，并把自己放到`message中`，然后把`Message`放到对应的`Looper`的`MessageQueue`，`Looper`通过控制`MessageQueue`来获取`message`执行其中的`handler`或者`runnable`。 要在当前线程中执行`handler`指定操作，必须要先看当前线程中有没有`looper`，如果有`looper`，`handler`就会通过`sendMessage`，或者`post`先构造一个`message`，然后把`message`放到当前线程的`looper`中，`looper`会在当前线程中循环取出`message`执行，如果没有`looper`，就要通过`looper.prepare()`方法在当前线程中构建一个`looper`，然后主动执行`looper.loop()`来实现循环。

梳理一下其实最简单的就下面四条：

1、每一个线程中最多只有一个`Looper`，通过`ThreadLocal`来保存，`Looper`中有`Message`队列，保存`handler`并且执行`handler`发送的`message`。

2、在线程中通过`Looper.prepare()`来创建`Looper`，并且通过`ThreadLocal`来保存`Looper`，每一个线程中只能调用一次`Looper.prepare()`，也就是说一个线程中最多只有一个`Looper`，这样可以保证线程中`Looper`的唯一性。

3、`handler`中执行`sendMessage`或者`post`操作，这些操作执行的线程是`handler`中`Looper`所在的线程，和`handler`在哪里创建没关系，和`Handler`中的`Looper`在那创建有关系。

4、一个线程中只能有一个`Looper`，但是一个`Looper`可以对应多个`handler`，在同一个`Looper`中的消息都在同一条线程中执行。

## **2、Handler机制，sendMessage和post（Runnable）的区别？**

要看`sendMessage`和`post`区别，需要从源码来看，下面是几种使用`handler`的方式，先看下这些方式，然后再从源码分析有什么区别。 **例1、 主线程中使用`handler`**



```java
  //主线程
          Handler mHandler = new Handler(new Handler.Callback() {
              @Override
              public boolean handleMessage(@NonNull Message msg) {
                  if (msg.what == 1) {
                      //doing something
                  }
                  return false;
              }
          });
          Message msg = Message.obtain();
          msg.what = 1;
          mHandler.sendMessage(msg);
```

上面是在主线程中使用`handler`，因为在`Android`中系统已经在主线程中生成了`Looper`，所以不需要自己来进行`looper`的生成。如果上面的代码在子线程中执行，就会报



```rust
  Can't create handler inside thread " + Thread.currentThread()
                          + " that has not called Looper.prepare()
```

如果想着子线程中处理`handler`的操作，就要必须要自己生成`Looper`了。

**例2 、子线程中使用handler**



```java
          Thread thread=new Thread(new Runnable() {
              @Override
              public void run() {
                  Looper.prepare();
                  Handler handler=new Handler();
                  handler.post(new Runnable() {
                      @Override
                      public void run() {

                      }
                  });
                  Looper.loop();
              }
          });
```

上面在`Thread`中使用`handler`，先执行`Looper.prepare`方法，来在当前线程中生成一个`Looper`对象并保存在当前线程的`ThreadLocal`中。 看下`Looper.prepare()`中的源码：



```csharp
  //prepare
      private static void prepare(boolean quitAllowed) {
          if (sThreadLocal.get() != null) {
              throw new RuntimeException("Only one Looper may be created per thread");
          }
          sThreadLocal.set(new Looper(quitAllowed));
      }
  //Looper
      private Looper(boolean quitAllowed) {
          mQueue = new MessageQueue(quitAllowed);
          mThread = Thread.currentThread();
      }
```

可以看到`prepare`方法中会先从`sThreadLocal`中取如果之前已经生成过`Looper`就会报错，否则就会生成一个新的`Looper`并且保存在线程的`ThreadLocal`中，这样可以确保每一个线程中只能有一个唯一的`Looper`。

另外：由于`Looper`中拥有当前线程的引用，所以有时候可以用`Looper`的这种特点来判断当前线程是不是主线程。



```java
      @RequiresApi(api = Build.VERSION_CODES.KITKAT)
      boolean isMainThread() {
          return Objects.requireNonNull(Looper.myLooper()).getThread() == 
  Looper.getMainLooper().getThread();
      }
```

**sendMessage vs post**

**先来看看`sendMessage`的代码调用链：**

![img](https:////upload-images.jianshu.io/upload_images/3117364-77e3c5d881039e7a.png?imageMogr2/auto-orient/strip|imageView2/2/w/765/format/webp)

`enqueueMessage`源码如下：



```cpp
      private boolean enqueueMessage(@NonNull MessageQueue queue, @NonNull Message msg,
              long uptimeMillis) {
          msg.target = this;
          msg.workSourceUid = ThreadLocalWorkSource.getUid();
          return queue.enqueueMessage(msg, uptimeMillis);
      }
```

`enqueueMessage`的代码处理很简单，`msg.target = this;`就是把当前的`handler`对象给`message.target`。然后再讲`message`进入到队列中。

**post代码调用链：**

![img](https:////upload-images.jianshu.io/upload_images/3117364-36887af3b1c50150.png?imageMogr2/auto-orient/strip|imageView2/2/w/917/format/webp)

调用`post`时候会先调用`getPostMessage`生成一个`Message`，后面和`sendMessage`的流程一样。下面看下`getPostMessage`方法的源码：



```cpp
      private static Message getPostMessage(Runnable r) {
          Message m = Message.obtain();
          m.callback = r;
          return m;
      }
```

可以看到`getPostMessage`中会先生成一个`Messgae`，并且把`runnable`赋值给`message`的`callback.`消息都放到`MessageQueue`中后，看下`Looper`是如何处理的。



```kotlin
      for (;;) {
          Message msg = queue.next(); // might block
          if (msg == null) {
              return;
          }
          msg.target.dispatchMessage(msg);
      }
```

`Looper`中会遍历`message`列表，当`message`不为`null`时调用`msg.target.dispatchMessage(msg)`方法。看下`message`结构：

![img](https:////upload-images.jianshu.io/upload_images/3117364-31404e2c15c65da9.png?imageMogr2/auto-orient/strip|imageView2/2/w/751/format/webp)

也就是说`msg.target.dispatchMessage`方法其实就是调用的`Handler中的dispatchMessage`方法，下面看下`dispatchMessage`方法的源码：



```csharp
      public void dispatchMessage(@NonNull Message msg) {
          if (msg.callback != null) {
              handleCallback(msg);
          } else {
              if (mCallback != null) {
                  if (mCallback.handleMessage(msg)) {
                      return;
                  }
              }
              handleMessage(msg);
          }
      }
  //
   private static void handleCallback(Message message) {
          message.callback.run();
      }
```

因为调用`post`方法时生成的`message.callback=runnable`,所以`dispatchMessage`方法中会直接调用 `message.callback.run();`也就是说直接执行`post`中的`runnable`方法。 而`sendMessage`中如果`mCallback`不为`null`就会调用`mCallback.handleMessage(msg)`方法，否则会直接调用`handleMessage`方法。

**总结** **`post`方法和`handleMessage`方法的不同在于，`post`的`runnable`会直接在`callback`中调用`run`方法执行，而`sendMessage`方法要用户主动重写`mCallback`或者`handleMessage`方法来处理。**

## **3、Looper会一直消耗系统资源吗？**

首先给出结论，`Looper`不会一直消耗系统资源，当`Looper`的`MessageQueue`中没有消息时，或者定时消息没到执行时间时，当前持有`Looper`的线程就会进入阻塞状态。

![img](https:////upload-images.jianshu.io/upload_images/3117364-80eacd6aacfbbe3c.png?imageMogr2/auto-orient/strip|imageView2/2/w/876/format/webp)

下面看下`looper`所在的线程是如何进入阻塞状态的。`looper`阻塞肯定跟消息出队有关，因此看下消息出队的代码。

**消息出队**



```java
     Message next() {
          // Return here if the message loop has already quit and been disposed.
          // This can happen if the application tries to restart a looper after quit
          // which is not supported.
          final long ptr = mPtr;
          if (ptr == 0) {
              return null;
          }
          int nextPollTimeoutMillis = 0;
          for (;;) {
              if (nextPollTimeoutMillis != 0) {
                  Binder.flushPendingCommands();
              }
              nativePollOnce(ptr, nextPollTimeoutMillis);
              // While calling an idle handler, a new message could have been delivered
              // so go back and look again for a pending message without waiting.
              if(hasNoMessage)
              {
              nextPollTimeoutMillis =-1；
              }
          }
      }
```

上面的消息出队方法被简写了，主要看下面这段，没有消息的时候`nextPollTimeoutMillis=-1`；



```undefined
    if(hasNoMessage)
            {
            nextPollTimeoutMillis =-1；
            }
```

看for循环里面这个字段所其的作用：



```undefined
   if (nextPollTimeoutMillis != 0) {
                  Binder.flushPendingCommands();
              }
    nativePollOnce(ptr, nextPollTimeoutMillis);
```

`Binder.flushPendingCommands();`这个方法的作用可以看源码里面给出的解释：



```dart
      /**
       * Flush any Binder commands pending in the current thread to the kernel
       * driver.  This can be
       * useful to call before performing an operation that may block for a long
       * time, to ensure that any pending object references have been released
       * in order to prevent the process from holding on to objects longer than
       * it needs to.
       */
```

也就是说在用户线程要进入阻塞之前跟内核线程发送消息，防止用户线程长时间的持有某个对象。再看看下面这个方法：`nativePollOnce(ptr, nextPollTimeoutMillis);`当`nextPollingTimeOutMillis=-1`时，这个`native`方法会阻塞当前线程，线程阻塞后，等下次有消息入队才会重新进入可运行状态，所以`Looper`并不会一直死循环消耗运行内存，对队列中的颜色消息还没到时间时也会阻塞当前线程，但是会有一个阻塞时间也就是`nextPollingTimeOutMillis>0`的时间。

当消息队列中没有消息的时候looper肯定是被消息入队唤醒的。

**消息入队**



```csharp
  boolean enqueueMessage(Message msg, long when) {
          if (msg.target == null) {
              throw new IllegalArgumentException("Message must have a target.");
          }
          if (msg.isInUse()) {
              throw new IllegalStateException(msg + " This message is already in use.");
          }

          synchronized (this) {
              if (mQuitting) {
                  IllegalStateException e = new IllegalStateException(
                          msg.target + " sending message to a Handler on a dead thread");
                  Log.w(TAG, e.getMessage(), e);
                  msg.recycle();
                  return false;
              }

              msg.markInUse();
              msg.when = when;
              Message p = mMessages;
              boolean needWake;
              if (p == null || when == 0 || when < p.when) {
                  // New head, wake up the event queue if blocked.
                  msg.next = p;
                  mMessages = msg;
                  needWake = mBlocked;
              } else {
                  // Inserted within the middle of the queue.  Usually we don't have to wake
                  // up the event queue unless there is a barrier at the head of the queue
                  // and the message is the earliest asynchronous message in the queue.
                  needWake = mBlocked && p.target == null && msg.isAsynchronous();
                  Message prev;
                  for (;;) {
                      prev = p;
                      p = p.next;
                      if (p == null || when < p.when) {
                          break;
                      }
                      if (needWake && p.isAsynchronous()) {
                          needWake = false;
                      }
                  }
                  msg.next = p; // invariant: p == prev.next
                  prev.next = msg;
              }

              // We can assume mPtr != 0 because mQuitting is false.
              if (needWake) {
                  nativeWake(mPtr);
              }
          }
          return true;
      }
```

上面可以看到消息入队之后会有一个



```undefined
  if (needWake) {
                nativeWake(mPtr);
            }
```

方法，调用这个方法就可以唤醒线程了。另外消息入队的时候是根据消息的`delay`时间来在链表中排序的，`delay`时间长的排在后面，时间短的排在前面。如果时间相同那么按插入时间先后来排，插入时间早的在前面，插入时间晚的在后面。

## **4、android的Handle机制，Looper关系，主线程的Handler是怎么判断收到的消息是哪个Handler传来的？**

`Looper`是如何判断`Message`是从哪个`handler`传来的呢？其实很简单，在`1`中分析过，`handler`在`sendMessage`的时候会构建一个`Message`对象，并且把自己放在`Message`的`target`里面，这样的话`Looper`就可以根据`Message`中的`target`来判断当前的消息是哪个`handler`传来的。

## 5、Handler机制流程、Looper中延迟消息谁来唤醒Looper？

从3中知道在消息出队的`for`循环队列中会调用到下面的方法。



```undefined
  nativePollOnce(ptr, nextPollTimeoutMillis);
```

如果是延时消息，会在被阻塞`nextPollTimeoutMillis`时间后被叫醒，`nextPollTimeoutMillis`就是消息要执行的时间和当前的时间差。

## **6、Handler是如何引起内存泄漏的？如何解决？**

在子线程中，如果手动为其创建`Looper`，那么在所有的事情完成以后应该调用`quit`方法来终止消息循环，否则这个子线程就会一直处于等待的状态，而如果退出`Looper`以后，这个线程就会立刻终止，因此建议不需要的时候终止`Looper`。



```css
  Looper.myLooper().quit()
```

那么，如果在`Handler`的`handleMessage`方法中（或者是run方法）处理消息，如果这个是一个延时消息，会一直保存在主线程的消息队列里，并且会影响系统对`Activity`的回收，造成内存泄露。

具体可以参考`Handler`内存泄漏分析及解决

**总结一下，解决`Handler`内存泄露主要2点**

1 、有延时消息，要在`Activity`销毁的时候移除`Messages`

2、 匿名内部类导致的泄露改为匿名静态内部类，并且对上下文或者`Activity`使用弱引用。

## **7、handler机制中如何确保Looper的唯一性？**



`Looper`是保存在线程的`ThreadLocal`里面的，使用`Handler`的时候要调用`Looper.prepare()`来创建一个`Looper`并放在当前的线程的`ThreadLocal`里面。



```csharp
      private static void prepare(boolean quitAllowed) {
          if (sThreadLocal.get() != null) {
              throw new RuntimeException("Only one Looper may be created per thread");
          }
          sThreadLocal.set(new Looper(quitAllowed));
      }
```

可以看到，如果多次调用`prepare`的时候就会报`Only one Looper may be created per thread`，所以这样就可以保证一个线程中只有唯一的一个`Looper`。

## **8、Handler 是如何能够线程切换，发送Message的？**

`handler`的执行跟创建`handler`的线程无关，跟创建`looper`的线程相关，加入在子线程中创建一个`Handler`，但是`Handler`相关的`Looper`是主线程的，这样，如果`handler`执行`post`一个`runnable`，或者`sendMessage`，最终的`handle Message`都是在主线程中执行的。

## 9、主线程的死循环一直运行是不是特别消耗CPU资源呢？

https://www.cnblogs.com/muouren/p/11706457.html

其实不然，这里就涉及到**Linux pipe/epoll机制**，简单说就是在主线程的MessageQueue没有消息时，便阻塞在loop的queue.next()中的nativePollOnce()方法里，详情见[Android消息机制1-Handler(Java层)](https://link.zhihu.com/?target=http://www.yuanhh.com/2015/12/26/handler-message-framework/%23next)，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。这里采用的epoll机制，是一种IO多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作，本质同步I/O，即读写是阻塞的。 **所以说，主线程大多数时候都是处于休眠状态，并不会消耗大量CPU资源。**

2020 10.7 23.38
