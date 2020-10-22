## 1.什么是Fragment?

　　你可以简单的理解为，Fragment是显示在Activity中的Activity。

　　它可以显示在Activity中，然后它也可以显示出一些内容。

　　因为它拥有自己的生命周期，可以接受处理用户的事件

　　并且你可以在一个Activity中动态的添加，替换，移除不同的Fragment，

　　

## 2.Fragment的生命周期

fragment覆盖另一个fragment的生命周期：https://blog.csdn.net/weixin_42934146/article/details/100888611



https://www.jianshu.com/p/efe5e7bc7bef

　　因为Fragment是依附于Activity存在的，因此它的生命周期受到Activity的生命周期影响。

Fragment比Activity多了几个生命周期的回调方法

- onAttach(Activity) 当Fragment与Activity发生关联的时候调用
- onCreateView(LayoutInflater, ViewGroup, Bundle) 创建该Fragment的视图
- onActivityCreated(Bundle) 当Activity的onCreated方法返回时调用
- onDestroyView() 与onCreateView方法相对应，当该Fragment的视图被移除时调用
- onDetach() 与onAttach方法相对应，当Fragment与Activity取消关联时调用

## 3.Fragment的使用方式

**静态使用Fragment**

步骤：

① 创建一个类继承Fragment，重写onCreateView方法，来确定Fragment要显示的布局

② 在Activity中声明该类，与普通的View对象一样

**动态使用Fragment**

https://blog.csdn.net/Kimhlo_/article/details/77609023

看书

1. 获取FragmentManager
2. 通过FragmentManager再获取FragmentTransaction
3. 构造一个fragment
4. 添加



## 4.什么是Fragment的回退栈？【重要】

　　Fragment的回退栈是用来保存每一次Fragment事务发生的变化 

　　如果你将Fragment任务添加到回退栈，当用户点击后退按钮时，

　　将看到上一次的保存的Fragment。 

　　一旦Fragment完全从后退栈中弹出，用户再次点击后退键，则退出当前Activity。



## 5.Fragment与Activity之间的通信【难点】

Fragment依附于Activity存在，因此与Activity之间的通信可以归纳为以下几点：

 

-  如果你Activity中包含自己管理的Fragment的引用，可以通过引用直接访问所有的Fragment的public方法
-  如果Activity中未保存任何Fragment的引用，那么没关系，每个Fragment都有一个唯一的TAG或者ID,可以通过getFragmentManager.findFragmentByTag()或者findFragmentById()获得任何Fragment实例，然后进行操作
-  Fragment中可以通过getActivity()得到当前绑定的Activity的实例，然后进行操作。

## 7.如何处理运行时配置发生变化【以屏幕翻转为例】

　　在Activity的学习中我们都知道，当屏幕旋转时，是对屏幕上的视图进行了重新绘制。　　

　　因为当屏幕发生旋转，Activity发生重新启动，

　　默认的Activity中的Fragment也会跟着Activity重新创建，

　　用脚趾头都明白...横屏和竖屏显示的不一样肯定是进行了重新绘制视图的操作。

　　所以，不断的旋转就不断绘制，这是一种很耗费内存资源的操作，那么如何来进行优化？

 　因为当屏幕发生旋转，Activity发生重新启动，

　　默认的Activity中的Fragment也会跟着Activity重新创建；　　

　　这样造成当旋转的时候，本身存在的Fragment会重新启动，

　　然后当执行Activity的onCreate时，又会再次实例化一个新的Fragment，这就是出现的原因。

 

　　

　　那么如何解决呢：

　　通过检查onCreate的参数Bundle savedInstanceState就可以判断，

　　当前是否发生Activity的重新创建

　　默认的savedInstanceState会存储一些数据，包括Fragment的实例

　　所以，我们简单改一下代码，判断只有在savedInstanceState==null时，才进行创建Fragment实例
　　现在无论进行多次旋转都只会有一个Fragment实例在Activity中，

　　现在还存在一个问题，就是重新绘制时，Fragment发生重建，原本的数据如何保持？

　　和Activity类似，Fragment也有onSaveInstanceState的方法，在此方法中进行保存数据，

　　然后在onCreate或者onCreateView或者onActivityCreated进行恢复都可以。





# Fragment和Activity之间传递数据

https://blog.csdn.net/LEO_ANDERSON/article/details/83478304

### 第一种方式，也是最常用的方式，就是使用Bundle来传递参数

### 第二种方式，在Fragment中定义一个接口回调方法，通过该回调方法把数据传递给Activity，Activity通过调用该回调方法即可得到需要的数据

2020 10.9  16.05
