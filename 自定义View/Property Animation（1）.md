# 属性动画 Property Animation

> Android 里动画是有一些分类 的：动画可以分为两类：Animation 和 Transition；其中 Animation 又可以再分为 View Animation 和 Property Animation 两类： View Animation 是纯粹基于 framework 的绘制转变，比较简单； Property Animation，属性动画，现在的项目中的动画 99% 都 是用的它，极少再用到 View Animation 了。属性动画不仅可以使用自带的 API 来实 现最常用的动画，而且通过自定义 View 的方式来做出定制化的动画。除了这两种 Animation，还有一类动画是 Transition。 Transition 这个词的本意是转换，在 Android 里指的是切换界面时的动画效果，这个在逻辑上要复杂一点，不过它的重 点是在于切换而不是动画，

## ViewPropertyAnimator

使用方式：View.animate() 后跟 translationX() 等方法，动画会自动执行。

```
view.animate().translationX(500); 
```

![](http://wx4.sinaimg.cn/large/006tKfTcgy1fj7xlvu1rgg30le0con5e.gif)

方法以及方法所对应的 `View` 中的实际操作的方法如下图所示

![](http://wx1.sinaimg.cn/large/006tKfTcgy1fj7x3rm1xxj30u50laq6y.jpg)

从图中可以看到， `View` 的每个方法都对应了 `ViewPropertyAnimator` 的两个方法，其中一个是带有 `-By` 后缀的，例如，`View.setTranslationX()` 对应了 `ViewPropertyAnimator.translationX()` 和 `ViewPropertyAnimator.translationXBy()` 这两个方法。其中带有 `-By()` 后缀的是增量版本的方法，例如，`translationX(100)` 表示用动画把 `View` 的 `translationX` 值渐变为 `100`，而 `translationXBy(100)` 则表示用动画把 `View` 的 `translationX` 值渐变地增加 `100`。

# ObjectAnimator

使用方式：

1. 如果是自定义控件，需要添加 `setter` / `getter` 方法；
2. 用 `ObjectAnimator.ofXXX()` 创建 `ObjectAnimator` 对象；
3. 用 `start()` 方法执行动画。

```
public class SportsView extends View {
     float progress = 0;
    
    ......
    
    // 创建 getter 方法
    public float getProgress() {
        return progress;
    }

    // 创建 setter 方法
    public void setProgress(float progress) {
        this.progress = progress;
        invalidate();
    }
    
    @Override
    public void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        
        ......
        
        canvas.drawArc(arcRectF, 135, progress * 2.7f, false, paint);
        
        ......
    }
}

......

// 创建 ObjectAnimator 对象
ObjectAnimator animator = ObjectAnimator.ofFloat(view, "progress", 0, 65);
// 执行动画
animator.start();
```

![](http://wx3.sinaimg.cn/large/006tKfTcgy1fj7y2vnw5jg30ek0dijwq.gif)

## **通用功能**

### 1. setDuration(int duration) 设置动画时长

单位是毫秒

```
// imageView1: 500 毫秒
imageView1.animate()
        .translationX(500)
        .setDuration(500);

// imageView2: 2 秒
ObjectAnimator animator = ObjectAnimator.ofFloat(
        imageView2, "translationX", 500);
animator.setDuration(2000);
animator.start();
```

![](http://wx3.sinaimg.cn/large/006tKfTcgy1fj7yc68dgrg30le0miken.gif)

### 2. setInterpolator(Interpolator interpolator) 设置 Interpolator

`Interpolator` 其实就是速度设置器。你在参数里填入不同的 `Interpolator` ，动画就会以不同的速度模型来执行。

```
// imageView1: 线性 Interpolator，匀速
imageView1.animate()
        .translationX(500)
        .setInterpolator(new LinearInterpolator());

// imageView: 带施法前摇和回弹的 Interpolator
ObjectAnimator animator = ObjectAnimator.ofFloat(
        imageView2, "translationX", 500);
animator.setInterpolator(new AnticipateOvershootInterpolator());
animator.start();
```

![img](http://wx1.sinaimg.cn/large/006tKfTcly1fj8ffqb46bg30lg0buduv.gif)

![img](http://wx2.sinaimg.cn/large/006tKfTcly1fj8fg48mu1g30lg0bunfi.gif)

##### 每一个 `Interpolator`

- #### AccelerateDecelerateInterpolator

先加速再减速。这是默认的 `Interpolator`，也就是说如果不设置的话，那么动画将会使用这个 `Interpolator`。

这个是一种最符合现实中物体运动的 `Interpolator`，它的动画效果**看起来就像是物体从速度为 0 开始逐渐加速，然后再逐渐减速直到 0 的运动**。

![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8haplmxvg30lg0bu4eu.gif)

#### LinearInterpolator

匀速

![img](http://wx1.sinaimg.cn/large/006tKfTcly1fj8ffqb46bg30lg0buduv.gif)

#### AccelerateInterpolator

持续加速

在整个动画过程中，一直在加速，直到动画结束的一瞬间，直接停止。

![img](http://wx3.sinaimg.cn/large/006tKfTcly1fj8hj03zvog30lg0bu4e0.gif)

#### DecelerateInterpolator

持续减速直到 0

动画开始的时候是最高速度，然后在动画过程中逐渐减速，直到动画结束的时候恰好减速到 0

![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8hs2sgltg30lg0bunbv.gif)

它的效果和上面这个 `AccelerateInterpolator` 相反，适用场景也和它相反：它主要用于入场效果，比如某个物体从界面的外部飞入界面后停在某处。

#### AnticipateInterpolator

先回拉一下再进行正常动画轨迹。效果看起来有点像投掷物体或跳跃等动作前的蓄力。

![img](http://wx1.sinaimg.cn/large/006tKfTcly1fj8hwknr1rg30lg0buaqt.gif)

如果是图中这样的平移动画，那么就是位置上的回拉；如果是放大动画，那么就是先缩小一下再放大；其他类型的动画同理。

#### OvershootInterpolator

动画会超过目标值一些，然后再弹回来。效果看起来有点像坐在沙发上后又被弹起来一点的感觉。

![img](http://wx2.sinaimg.cn/large/006tKfTcly1fj8i6bnopfg30lg0buwte.gif)

#### AnticipateOvershootInterpolator

上面这两个的结合版：开始前回拉，最后超过一些然后回弹。

![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8i97q2e1g30lg0bunfi.gif)

#### BounceInterpolator

在目标值处弹跳。有点像玻璃球掉在地板上的效果。

![img](http://wx1.sinaimg.cn/large/006tKfTcly1fj8ia6zyl1g30lg0butmb.gif)

#### CycleInterpolator

它和 `AccelerateDecelerateInterpolator` 的区别是，它可以自定义曲线的周期，所以动画可以不到终点就结束，也可以到达终点后回弹，回弹的次数由曲线的周期决定，曲线的周期由 `CycleInterpolator()` 构造方法的参数决定。

> 参数为 0.5f：

![img](http://wx3.sinaimg.cn/large/006tKfTcly1fj8in23hktg30lg0bu197.gif)

> 参数为 2f：

![img](http://wx3.sinaimg.cn/large/006tKfTcly1fj8iuqytg8g30lg0buqoh.gif)

#### PathInterpolator

自定义动画完成度 / 时间完成度曲线。

用这个 `Interpolator` 可以定制出任何你想要的速度模型。定制的方式是使用一个 `Path` 对象来绘制出你要的动画完成度 / 时间完成度曲线。例如：

```java
Path interpolatorPath = new Path();

...

// 匀速
interpolatorPath.lineTo(1, 1);
```

![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8jc60j9tj30cn09hq32.jpg)

![img](http://wx1.sinaimg.cn/large/006tKfTcly1fj8ffqb46bg30lg0buduv.gif)

```java
Path interpolatorPath = new Path();

...

// 先以「动画完成度 : 时间完成度 = 1 : 1」的速度匀速运行 25%
interpolatorPath.lineTo(0.25f, 0.25f);
// 然后瞬间跳跃到 150% 的动画完成度
interpolatorPath.moveTo(0.25f, 1.5f);
// 再匀速倒车，返回到目标点
interpolatorPath.lineTo(1, 1);
```

Java

![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8jmom7kaj30cd0ay74f.jpg)

![img](http://wx4.sinaimg.cn/large/006tKfTcly1fj8jsmxr3eg30lg0buto5.gif)

根据需求，绘制出自己需要的 `Path`，就能定制出想要的速度模型

注意：同一段时间完成度上不能有两段不同的动画完成度，而且每一个时间完成度的点上都必须要有对应的动画完成度



![img](http://wx3.sinaimg.cn/large/006tKfTcly1fj8lk0do93j30c109baa6.jpg)

#### FastOutLinearInInterpolator

加速运动

这个 `Interpolator` 的作用和 `AccelerateInterpolator` 一样，都是一个持续加速的运动路线。只不过 `FastOutLinearInInterpolator` 的曲线公式是用的贝塞尔曲线，而 `AccelerateInterpolator` 用的是指数曲线。具体来说，它俩最主要的区别是 `FastOutLinearInInterpolator` 的初始阶段加速度比 `AccelerateInterpolator` 要快一些。

![img](http://wx3.sinaimg.cn/large/006tKfTcgy1fj8nao9l37j30c709e3yo.jpg)

两条线几乎是一致的，只是红线比绿线更早地到达了较高的斜率，这说明在初始阶段，`FastOutLinearInInterpolator` 的加速度比 `AccelerateInterpolator` 更高。

实际上，这点区别，在实际应用中用户根本察觉不出来。

#### FastOutSlowInInterpolator

先加速再减速。

同样也是先加速再减速的还有前面的 `AccelerateDecelerateInterpolator`，不过它们的效果是明显不一样的。`FastOutSlowInInterpolator` 用的是贝塞尔曲线，`AccelerateDecelerateInterpolator` 用的是正弦 / 余弦曲线。具体来讲， `FastOutSlowInInterpolator` 的前期加速度要**快得多**。

`FastOutSlowInInterpolator`：

![img](http://wx4.sinaimg.cn/large/006tKfTcgy1fj8ojcnakkj30ca094q32.jpg)

![img](http://wx1.sinaimg.cn/large/006tKfTcgy1fj8ojvxe6dg30lg0bu17c.gif)

`AccelerateDecelerateInterpolator`：

![img](http://wx1.sinaimg.cn/large/006tKfTcgy1fj8okq3druj30c7092jrj.jpg)

![img](http://wx4.sinaimg.cn/large/006tKfTcgy1fj8ol1bvc3g30lg0bu4eu.gif)

不论是从动图还是从曲线都可以看出，这二者比起来，`FastOutSlowInInterpolator` 的前期加速更猛一些，后期的减速过程的也减得更迅速。用更直观一点的表达就是，`AccelerateDecelerateInterpolator` 像是物体的自我移动，而 `FastOutSlowInInterpolator` 则看起来像有一股强大的外力「推」着它加速，在接近目标值之后又「拽」着它减速。总之，`FastOutSlowInterpolator` 看起来有一点「着急」的感觉。

二者曲线对比图：

![img](http://wx3.sinaimg.cn/large/006tKfTcgy1fj8pcob93jj30bc08fmxb.jpg)

#### LinearOutSlowInInterpolator

持续减速。

它和 `DecelerateInterpolator` 比起来，同为减速曲线，主要区别在于 `LinearOutSlowInInterpolator` 的初始速度更高。对于人眼的实际感觉，区别其实也不大，不过还是能看出来一些的。

`LinearOutSlowInInterpolator`：

![img](http://wx2.sinaimg.cn/large/006tKfTcgy1fj8po2x425j30cf098mxa.jpg)

![img](http://wx4.sinaimg.cn/large/006tKfTcgy1fj8plj835gg30lg0bu4dl.gif)

`DecelerateInterpolator`：

![img](http://wx3.sinaimg.cn/large/006tKfTcgy1fj8pntgmfuj30ca09874f.jpg)

![img](http://wx1.sinaimg.cn/large/006tKfTcgy1fj8pof5h5dg30lg0bunbv.gif)

二者曲线对比：

![img](http://wx2.sinaimg.cn/large/006tKfTcgy1fj8ppbuqoaj30cg093jrk.jpg)

对于所有 `Interpolator` 的介绍就到这里。这些 `Interpolator`，有的较为常用且有通用的使用场景，有的需要根据情况而定。把它们了解清楚了，对于制作出观感舒服的动画很有好处。

### 3. 设置监听器

给动画设置监听器，可以在关键时刻得到反馈，从而及时做出合适的操作，例如在动画的属性更新时同步更新其他数据，或者在动画结束后回收资源等。

设置监听器的方法， `ViewPropertyAnimator` 和 `ObjectAnimator` 略微不一样： `ViewPropertyAnimator` 用的是 `setListener()` 和 `setUpdateListener()` 方法，可以设置一个监听器，要移除监听器时通过 `set[Update]Listener(null)` 填 null 值来移除；而 `ObjectAnimator` 则是用 `addListener()` 和 `addUpdateListener()` 来添加一个或多个监听器，移除监听器则是通过 `remove[Update]Listener()` 来指定移除对象。

另外，由于 `ObjectAnimator` 支持使用 `pause()` 方法暂停，所以它还多了一个 `addPauseListener()` / `removePauseListener()` 的支持；而 `ViewPropertyAnimator` 则独有 `withStartAction()` 和 `withEndAction()` 方法，可以设置一次性的动画开始或结束的监听。

#### 3.1 ViewPropertyAnimator.setListener() / ObjectAnimator.addListener()

这两个方法的名称不一样，可以设置的监听器数量也不一样，但它们的参数类型都是 `AnimatorListener`，所以本质上其实都是一样的。 `AnimatorListener` 共有 4 个回调方法：

##### 3.1.1 onAnimationStart(Animator animation)

当动画开始执行时，这个方法被调用。

##### 3.1.2 onAnimationEnd(Animator animation)

当动画结束时，这个方法被调用。

##### 3.1.3 onAnimationCancel(Animator animation)

当动画被通过 `cancel()` 方法取消时，这个方法被调用。

需要说明一下的是，就算动画被取消，`onAnimationEnd()` 也会被调用。所以当动画被取消时，如果设置了 `AnimatorListener`，那么 `onAnimationCancel()` 和 `onAnimationEnd()` 都会被调用。`onAnimationCancel()` 会先于 `onAnimationEnd()` 被调用。

##### 3.1.4 onAnimationRepeat(Animator animation)

当动画通过 `setRepeatMode()` / `setRepeatCount()` 或 `repeat()` 方法重复执行时，这个方法被调用。

由于 `ViewPropertyAnimator` 不支持重复，所以这个方法对 `ViewPropertyAnimator` 相当于无效。

#### 3.2 ViewPropertyAnimator.setUpdateListener() / ObjectAnimator.addUpdateListener()

和上面 3.1 的两个方法一样，这两个方法虽然名称和可设置的监听器数量不一样，但本质其实都一样的，它们的参数都是 `AnimatorUpdateListener`。它只有一个回调方法：`onAnimationUpdate(ValueAnimator animation)`。

##### 3.2.1 onAnimationUpdate(ValueAnimator animation)

当动画的属性更新时（不严谨的说，即每过 10 毫秒，动画的完成度更新时），这个方法被调用。

方法的参数是一个 `ValueAnimator`，`ValueAnimator` 是 `ObjectAnimator` 的父类，也是 `ViewPropertyAnimator` 的内部实现，所以这个参数其实就是 `ViewPropertyAnimator` 内部的那个 `ValueAnimator`，或者对于 `ObjectAnimator` 来说就是它自己本身。

`ValueAnimator` 有很多方法可以用，它可以查看当前的动画完成度、当前的属性值等等。

#### 3.3 ViewPropertyAnimator.withStartAction/EndAction()

这两个方法是 `ViewPropertyAnimator` 的独有方法。它们和 `set/addListener()` 中回调的 `onAnimationStart()` / `onAnimationEnd()` 相比起来的不同主要有两点：

1. `withStartAction()` / `withEndAction()` 是一次性的，在动画执行结束后就自动弃掉了，就算之后再重用 `ViewPropertyAnimator` 来做别的动画，用它们设置的回调也不会再被调用。而 `set/addListener()` 所设置的 `AnimatorListener` 是持续有效的，当动画重复执行时，回调总会被调用。
2. `withEndAction()` 设置的回调只有在动画正常结束时才会被调用，而在动画被取消时不会被执行。这点和 `AnimatorListener.onAnimationEnd()` 的行为是不一致的。
