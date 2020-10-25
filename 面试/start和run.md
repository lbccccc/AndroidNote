https://zhuanlan.zhihu.com/p/139878234

- 1、 **NEW 表示线程创建成功，但没有运行** ，在 new Thread 之后，没有 start 之前，线程都处于 NEW 状态；
- 2、 **RUNNABLE 表示线程正在运行中** ，当我们运行 strat 方法，子线程被创建成功之后，子线程的状态变成 RUNNABLE；
- 3、 **TERMINATED 表示线程已经运行结束** ，子线程运行完成、被打断、被中止，状态都会从 RUNNABLE 变成 TERMINATED；
- 4、 **BLOCKED 表示线程被阻塞** ，如果线程正好在等待获得 monitor lock 锁，比如在等待进入 synchronized 修饰的代码块或方法时，会从 RUNNABLE 变成 BLOCKED；
- 5、 **WAITING 和 TIMED_WAITING 都表示等待** ，现在在遇到 Object#wait、Thread#join、 LockSupport#park 这些方法时，线程就会等待另一个线程执行完特定的动作之后，才能结 束等待，只不过 TIMED_WAITING 是带有等待时间的；

### 优先级

优先级代表线程执行的机会的大小，优先级高的可能先执行，低的可能后执行。

在 Java 源码中，优先级从低到高分别是 1 到 10，线程默认 new 出来的优先级都是 5

start 方法的源码也没几行代码，注释也比较详细，最主要的是 start0() 方法，这个后面在解释。再来看看 run() 方法的源码：

```text
    @Override
    public void run() {
     // 简单的运行，不会新起线程，target 是 Runnable
        if (target != null) {
            target.run();
        }
    }
```

run() 方法的源码就比较简单的，就是一个普通方法的调用，这也印证了我们上面的结论。

接下来我们就来说一说这个 start0() 这个方法，这个是真正实现多线程的关键，start0() 代码如下：

```text
private native void start0();
```

start0 被标记成 native ，也就是本地方法，并不需要我们去实现或者了解，**为什么 start0() 会标记成 native **？

start() 方法调用 start0() 方法后，该线程并不一定会立马执行，只是将线程变成了可运行状态（NEW ---> RUNNABLE）。具体什么时候执行，取决于 CPU ，由 CPU 统一调度。

我们又知道 Java 是跨平台的，可以在不同系统上运行，每个系统的 CPU 调度算法不一样，所以就需要做不同的处理，这件事情就只能交给 JVM 来实现了，start0() 方法自然就表标记成了 native。

最后，总结一下，Java 中实现真正的多线程是 start 中的 start0() 方法，run() 方法只是一个普通的方法。





2020 10.25 11.08
