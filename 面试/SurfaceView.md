##### 一、概念：

SurfaceView本身是一个View，符合一切View的特性，需要通过Canvas画布绘制。



##### 二、优点：

可以在另一个线程中更新界面

不会阻碍界面的交互

#####  三、View和SurfaceView的区别



基于View的绘图效率不高，主要用于动画变化较少的程序

SurfaceView 绘图效率较高，用于界面更新频繁的程序，如相机预览。

SurfaceView 可以在另一个线程中更新界面。





SurfaceView第一印象它是一个view，因为它继承了View，有两个直接子类GLSurfaceView,VideoView。但根据SDK文档SurfaceView和普通的view又有较大区别。

> 最显著的区别就是普通view和它的宿主窗口共享一个绘图表面（Surface），SurfaceView虽然也在View的树形结构中，但是它有属于自己的绘图表面，Surface 内部持有一个Canvas，可以利用这个Canvas绘制。

SurfaceView提供一个直接的绘图表面（Surface）嵌入到视图结构层次中。你可以控制这个Surface的格式，大小，SurfaceView负责在屏幕上正确的摆放Surface。简单说就是SurfaceView拥有自己的Surface，它与宿主窗口是分离的。
我们知道窗口中的view共享一个window，window又对应一个Surface，所以窗口中的view共享一个Surface，而SurfaceView拥有自己的Surface。SurfaceView会创建一个置于应用窗口之后的新窗口，SurfaceView相当于在Window上挖一个洞，它就是显示在这个洞里，其他的View是显示在Window上，所以View可以显示在 SurfaceView之上，也可以添加一些层在SurfaceView之上。

SurfaceView的窗口刷新的时候不需要重绘应用程序的窗口而android普通窗口的视图绘制机制是一层一层的，任何一个子元素或者是局部的刷新都会导致整个视图结构全部重绘一次。

**View和SurfaceView的区别：**
View适用主动更新，SurfaceView 适用被动更新，如频繁的刷新
View在UI线程更新，在非UI线程更新会报错，当在主线程更新view时如果耗时过长也会出错, SurfaceView在子线程刷新不会阻塞主线程，适用于界面频繁更新、对帧率要求较高的情况。
SurfaceView可以控制刷新频率。
SurfaceView底层利用双缓存机制，绘图时不会出现闪烁问题。

双缓冲技术是游戏开发中的一个重要的技术，主要是为了解决 反复局部刷屏带来的闪烁。游戏，视频等画面变化较频繁，前面还没有显示完，程序又请求重新绘制，这样屏幕就会不停地闪烁。双缓冲技术会把要处理的图片在内存中处理好之后，把要画的东西先画到一个内存区域里，然后整体的一次性画出来，将其显示在屏幕上。





2020 10.6 22.42
