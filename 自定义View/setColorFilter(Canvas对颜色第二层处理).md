# setColorFilter(ColorFilter colorFilter)

 ColorFilter 这个类，它的名字已经足够解释它的作用：为绘制设置颜色过滤。颜 色过滤的意思，就是为绘制的内容设置一个统一的过滤策略，然后 Canvas.drawXXX() 方法会对每个像素都进行过滤后再绘制出来。

在 Paint 里设置 ColorFilter ，使用的是 Paint.setColorFilter(ColorFilter filter) 方法。 ColorFilter 并不直接 使用，而是使用它的子类。它共有三个子类：LightingColorFilter     PorterDuffColorFilter 和 ColorMatrixColorFilter 。





- ####  LightingColorFilter

   这个 LightingColorFilter 是用来模拟简单的光照效果的。 LightingColorFilter 的构造方法是 LightingColorFilter(int mul, int add) ，参数里的 mul 和 add 都是和颜色 值格式相同的 int 值，其中 mul 用来和目标像素相乘，add 用来和目标像素相加：

  ​	```R' = R * mul.R / 0xff + add.R ```

   ```G' = G * mul.G / 0xff + add.G```

   ``` B' = B * mul.B / 0xff + add.B ```

  

可以修改参数来模拟想要的某种具体光照效果

- ### PorterDuffColorFilter

   这个 PorterDuffColorFilter 的作用是使用一个指定的颜色和一种指定的 PorterDuff.Mode 来与绘制对象进行合成。它的构造方法是``` PorterDuffColorFilter(int color, PorterDuff.Mode mode) ```其中的 color 参数是指定的颜色， mode 参数是指定的 Mode 。同样也是 PorterDuff.Mode ，不 过和 ComposeShader 不同的是，PorterDuffColorFilter 作为一个 ColorFilter ，只能指定一种颜色作为源，而不是一个 Bitmap 。

  

- ### ColorMatrixColorFilter

ColorMatrixColorFilter 使用一个 ColorMatrix 来对颜色进行 处理。 ColorMatrix 这个类，内部是一个 4x5 的矩阵：

```
[ a, b, c, d, e,
 f, g, h, i, j,
 k, l, m, n, o,
 p, q, r, s, t ]

```

通过计算， ColorMatrix 可以把要绘制的像素进行转换。对于颜色 [R, G, B, A] ， 转换算法是这样的：

```
R’ = a*R + b*G + c*B + d*A + e;
G’ = f*R + g*G + h*B + i*A + j;
B’ = k*R + l*G + m*B + n*A + o;
A’ = p*R + q*G + r*B + s*A + t; 

```

ColorMatrix 有一些自带的方法可以做简单的转换，例如可以使用 setSaturation(float sat) 来设置饱和度；另外也可以去设置它的每一个 元素来对转换效果做精细调整。







以上，就是 Paint 对颜色的第二层处理：通过 setColorFilter(colorFilter) 来加工颜色。 除了基本颜色的设置（ setColor/ARGB() , setShader() ）以及基于原始颜色的过 滤（ setColorFilter() ）之外，Paint 最后一层处理颜色的方法是 setXfermode(Xfermode xfermode) ，它处理的是「当颜色遇上 View」的问题。

2020 7.4 15:48
