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

#### 5、AsyncTask的使用限制

- AsyncTask的对象必须在主线程中创建；
- execute 方法必须在UI线程调用；
- 不要在程序中手动调用 onPreExecute、onPostExecute、 doInBackground、onProgressUpdate方法；
- 一个AsyncTask对象只能调用一次excute()方法，执行一次，否则会报异常。



2020 10.7 17.12
