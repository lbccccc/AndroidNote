# 线程间通信的原理,以及Android中的多线程

## 线程间交互

- ⼀个线程启动别的线程：new Thread().start()、Executor.execute() 等
- ⼀个线程终结另⼀个线程
  - Thread.stop() 
  - Thread.interrupt()：温和式终结：不⽴即、不强制 
    - interrupted() 和 isInterrupted()：检查（和重置）中断状态 
    - InterruptedException：如果在线程「等待」时中断，或者在中断状态「等待」，直 接结束等待过程（因为等待过程什么也不会做，⽽ interrupt() 的⽬的是让线程做完收 尾⼯作后尽快终结，所以要跳过等待过程）
- Object.wait() 和 Object.notify() / notifyAll() 
  - 在未达到⽬标时 wait() 
  - ⽤ while 循环检查 
  - 设置完成后 notifyAll() 
  - wait() 和 notify() / notifyAll() 都需要放在同步代码块⾥
- Thread.join()：让另⼀个线程插在⾃⼰前⾯ 
- Thread.yield()：暂时让出⾃⼰的时间⽚给同优先级的线程

# Android 的 Handler 机制

- 本质：在某个指定的运⾏中的线程上执⾏代码 

- 思路：在接受任务的线程上执⾏循环判断

- 基本实现：

  - Thread ⾥ while 循环检查 
  - 加上 Looper（优势在于⾃定义 Thread 的代码可以少写很多）
  - 再加上 Handler（优势在于功能分拆，⽽且可以有多个 Handler）

- Java 的 Handler 机制：

  - HandlerThread：具体的线程
  - Looper：负责循环、条件判断和任务执⾏ 
  - Handler：负责任务的定制和线程间传递

- ThreadLocal：

  - 每一个线程对主内存里面的同一个数据有专属于自己的内存区域进行保存和读取，互不影响

- AsyncTask：

  - AsyncTask 的内存泄露

    - 众所周知的原因：AsyncTask 持有外部 Activity 的引⽤

    - 没提到的原因：执⾏中的线程不会被系统回收

    - Java 回收策略：没有被 GC Root 直接或间接持有引⽤的对象，会被回收

      GC Root： 

      1. 运⾏中的线程 
      2. 静态对象 
      3. 来⾃ native code 中的引⽤

    - 所以： AsyncTask 的内存泄露，其他类型的线程⽅案（Thread、Executor、 HandlerThread）⼀样都有，所以不要忽略它们，或者认为 AsyncTask ⽐别的⽅ 案更危险。并没有。

    - 就算是使⽤ AsyncTask，只要任务的时间不⻓（例如 10 秒之内），那就完全没 必要做防⽌内存泄露的处理。

## Service 和 IntentService 

- Service：后台任务的活动空间。适⽤场景：⾳乐播放器等。 
- IntentService：执⾏单个任务后⾃动关闭的 Service

## Executor、AsyncTask、HandlerThead、IntentService 的选 择

原则：哪个简单⽤哪个

- 能⽤ Executor 就⽤ Executor 
- 需要⽤到「后台线程推送任务到 UI 线程」时，再考虑 AsyncTask 或者 Handler 
- HandlerThread 的使⽤场景：原本它设计的使⽤场景是「在已经运⾏的指定线程上执⾏代 码」，但现实开发中，除了主线程之外，⼏乎没有这种需求，因为 HandlerThread 和 Executor 相⽐在实际应⽤中并没什么优势，反⽽⽤起来会麻烦⼀点。不过，这⼆者喜欢⽤谁就⽤谁吧。 
- IntentService：⾸先，它是⼀个 Service；另外，它在处理线程本身，没有⽐ Executor 有任何优 势

### 关于 Executor 和 HandlerThread 的关闭

如果在界⾯组件⾥创建 Executor 或者 HandlerThread，记得要在关闭的时候（例如 Activity.onDestroy() ）关闭 Executor 和 HandlerThread。

```
@Override
protected void onDestroy() {
 super.onDestroy();
 executor.shutdown();
}
```

```
@Override
protected void onDestroy() {
 super.onDestroy();
 handlerThread.quit(); // 这个其实就是停⽌ Looper 的循环
}
```

2020 7.19 17:20
