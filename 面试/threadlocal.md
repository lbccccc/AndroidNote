https://www.zhihu.com/question/341005993/answer/1367225682

ThreadLocal的作用主要是做数据隔离，填充的数据只属于当前线程，变量的数据对别的线程而言是相对隔离的，在多线程环境下，如何防止自己的变量被其它线程篡改。

```text
ThreadLocal<String> localName = new ThreadLocal();
localName.set("张三");
String name = localName.get();
localName.remove();
```

其实使用真的很简单，线程进来之后初始化一个可以泛型的ThreadLocal对象，之后这个线程只要在remove之前去get，都能拿到之前set的值，注意这里我说的是remove之前。

他是能做到线程间数据隔离的，所以别的线程使用get（）方法是没办法拿到其他线程的值的，但是有办法可以做到，我后面会说。

我们先看看他set的源码：

```text
public void set(T value) {
    Thread t = Thread.currentThread();// 获取当前线程
    ThreadLocalMap map = getMap(t);// 获取ThreadLocalMap对象
    if (map != null) // 校验对象是否为空
        map.set(this, value); // 不为空set
    else
        createMap(t, value); // 为空创建一个map对象
}
```

大家可以发现set的源码很简单，主要就是ThreadLocalMap我们需要关注一下，而ThreadLocalMap呢是当前线程Thread一个叫threadLocals的变量中获取的。

为什么需要数组呢？没有了链表怎么解决Hash冲突呢？
用数组是因为，我们开发过程中可以一个线程可以有多个TreadLocal来存放不同类型的对象的，但是他们都将放到你当前线程的ThreadLocalMap里，所以肯定要数组来存。


2020 10.24 23.35
