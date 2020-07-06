# 属性动画 Property Animation

> Android 里动画是有一些分类 的：动画可以分为两类：Animation 和 Transition；其中 Animation 又可以再分为 View Animation 和 Property Animation 两类： View Animation 是纯粹基于 framework 的绘制转变，比较简单； Property Animation，属性动画，现在的项目中的动画 99% 都 是用的它，极少再用到 View Animation 了。属性动画不仅可以使用自带的 API 来实 现最常用的动画，而且通过自定义 View 的方式来做出定制化的动画。除了这两种 Animation，还有一类动画是 Transition。 Transition 这个词的本意是转换，在 Android 里指的是切换界面时的动画效果，这个在逻辑上要复杂一点，不过它的重 点是在于切换而不是动画，

## ViewPropertyAnimator

使用方式：View.animate() 后跟 translationX() 等方法，动画会自动执行。

```
view.animate().translationX(500); 
```

![](http://wx4.sinaimg.cn/large/006tKfTcgy1fj7xlvu1rgg30le0con5e.gif)
