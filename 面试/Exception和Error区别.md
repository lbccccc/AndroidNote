https://www.zhihu.com/question/36278363/answer/67013226

首先可以看到 Throwable 分成两个大类，Exception 和 Error

### Error

> Error 是 java 虚拟机抛出的错误，程序运行中出现的较严重的问题。

例如，虚拟机的堆内存不够用了，就会抛出 OutOfMemoryError。这些异常用户的代码无需捕获，因为捕获了也没用。

这就好比船坏了，而船上的乘客即便知道船坏了也没办法，因为这不是他们能解决的问题。

### Exception

> Exception 是应用程序中可能的可预测、可恢复问题。

啥意思呢？也就是说Exception都是用户代码层面抛出的异常。换句话说，这些异常都是船上的乘客自己可以解决的。例如常见的空指针异常NullPointerException，取数组下标越界时会抛出
ArrayIndexOutOfBoundException。

这些都是“乘客”的错误操作引发的问题，所以“乘客”是可以解决的。

到了这里，老源Sir听到的那道面试题，是不是就已经解决了呢？

## CheckedException 和 UncheckedException

老源Sir前面也说了，这是个常见的连环问题。那么解决了第一个问题，面试官接下来会问什么呢？

通过上面一节的叙述大家可以看到，就 Throwable 体系本身，与程序员关系比较大的其实还是 Exception 及其子类。因为船上的乘客都是程序员们创造，所以他们的错误行为，程序员还是要掌握得比较透彻的。

Exception 可分为两种，CheckedException 和 UncheckedException。

- UncheckedException

> 顾名思义，UncheckedException 也就是可以不被检查的异常。JVM 规定继承自 RuntimeException 的异常都是 UncheckedException.

- CheckedException

> 所有非 RuntimeException 的 Exception.

Exception又分为运行时异常（Runtime Exception）和受检查的异常(Checked Exception )。

- RuntimeException：其特点是Java编译器不去检查它，也就是说，当程序中可能出现这类异常时，即使没有用try……catch捕获，也没有用throws抛出，还是会编译通过，如除数为零的ArithmeticException、错误的类型转换、数组越界访问和试图访问空指针等。处理RuntimeException的原则是：如果出现RuntimeException，那么一定是程序员的错误。

- 受检查的异常（IOException等）：这类异常如果没有try……catch也没有throws抛出，编译是通不过的。这类异常一般是外部错误，例如文件找不到、试图从文件尾后读取数据等，这并不是程序本身的错误，而是在应用环境中出现的外部错误。

  

2020 10.29 15.15
