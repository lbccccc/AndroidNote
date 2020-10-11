https://blog.csdn.net/qq_43377749/article/details/102979418

其设计用意在于：

1. 封装。对数据进行封装，提供统一的接口，使用者完全不必关心这些数据是在 `DB` ，`XML` 、`Preferences` 或者网络请求来的。当项目需求要改变数据来源时，使用我们的地方完全不需要修改。
2. 提供一种跨进程数据共享的方式。
3. 应用程序间的数据共享还有另外的一个重要话题，就是数据更新通知机制了。因为数据是在多个应用程序中共享的，当其中一个应用程序改变了这些共享数据的时候，它有责任通知其它应用程序，让它们知道共享数据被修改了，这样它们就可以作相应的处理。





##  为什么要用 ContentProvider ?它和 sql 的实现上有什么差别?

- `ContentProvider` 屏蔽了数据存储的细节 , 内部实现对用户完全透明 , 用户只需要关心操作数据的 `uri` 就可以了, `ContentProvider` 可以实现不同 `app`之间 共享。
- `Sql` 也有增删改查的方法, 但是 `sql` 只能查询本应用下的数据库。
- 而 `ContentProvider` 还可以去增删改查本地文件. `xml` 文件的读取等。





#### 每个 ContentProvider 的操作是在哪个线程中运行的呢?（ 其实我们关心的是 UI 线程和工作线程 ）
https://blog.csdn.net/static_zh/article/details/82899312

- 比如我们在UI线程调用getContentResolver().query查询数据，而当数据量很大时（或者需要进行较长时间的计算）会不会阻塞UI线程呢？
- 要分两种情况

1. `ContentProvider` 和调用者在同一个进程，`ContentProvider` 的方法（ `query/insert/update/delete` 等 ）和调用者在同一线程中；
2. `ContentProvider` 和调用者在不同的进程，`ContentProvider` 的方法会运行在它自身所在进程的一个 Binder 线程中。
   但是，注意这两种方式在 `ContentProvider` 的方法没有执行完成前都会 `blocked` 调用者。所以你应该知道这个上面这个问题的答案了吧。
3. 也可以看看 `CursorLoader` 这个类的源码，看 `Google` 自己是怎么使用 `getContentResolver().query` 的。





## ContentProvider 是如何在不同应用程序之间传输数据的？

- 一个应用进程有 `16` 个 `Binder` 线程去和远程服务进行交互，而每个线程可占用的缓存空间是 `128KB` 这样，超过会报异常。
- `ContentResolver` 虽然是通过 `Binder` 进程间通信机制打通了应用程序之间共享数据的通道，但 `ContentProvider` 组件在不同应用程序之间传输数据是基于匿名共享内存机制来实现的。



2020 10.7 21.11
