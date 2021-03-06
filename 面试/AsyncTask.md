https://blog.csdn.net/lingguiqin/article/details/79184356

#### 一、什么是AsyncTask？

1、是一种轻量级的异步任务类;
2、是一个封装了线程池和Handler的异步框架；
3、使用它可以更加方便的执行后台任务以及在主线程访问UI，但他不适合进行特别耗时的后台任务；

#### 二、AsyncTask的使用方法

##### 1、3个参数

- Params：执行AsyncTask时，后台任务需要传入的参数，在doInBackground中取出使用。
- Progress：后台任务执行时，如果需要显示当前进度，则制定进度的数据类型。
- Result：后台任务执行完成后的返回值类型。

##### 2、5个方法

- onPreExecute：
  在主线程中执行，在后台任务执行前调用，通常用于做一些准备操作。
- doInBackground(Params… params)：
  在线程池中执行，用于执行后台任务；params 参数是 execute(Params… params)方法中传递的参数。在此方法中可以调用 publishProgress 方法来更新任务的进度，publishProgress方法会调用onProgressUpdate方法。
- publishProgress(Progress… values)：
  用于更新任务的进度，需要手动调用，publishProgress方法会调用onProgressUpdate方法；values参数为设置的进度值。
- onProgressUpdate(Progress… values)：
  在主线程中执行，当后台任务的执行进度发生改变时，会被调用，values参数为进度值。
- onPostExecute(Result result):
  在主线程中执行，当后台任务执行完成时，会被调用。result 的值是doInBackground的返回值。

#### 三、AsyncTask的机制原理

- AsyncTask 的本质是一个静态的线程池，可以执行不同的异步任务，这些任务提交到静态的线程池中执行。
- 线程池中的工作线程执行doInBackgroup(Params… params)方法执行异步任务。
- 当任务状态改变后，工作线程向UI线程发送消息，AsyncTask内部的InternalHandler 响应这些消息，并调用相关的回调函数。

##### AsyncTask的内部封装了两个线程池(SerialExecutor和THREAD_POOL_EXECUTOR)和一个Handler(InternalHandler)。其中SerialExecutor线程池用于任务的排队，让需要执行的多个耗时任务，按顺序排列，THREAD_POOL_EXECUTOR线程池才真正地执行任务，InternalHandler用于从工作线程切换到主线程。

#### 四、AsyncTask的注意事项

##### 1、内存泄漏

- 原因：非静态内部类持有外部类的匿名引用，导致Activity无法释放。
- 解决：
  - AsyncTask内部持有外部Activity的弱引用。
  - AsyncTask改为静态内部类。
  - AsyncTask.cancel()。

##### 2、生命周期

不受Activity生命周期的影响，在Activity销毁之前，取消AsyncTask的运行，以此来保证程序的稳定。

##### 3、结果丢失

由于屏幕旋转、Activity在内存紧张时被回收等情况下，Activity会被重新创建，此时，还在运行的AsyncTask会持有一个Activity的非法引用即之前的Activity实例。导致onPostExecute()没有任何作用。

##### 4、并行or串行

- Android 1.6之前，默认采用串行执行任务；
- Android 1.6~ 2.3 ,默认采用并行执行任务；
- Android 3.0，默认采用串行执行任务，如果需要改为并行，可以调用AsyncTask的executeOnExecutor()来执行任务即可。

#### 5、AsyncTask的使用限制https://www.jianshu.com/p/9b0afe6e40f9

- 必须在主线程中加载，不然在API 16以下不可用，但目前来说，大部分app最低版本也到16了，这个缺点可以忽略了

  内存泄露
   在`Activity`中使用非静态匿名内部`AsyncTask`类，会持有外部类的隐式引用。由于`AsyncTask`的生命周期可能比Activity的长，当Activity进行销毁`AsyncTask`还在执行时，由于`AsyncTask`持有Activity的引用，导致`Activity`对象无法回收，进而产生内存泄露。改为静态内部类

  ```
  Activity` 已被销毁，`doInBackground`还没有执行完，执行完后再执行 `onPostResult`, 导致产生异常 ，记得在`Activity`的 `onDestroy` 方法中调 `cancel`方法取消 `AsyncTask
  ```

  AsyncTask不适合特别耗时的任务，原因就是上述两点，1.内存泄漏 2.生命周期没有跟`Activity`同步，建议用线程池

  每个`AsyncTask`实例只能执行一次。

2020 10.7 17.12
