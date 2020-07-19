# RxJava

## 基本⽤法

```
@GET("users/{username}/repos")
Single<List<Repo>> getRepos(@Path("username") String username);
...
api.getRepos("rengwuxian")
 .subscribeOn(Schedulers.newThread())
 .observeOn(AndroidSchedulers.mainThread())
 .subscribe(new SingleObserver<List<Repo>>() {
 @Override
 public void onSubscribe(Disposable disposable) {
 textView.setText("正在请求");
 MainActivity.this.disposable.add(disposable);
 }
 @Override
 public void onSuccess(List<Repo> repos) {
 textView.setText(repos.get(0).name);
 }
 @Override
 public void onError(Throwable e) {
 textView.setText(e.getMessage());
 }
 });
```

### 框架结构

RxJava 的整体结构是⼀条链，其中： 

1. 链的最上游：⽣产者 Observable 
2. 链的最下游：观察者 Observer 
3. 链的中间：各个中介节点，既是下游的 Observable，⼜是上游的 Observer

### 操作符 Operator（map() 等等）：

1. 基于原 Observable 创建⼀个新的 Observable
2. Observable 内部创建⼀个 Observer
3. 通过定制 Observable 的 subscribeActual() ⽅法和 Observer 的 onXxx() ⽅法，来实现⾃⼰的中 介⻆⾊（例如数据转换、线程切换）

### Disposable：

可以通过 dispose() ⽅法来让上游停⽌⼯作，达到「丢弃」的效果。

# subscribeOn()

### 原理 

在 Scheduler 指定的线程⾥启动 subscribe()

### 效果

- 切换起源 Observable 的线程； 
- 当多次调⽤ subscribeOn() 的时候，只有最上⾯的会对起源 Observable 起作⽤。

## observeOn()

### 原理

在内部创建的 Observer 的 onNext() onError() onSuccess() 等回调⽅法⾥，通过 Scheduler 指定的 线程来调⽤下级 Observer 的对应回调⽅法

### 效果 

- 切换 observeOn() 下⾯的 Observer 的回调所在的线程 
- 当多次调⽤ observeOn() 的时候，每个都会进⾏⼀次线程切换，影响范围是它下⾯的每个 Observer (除⾮⼜遇到新的 observeOn())

# Scheduler 的原理

1. Schedulers.newThread() 和 Schedulers.io()： 
   - 当 scheduleDirect() 被调⽤的时候，会创建⼀个 Worker，Worker 的内部会有⼀个 Executor，由 Executor 来完成实际的线程切换； 
   - scheduleDirect() 还会创建出⼀个 Disposable 对象，交给外层的 Observer，让它能执⾏ dispose() 操作，取消订阅链； 
   - newThread() 和 io() 的区别在于，io() 可能会对 Executor 进⾏重⽤。

2. AndroidSchedulers.mainThread()： 

   通过内部的 Handler 把任务 post 到主线程去做。

2020 7.20 0:36
