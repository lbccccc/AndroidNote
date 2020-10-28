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



# ThreadLocalMap初始化及扩容

https://blog.csdn.net/u010002184/article/details/82227795

扩容经过以下步骤：
1 private T setInitialValue() { //源码
2 map.set(this, value);//源码
3 if (!cleanSomeSlots(i, sz) && sz >= threshold)//源码，cleanSomeSlots使用expungeStaleEntry()，提前移走过期Entry
4 rehash();//源码
5 expungeStaleEntries();//源码，使用expungeStaleEntry()
6 if (size >= threshold - threshold / 4)//源码，10-10/4=8，在清理过期Entery后如果长度大于等于8，则进行扩容，
7 resize();//源码， int newLen = oldLen * 2;如果长度是8，则会扩容到16，到原来的2倍
8 setThreshold(newLen);//源码，新的门限是新长度的2/3

2/3 * 3/4 = 1/2 ,即长度大于等于原长度的1/2时，会扩容到原来的2倍

在清理过期Entery后如果长度大于等于原长度的2/3时会进行rehash，再次清理过期Entery后如果长度大于等于原长度的1/2时会进行扩容。

过期Entry的意思就是ThreadLocal<HashMap> totalMap 该变量为空，里面没有内容了，



2020 10.24 23.35
