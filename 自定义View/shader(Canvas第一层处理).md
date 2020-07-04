## shader(着色器)        图片上传了用不了，有毒

 setShader(Shader shader) 设置 Shader 除了直接设置颜色， Paint 还可以使用 Shader 。

「着色器」不是 Android 独有的，它是图形领域里一个通用的概念，它 和直接设置颜色的区别是，着色器设置的是一个颜色方案，或者说是一套着色规 则。当设置了 Shader 之后，Paint 在绘制图形和文字时就不使用 setColor/ARGB() 设置的颜色了，而是使用 Shader 的方案中的颜色。 在 Android 的绘制里使用 Shader ，并不直接用 Shader 这个类，而是用它的几个 子类。具体来讲有 ``` SweepGradient BitmapShader ComposeShader``` 这么几个：

- #####  LinearGradient 线性渐变

   设置两个点和两种颜色，以这两个点作为端点，使用两种颜色的渐变来绘制颜色。 就像这样：

构造方法：LinearGradient(float x0, float y0, float x1, float y1, int color0, int color1, Shader.TileMode)

![](https://wx3.sinaimg.cn/large/52eb2279ly1fig6dq7wudj206l06875e.jpg)

注意：在设置了 Shader 的情况下， Paint.setColor/ARGB() 所设置的颜色 就不再起作用。

#### 参数：

 x0 y0 x1 y1 ：渐变的两个端点的位置 

color0 color1 是端点的颜色 

tile ：端点范围之外的着色规则，类型是 TileMode 。TileMode 一共有 3 个值可 选： CLAMP , MIRROR 和 REPEAT 。CLAMP 会在端点之外延续端点处的颜色；MIRROR 是镜像模式；REPEAT 是重复模式。 具体的看一下例子就明白。

CLAMP:

![image-20200704145700698](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704145700698.png)

![image-20200704145711474](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704145711474.png)

REPEAT:

![image-20200704145729747](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704145729747.png)

- ### RadialGradient 辐射渐变

辐射渐变很好理解，就是从中心向周围辐射状的渐变。大概是这样：

![image-20200704145850074](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704145850074.png)

##### 构造方法：

RadialGradient(float centerX, float centerY, float radius, int centerColor,edgeColor,titleMode)

###### 参数：

 centerX centerY ：辐射中心的坐标

 radius ：辐射半径

 centerColor ：辐射中心的颜色 

edgeColor ：辐射边缘的颜色

 tileMode ：辐射范围之外的着色模式。

![image-20200704150042539](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704150042539.png)

![image-20200704150111052](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704150111052.png)

![image-20200704150120178](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704150120178.png)

- ## SweepGradient 扫描渐变

![image-20200704150148291](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704150148291.png)

##### 构造方法： SweepGradient(float cx, float cy, int color0, int color1) 

参数： cx cy ：扫描的中心

 color0 ：扫描的起始颜色

 color1 ：扫描的终止颜色

- ##  BitmapShader 

  用 Bitmap 来着色。其实也就是用 Bitmap 的像素来作为图形 或文字的填充。

![image-20200704150454820](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704150454820.png)

这个和 Canvas.drawBitmap() 很像，事实上也是一样的效果。如果想绘制圆形的 Bitmap ，就别用 drawBitmap() 了，改用 drawCircle() + BitmapShader 就可以了（其他形状同理）。

#### 构造方法：

 BitmapShader(Bitmap bitmap, Shader.TileMode tileX, Shader.TileMode tileY )

##### 参数：

 bitmap ：用来做模板的

 Bitmap 对象 

tileX：横向的 TileMode

 tileY：纵向的 TileMode 。

![image-20200704151027881](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704151027881.png)

MIRROR

![image-20200704151048342](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704151048342.png)

![image-20200704151055409](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704151055409.png)

- ## ComposeShader 混合着色器

   所谓混合，就是把两个 Shader 一起使用。

![image-20200704151218786](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704151218786.png)

##### 构造方 法：

ComposeShader(Shader shaderA, Shader shaderB, PorterDuff.Mode mode) 

##### 参数：

-  shaderA , shaderB ：两个相继使用的 Shader
- mode : 两个 Shader 的叠加模式，即 shaderA 和 shaderB 应该怎样共同绘制。它 的类型是 PorterDuff.Mode 。



###### 注意： PorterDuff.Mode 一共有 17 个，可以分为两类： 1. Alpha 合成 (Alpha Compositing) 2. 混合 (Blending)，具体去看文档

![image-20200704151539323](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704151539323.png)

Alpha 合成：

![image-20200704151557486](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704151557486.png)

混合合成：

![image-20200704151622339](C:\Users\zl\AppData\Roaming\Typora\typora-user-images\image-20200704151622339.png)

2020.7.4 15:41
