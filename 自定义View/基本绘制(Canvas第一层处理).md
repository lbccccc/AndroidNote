# 绘制（一）

### 绘制的基本要素：

- 重写 `onDraw()`

- 使用 Canvas 来绘制

- 使用 Paint 来配置

- 坐标系的y轴是上负下正

- 尺寸单位是像素，而不是 dp。转换方式：

  ```
  public static float dp2px(float dp) {
  ```

  ```
          return  TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, dp,
  ```

  ```
                  Resources.getSystem().getDisplayMetrics());
  ```

  ```
      }
  ```

### Path 的方向以及封闭图形的内外判断：

从圆内的一点作射线

- Winding： 如果方向相反的穿插次数相等则为内部，不等则为外部：

  

- Even Odd：不考虑方向。穿插奇数次则为内部，偶数次则为外部：

如果判定为外部则不画

### PathMeasure

把 Path 对象填入，用于对 Path 做针对性的计算（例如图形周长）。

### 图形一：仪表盘

- 用 drawArc() 绘制弧形
- 三角函数的计算 横向的位移是 cos，纵向的位移是 sin
- PathDashPathEffect
  - 加上 PathEffect 之后，就只绘制 effect，而不绘制原图形。所以需要弧线和刻度分别绘制，一共两次。
  - dash 的方向
  - advance 计算

### 图形二：饼图

- 用 drawArc() 绘制扇形
- 用 Canvas.translate() 来移动扇形，并用 Canvas.save() 和 Canvas.restore() 来保存和恢复位置
- 用三角函数 cos 和 sin 来计算偏移

### 图形三：圆形图像

- Xfermode：
  - 为什么要 Xfermode？为了把多次绘制进行「合成」，例如蒙版效果：用 A 的形状和 B 的图案
  - 怎么做？
    - Canvs.saveLayer() 把绘制区域拉到单独的离屏缓冲里
    - 绘制 A 图形
    - 用 Paint.setXfermode() 设置 Xfermode
    - 绘制 B 图形
    - 用 Paint.setXfermode(null) 恢复 Xfermode
    - 用 Canvas.re

小知识：

1. 圆心坐标和半径，这些都是圆的基本信息，也是它的独有信息。什么叫独有信息？ 就是只有它有，别人没有的信息。比如画圆有圆心坐标和半径，画方有吗？画椭圆有 吗？这就叫独有信息。独有信息都是直接作为参数写进 drawXXX() 方法里的（比 如 drawCircle(centerX, centerY, radius, paint) 的前三个参数）。 而除此之外，其他的都是公有信息。比如图形的颜色、空心实心这些，你不管是画 圆还是画方都有可能用到的，这些信息则是统一放在 paint 参数里的。
2. 在 Android 里，每个 View 都有一个自己的坐 标系，彼此之间是不影响的。这个坐标系的原点是 View 左上角的那个点；水平方 向是 x 轴，右正左负；竖直方向是 y 轴，下正上负（注意，是下正上负，不是上正 下负，和上学时候学的坐标系方向不一样）。
3. 抗锯齿既然这么有用，为什么不默认开启，或者干脆把这 个开关取消，自动让所有绘制都开启抗锯齿？ 短答案：因为抗锯齿并不一定适合所有场景。 长答案：所谓的毛边或者锯齿，发生的原因并不是很多人所想象的「绘制太粗 糙」「像素计算能力不足」；同样，抗锯齿的原理也并不是选择了更精细的算 法来算出了更平滑的图形边缘。 实质上，锯齿现象的发生，只是由于图形分辨率过低，导致人眼察觉出了画面 中的像素颗粒而已。换句话说，就算不开启抗锯齿，图形的边缘也已经是最完 美的了，而并不是一个粗略计算的粗糙版本。 那么，为什么抗锯齿开启之后的图形边缘会更加平滑呢？因为抗锯齿的原理 是：修改图形边缘处的像素颜色，从而让图形在肉眼看来具有更加平滑的感 觉。
4. 由于直线不是封闭图形，所以 setStyle(style) 对直线没有影响。







###### 图形简单时，使用 drawCircle() drawRect() 等方法来直接绘制；图形复杂时，使用 drawPath() 来绘制自定义图 形。

# Path 画自定义图 形

前面的这些方法，都是绘制某个给定的图形，而 drawPath() 可以绘制自定义图 形。当你要绘制的图形比较特殊，使用前面的那些方法做不到的时候，就可以使用 drawPath() 来绘制。drawPath(path) 这个方法是通过描述路径的方式来绘制图形的，

Path 有两类方法，一类是直接描述路径的，另一类是辅助的设置或计算。

## Path 方法第一类：直接描述路径。

- #### 第一组： addXxx() ——添加子图形

```addCircle(oat x, oat y, oat radius, Direction dir) ```添加圆

在用 addCircle() 为 Path 中新增一个圆之后，调用 ```canvas.drawPath(path, paint)``` ，就能画一个圆出来。

可以看出，```path.AddCircle(x, y, radius, dir) + canvas.drawPath(path, paint)``` 这种写法，和直接使用``` canvas.drawCircle(x, y, radius, paint)``` 的效果是一样的，区别只是它的写 法更复杂。所以如果只画一个圆，没必要用 Path ，直接用 drawCircle() 就行 了。drawPath() 一般是在绘制组合图形时才会用到的。 其他的 Path.add-() 方法和这类似，

- #### 第二组： xxxTo() ——画线（直线或曲线）

这一组和第一组 addXxx() 方法的区别在于，第一组是添加的完整封闭图形（除了 addPath() ），而这一组添加的只是一条线。

- ##### ```lineTo(oat x, oat y) / rLineTo(oat x, oat y)```

 画直线 从当前位置向目标位置画一条直线， x 和 y 是目标位置的坐标。这两个方法的区 别是， ```lineTo(x, y)``` 的参数是绝对坐标，而``` rLineTo(x, y)``` 的参数是相对当前 位置的相对坐标 （前缀 r 指的就是 relatively 「相对地」)。

> 当前位置：即最后一次调用画 Path 的方法的终点位置。初始 值为原点 (0, 0)。

```
paint.setStyle(Style.STROKE);
path.lineTo(100, 100); // 由当前位置 (0, 0) 向 (100, 100) 画一条直线
path.rLineTo(100, 0); // 由当前位置 (100, 100) 向正右方 100 像素的位置画
```

- ##### ```quadTo(oat x1, oat y1, oat x2, oat y2) / rQuadTo(oat dx1, oat dy1, oat dx2, oat dy2)``` 画二次贝塞尔曲线

这条二次贝塞尔曲线的起点就是当前位置，而参数中的 x1 , y1 和 x2 , y2 则分别 是控制点和终点的坐标。和 rLineTo(x, y) 同 理，rQuadTo(dx1, dy1, dx2, dy2) 的参数也是相对坐标

> 贝塞尔曲线：贝塞尔曲线是几何上的一种曲线。它通过起点、控制点和终点来 描述一条曲线，主要用于计算机图形学。它可以绘制很多圆润又好看的图形一般情况下，贝塞尔曲线并没有什么用处，只在少数场景 下绘制一些特殊图形的时候才会用到.

- ##### ```cubicTo(float x1, float y1,float x2, float y2, float x3, float y3) / rCubicTo(float x1, float y1, float x2, float y2, float x3, float y3) ```画三次贝塞 尔曲线

 和上面这个 quadTo() rQuadTo() 的二次贝塞尔曲线同理，cubicTo() 和 rCubicTo() 是三次贝塞尔曲线，

- ##### ```moveTo(float x, float y) / rMoveTo(float x, float y)``` 移动到目标位置

不论是直线还是贝塞尔曲线，都是以当前位置作为起点，而不能指定起点。但你可 以通过 moveTo(x, y) 或 rMoveTo() 来改变当前位置，从而间接地设置这些方法 的起点。

- ##### ```arcTo(RectF oval, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(RectF oval, float startAngle, float sweepAngle)``` 画弧形

这个方法和 Canvas.drawArc() 比起来，少了一个参数 useCenter ，而多了一个 参数 forceMoveTo 。 少了 useCenter ，是因为 arcTo() 只用来画弧形而不画扇形，所以不再需要 useCenter 参数；而多出来的这个 forceMoveTo 参数的意思是，绘制是要「抬一 下笔移动过去」，还是「直接拖着笔过去」，区别在于是否留下移动的痕迹。



- ##### ```addArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle) / addArc(RectF oval, float startAngle, float sweepAngle)```

又是一个弧形的方法。一个叫 arcTo ，一个叫 addArc() ，都是弧形，区别在哪 里？其实很简单： addArc() 只是一个直接使用了 forceMoveTo = true 的简化版 arcTo() 。



- ##### close() 封闭当前子图形

它的作用是把当前的子图形封闭，即由当前位置向当前子图形的起点绘制一条直 线。

注意：close() 和 lineTo(起点坐标) 是完全等价的。



- ## Path 方法第二类：辅助的设置或计算



这类方法的使用场景比较少

#### ```Path.setFillType(Path.FillType ft)``` 设置填充方式

Path.setFillType(fillType) 是用来设置图形 自相交时的填充算法的：

方法中填入不同的 FillType 值，就会有不同的填充效果。

FillType 的取值有四 个：

-  EVEN_ODD
-  WINDING （默认值）
-  INVERSE_EVEN_ODD
-  INVERSE_WINDING

后面的两个带有 INVERSE_ 的，是前两个的反色版本,所以只要把前两个，即 EVEN_ODD 和 WINDING ，搞明白就可以了。

- ### EVEN_ODD

 即 even-odd rule （奇偶原则）：对于平面中的任意一点，向任意方向射出一条射 线，这条射线和图形相交的次数（相交才算，相切不算哦）如果是奇数，则这个点 被认为在图形内部，是要被涂色的区域；如果是偶数，则这个点被认为在图形外 部，是不被涂色的区域。所以两个圆相交的部位不会被涂色



- ### WINDING 

即 non-zero winding rule （非零环绕数原则）：首先，它需要你图形中的所有线条 都是有绘制方向的： 然后，同样是从平面中的点向任意方向射出一条射线，但计算规则不一样：以 0 为 初始值，对于射线和图形的所有交点，遇到每个顺时针的交点（图形从射线的左边 向右穿过）把结果加 1，遇到每个逆时针的交点（图形从射线的右边向左穿过）把 结果减 1，最终把所有的交点都算上，得到的结果如果不是 0，则认为这个点在图形 内部，是要被涂色的区域；如果是 0，则认为这个点在图形外部，是不被涂色的区 域。

#### 图形的方向：对于添加子图形类方法（如 Path.addCircle() Path.addRect() ）的方向，由方法的 dir 参数来控制；而对于画线类的方法（如 Path.lineTo() Path.arcTo() ）就更简单了， 线的方向就是图形的方向。





- ## ```drawBitmap(Bitmap bitmap, float left, float top, Paint paint)``` 画 Bitmap

绘制 Bitmap 对象，也就是把这个 Bitmap 中的像素内容贴过来。其中 left 和 top 是要把 bitmap 绘制到的位置坐标。

> drawBitmap 还有一个兄弟方法 drawBitmapMesh() ，可以绘制具有网格拉伸 效果的 Bitmap。 drawBitmapMesh() 的使用场景较少



- ## ```drawText(String text, float x, float y, Paint paint)``` 绘制文字

 界面里所有的显示内容，都是绘制出来的，包括文字。 drawText() 这个方法就是 用来绘制文字的。参数 text 是用来绘制的字符串，x 和 y 是绘制的起点坐标。

```
canvas.drawText(text, 200, 100, paint); 
```

通过 ```Paint.setTextSize(textSize)``` ，可以设置文字的大小。

#### 如果想调整图片的大小，可以自定义设置以下函数

```
fun imageScale(bitmap: Bitmap,newWidth :Float,newHeight :Float):Bitmap{
         val Width = bitmap.width
         val Height = bitmap.height
         scaleX =newWidth/Width
         scaleY =newHeight/Height

         val matrix = Matrix().apply {
             postScale(scaleX,scaleY)
         }
         val newBitmap = Bitmap.createBitmap(bitmap,0,0,Width,Height,matrix,true)
         return newBitmap
    }
```





作业：完成了扇形图的绘制并将一部分移出来

2020.7.4 0:50
