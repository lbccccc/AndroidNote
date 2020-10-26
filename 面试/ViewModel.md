https://www.jianshu.com/p/35d143e84d42

1、数据持久化
我们知道在屏幕旋转的 时候 会经历 activity 的销毁与重新创建，这里就涉及到数据保存的问题，显然重新请求或加载数据是不友好的。在 ViewModel 出现之前我们可以用 activity 的onSaveInstanceState()机制保存和恢复数据，但缺点很明显，onSaveInstanceState只适合保存少量的可以被序列化、反序列化的数据，假如我们需要保存是一个比较大的 bitmap list ，这种机制明显不合适。
由于 ViewModel 的特殊设计，可以解决此痛点。

2、异步回调问题
通常我们 app 需要频繁异步请求数据，比如调接口请求服务器数据。当然这些请求的回调都是相当耗时的，之前我们在 Activity 或 fragment里接收这些回调。所以不得不考虑潜在的内存泄漏情况，比如 Activity 被销毁后接口请求才返回。处理这些问题，会给我们增添好多复杂的工作。
但现在我们利用 ViewModel 处理数据回调，可以完美的解决此痛点。

3、分担 UI controller负担
从最早的 MVC 到目前流行的 MVP、MVVM，目的无非是 明确职责，分离 UI controller 负担。
UI controller 比如 Activity 、Fragment 是设计用来渲染展示数据、响应用户行为、处理系统的某些交互。如果再要求他去负责加载网络或数据库数据，会让其显得臃肿和难以管理。所以为了简洁、清爽、丝滑，我们可以分离出数据操作的职责给 ViewModel。

4、Fragments 间共享数据
（可以先看下后面的用法介绍）

比如在一个 Activity 里有多个fragment，这fragment 之间需要做某些交互。我之前的做法是接口回调，需要统一在 Activity 里管理，并且不可避免的 fragment 之间还得互相持有对方的引用。仔细想想就知道这是很翔的一件事，耦合度高不说，还需要大量的容错判断（比如对方的 fragment 是否还活着）。

仔细体会下这样的好处会发现：
1、Activity 不需要做任何事，甚至不知道这次交互，完美解耦。
2、Fragment 只需要 与ViewModel交互，不需要知道对方 Fragment 的状态甚至是否存在，更不需要持有其引用。所有当对方 Fragment 销毁时，不影响本身任何工作。
3、Fragment 生命周期互不影响，甚至 fragment 替换成其他的 也不影响这个系统的运作。



https://zhuanlan.zhihu.com/p/110772274

- Activity(Fragment) 的 ViewModel 都存储在 ViewModelStore 中，每个 Activity(Fragment) 都会拥有一个 ViewModelStore 实例
- ViewModelProvider 负责向使用者提供访问某个 ViewModel 的接口，其内部会持有当前 Activity(Fragment) 的 ViewModelStore，然后将操作委托给 ViewModelStore 完成
- ViewModel 能在 Activity(Fragment) 在由于配置重建时恢复数据的实现原理是：Activity(指 support library 中的 ComponentActivity) 会将 ViewModelStore 在 Activity(Fragment) 重建之前交给 ActivityThread 中的 ActivityClientRecord 持有，待 Activity(Fragment) 重建完成之后，再从 ActivityClientRecord 中获取 ViewModelStore
- 如果应用的进程位于后台时，由于系统内存不足被销毁了。即使利用 ViewModel 的也不能在 Activity(Fragment) 重建时恢复数据。因为存储 ViewModel 的 ViewModelStore 是交给 ActivityThread 中的 ActivityClientRecord 暂存的，进程被回收了，ActivityThread 也就会被回收，ViewModelStore 也就被回收了，ViewModel 自然不复存在了

2020 10.19 22.18
