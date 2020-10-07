https://www.jianshu.com/p/57ba91db3705
### 为什么使用binder

在上面这些可供选择的方式中，Android使用得最多也最被认可的还是Binder机制。

因为Binder更加简洁和快速，消耗的内存资源更小吗？不错，这些也正是Binder的优点。

当然，也还有很多其他原因，比如传统的进程间通信可能会增加进程的开销，而且有进程过载和安全漏洞等方面的风险，Binder正好能解决和避免这些问题。

**Binder主要能提供以下一些功能：**

用驱动程序来推进进程间的通信。

通过共享内存来提高性能。

为进程请求分配每个进程的线程池。

针对系统中的对象引入了引用计数和跨进程的对象引用映射。

进程间同步调用。



### 什么是binder

1. 通常意义下, Binder指的是一种通信机制
2. 对于 Server进程来说, Binder指的是 Binder本地对象，对于Client来说, Binder指的是 Binder代理对象
3. 对于传输过程而言, Binder是可以进行跨进程传递的对象

### 3、binder通信模型

Binder的通信模型有4个角色：Binder Client、Binder Server、Binder Driver（Binder驱动）、ServiceManager。（图片点开链接）

可以看到，ServiceManager、Binder Client、Binder Server处于不同的进程，他们三个都在用户空间，而Binder驱动在内核空间。

那先来简述一下这个通信模型：

首先是有一个ServiceManager，刚开始这个通讯录是空白的，然后Server进程向ServiceManager注册一个映射关系表，之后Client进程想要和Server进程通信，首先向ServiceManager查询地址，ServiceManager收到查询的请求之后，返回查询结果给Client。

注意到这里不管是Server进程注册，还是Client查询，都是经过Binder驱动的，这也真是Binder驱动的作用所在，

1． Server进程向ServiceManager注册，告诉ServiceManager我是谁，我有什么，我能做什么。就好比徐同学（Server进程）有一台笔记本（computer对象），这台笔记本有个add方法。这时映射关系表就生成了。

2． Client进程向ServiceManager查询，我要调用Server进程的computer对象的add方法，可以看到这个过程经过Binder驱动，这时候Binder驱动就开始发挥他的作用了。当向ServiceManager查询完毕，是返回一个computer对象给Client进程吗？其实不然，Binder驱动将computer对象转换成了computerProxy对象，并转发给了Client进程，因此，Client进程拿到的并不是真实的computer对象，而是一个代理对象，即computerProxy对象。很容易理解这个computerProxy对象也是有add方法，（如果连add方法都没有，岂不是欺骗了Client？），但是这个add方法只是对参数进行一些包装而已。

3． 当Client进程调用add方法，这个消息发送给Binder驱动，这时驱动发现，原来是computerProxy，那么Client进程应该是需要调用computer对象的add方法的，这时驱动通知Server进程，调用你的computer对象的add方法，将结果给我。然后Server进程就将计算结果发送给驱动，驱动再转发给Client进程，这时Client进程还蒙在了鼓里，他以为自己调用的是真实的computer对象的add方法，其实他只是调用了代理而已。不过Client最终还是拿到了计算结果。

好了，一个通信过程就完成了。我们发现，其实Binder驱动就是一个中转。

2020 10.7 16.51
