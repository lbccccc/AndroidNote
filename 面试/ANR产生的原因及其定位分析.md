https://blog.csdn.net/a820703048/article/details/74907259

https://www.jianshu.com/p/7fd95bc2a55c

ANR的全称是application not responding，意思就是程序未响应，类似于我们在windows上见到的程序未响应。ANR发生会使用户觉得我们的程序不友好，那么什么情况会导致ANR的发生呢？

首先ANR的发生是有条件限制的，分为以下三点：

1.只有主线程才会产生ANR，主线程就是UI线程；

2.必须发生某些输入事件或特定操作，比如按键或触屏等输入事件，在BroadcastReceiver或Service的各个生命周期调用函数；

3.上述事件响应超时，不同的context规定的上限时间不同

  a.主线程对输入事件5秒内没有处理完毕

  b.主线程在执行BroadcastReceiver的onReceive()函数时10秒内没有处理完毕

  c.主线程在Service的各个生命周期函数时20秒内没有处理完毕。

那么导致ANR的根本原因是什么呢？简单的总结有以下两点：

1.主线程执行了耗时操作，比如数据库操作或网络编程

2.其他进程（就是其他程序）占用CPU导致本进程得不到CPU时间片，比如其他进程的频繁读写操作可能会导致这个问题。

细分的话，导致ANR的原因有如下几点：

1.耗时的网络访问

2.大量的数据读写

3.数据库操作

4.硬件操作（比如camera)

5.调用thread的join()方法、sleep()方法、wait()方法或者等待线程锁的时候

6.service binder的数量达到上限

7.system server中发生WatchDog ANR

8.service忙导致超时无响应

9.其他线程持有锁，导致主线程等待超时

10.其它线程终止或崩溃导致主线程一直等待

那么如何避免ANR的发生呢或者说ANR的解决办法是什么呢？

1.避免在主线程执行耗时操作，所有耗时操作应新开一个子线程完成，然后再在主线程更新UI。

2.BroadcastReceiver要执行耗时操作时应启动一个service，将耗时操作交给service来完成。

3.避免在Intent Receiver里启动一个Activity，因为它会创建一个新的画面，并从当前用户正在运行的程序上抢夺焦点。如果你的应用程序在响应Intent广 播时需要向用户展示什么，你应该使用Notification Manager来实现。



2020 10.13 12.12
