https://blog.csdn.net/junehappylove/article/details/81365212

https://www.cnblogs.com/xuanxufeng/p/6558330.html

## [java对象克隆以及深拷贝和浅拷贝](https://www.cnblogs.com/xuanxufeng/p/6558330.html)

# 1.什么是"克隆"？

在实际编程过程中，我们常常要遇到这种情况：有一个对象A，在某一时刻A中已经包含了一些有效值，此时可能 会需要一个和A完全相同新对象B，并且此后对B任何改动都不会影响到A中的值，也就是说，A与B是两个独立的对象，但B的初始值是由A对象确定的。在 Java语言中，用简单的赋值语句是不能满足这种需求的。要满足这种需求虽然有很多途径，但实现clone（）方法是其中最简单，也是最高效的手段。 
Java的所有类都默认继承java.lang.Object类，在java.lang.Object类中有一个方法clone()。JDK API的说明文档解释这个方法将返回Object对象的一个拷贝。要说明的有两点：一是拷贝对象返回的是一个新对象，而不是一个引用。二是拷贝对象与用 new操作符返回的新对象的区别就是这个拷贝已经包含了一些原来对象的信息，而不是对象的初始信息。 

# 2.怎样应用clone方法

一个很典型的调用clone()代码如下： 

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 class CloneClass implements Cloneable{ 
 2 　public int aInt; 
 3 　public Object clone(){ 
 4 　　CloneClass o = null; 
 5 　　try{ 
 6 　　　o = (CloneClass)super.clone(); 
 7 　　}catch(CloneNotSupportedException e){ 
 8 　　　e.printStackTrace(); 
 9 　　} 
10 　　return o; 
11 　} 
12 ｝
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

有三个值得注意的地方，一是希望能实现clone功能的CloneClass类实现了Cloneable接口，这个接口属于java.lang 包，java.lang包已经被缺省的导入类中，所以不需要写成java.lang.Cloneable。另一个值得请注意的是重载了clone()方 法。最后在clone()方法中调用了super.clone()，这也意味着无论clone类的继承结构是什么样的，super.clone()直接或 间接调用了java.lang.Object类的clone()方法。下面再详细的解释一下这几点。 

应该说第三点是最重要的，仔细 观察一下Object类的clone()一个native方法，native方法的效率一般来说都是远高于java中的非native方法。这也解释了为 什么要用Object中clone()方法而不是先new一个类，然后把原始对象中的信息赋到新对象中，虽然这也实现了clone功能。对于第二点，也要 观察Object类中的clone()还是一个protected属性的方法。这也意味着如果要应用clone()方法，必须继承Object类，在 Java中所有的类是缺省继承Object类的，也就不用关心这点了。然后重写clone()方法。还有一点要考虑的是为了让其它类能调用这个clone 类的clone()方法，重写之后要把clone()方法的属性设置为public。 

那么clone类为什么还要实现 Cloneable接口呢？稍微注意一下，Cloneable接口是不包含任何方法的！其实这个接口仅仅是一个标志，而且这个标志也仅仅是针对 Object类中clone()方法的，如果clone类没有实现Cloneable接口，并调用了Object的clone()方法（也就是调用了 super.Clone()方法），那么Object的clone()方法就会抛出CloneNotSupportedException异常。 

# 3.深拷贝与浅拷贝

浅拷贝是指拷贝对象时仅仅拷贝对象本身（包括对象中的基本变量），而不拷贝对象包含的引用指向的对象。深拷贝不仅拷贝对象本身，而且拷贝对象包含的引用指向的所有对象。举例来说更加清楚：对象A1中包含对B1的引用，B1中包含对C1的引用。浅拷贝A1得到A2，A2 中依然包含对B1的引用，B1中依然包含对C1的引用。深拷贝则是对浅拷贝的递归，深拷贝A1得到A2，A2中包含对B2（B1的copy）的引用，B2 中包含对C2（C1的copy）的引用。

若不对clone()方法进行改写，则调用此方法得到的对象即为浅拷贝

2020  10.13 11.59
