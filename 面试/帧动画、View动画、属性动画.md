https://blog.csdn.net/qq475703980/article/details/93243968?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.add_param_isCf&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.add_param_isCf

Android的动画分为了三种， 分别是 帧动画、View动画、属性动画。

## 1、帧动画

帧动画就是顺序播放一组预先定义好的图片，就类似于我们观看视频，就是一张一张的图片连续播放。

帧动画的使用很简单，总共就两个步骤：

- 1、在res/drawable目录下定义一个XML文件，根节点为系统提供的animation-list，然后放入定义更好的图片；
- 2、使用AnimationDrawable类播放第一步定义好的Drawable中的图片，形成动画效果；

**代码示例，第一步**，创建Drawable文件：

```
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
	android:oneshot="false">
	<item android:drawable="@drawable/image01" android:duration="500"/>
	<item android:drawable="@drawable/image02" android:duration="500"/>
	<item android:drawable="@drawable/image03" android:duration="500"/>
</animation-list>
1234567
```

上述xml中，有些属性我们要了解到：

- 1、android:oneshot=“false”： 表示是否重复播放动画，还是只播放一次；
- 2、每个item都有Drawable和duration属性，Drawable表示我们要播放的图片；duration表示这张图播放的时间；

**代码示例，第二步**，用AnimationDrawable播放动画：

```
    Button button = (Button) findViewById(R.id.bt_001);
    button.setBackgroundResource(R.drawable.frame_animation);//把Drawable设置为button的背景
    //拿到这个我们定义的Drawable，实际也就是AnimationDrawable
    AnimationDrawable animationDrawable = (AnimationDrawable) button.getBackground();
    animationDrawable.start();//开启动画
12345
```

实际上，从名字也可以看出，AnimationDrawable是一个Drawable的子类，所以我们定义的xml文件也是防在res/rawable目录下的.

## 2、View动画（也称补间动画）

### 2.1 View动画的基本介绍

view动画也称为补间动画，因为我们只需要拿到一个view，设定它开始和结束的位置，中间的view会自动由系统补齐，而不需要帧动画每一幅图都是提前准备好的。

View动画是Android一开始就提供的比较原始的动画，主要支持四种效果：平移、缩放、旋转、透明度变化（渐变） 四种基本效果，我们可以再这四种基础效果的基础上，选择其中的几种进行组合。



2020 10.13 12.15
