## BlockCanary 的原理 

- 在调⽤ start() 时，通过调⽤主线程的 Looper.setMessageLogging() ⽅ 法，为 Looper 的 mLogging 成员变量赋值。
-  在 Looper 死循环中， println ⽅法分别会在 dispatchMessage(msg) 之 前和之后被调⽤。
-  所以通过⾃定义 Printer 对象，我们就可以获得 dispatchMessage 的耗时， 从⽽判断出是否有应⽤卡顿。
- 同时 BlockCanary 还会在⼦线程中执⾏⼀个获取主线程堆栈信息的定时任务，这个 任务会在 dispatchMessage 结束的时候被移除。
- BlockCanary 的缺陷 依靠定时获取堆栈的⽅法，定位不够精准。 println ⽅法中会拼接字符串对象

2020 9.24 11.25
