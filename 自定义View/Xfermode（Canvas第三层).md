# setXfermode(Xfermode xfermode)

 "Xfermode" 其实就是 "Transfer mode"，用 "X" 来代替 "Trans" 是一些美国人喜欢用 的简写方式。严谨地讲， Xfermode 指的是你要绘制的内容和 Canvas 的目标位置 的内容应该怎样结合计算出最终的颜色。但通俗地说，其实就是要你以绘制的内容 作为源图像，以 View 中已有的内容作为目标图像，选取一个 PorterDuff.Mode 作 为绘制内容的颜色处理方案。

```
Xfermode xfermode = new PorterDuffXfermode(PorterDuff.Mode.DST_IN);
...
canvas.drawBitmap(rectBitmap, 0, 0, paint); // 画方
paint.setXfermode(xfermode); // 设置 Xfermode
canvas.drawBitmap(circleBitmap, 0, 0, paint); // 画圆
paint.setXfermode(null); // 用完及时清除 Xfermode 
```

又是 PorterDuff.Mode 。 PorterDuff.Mode 在 Paint 一共有三处 API ，它们的 工作原理都一样，只是用途不同：

- ComposeShader:用途是混合两个Shader
- PorterDuffColorFilter:增加一个单色的ColorFilter
- Xfermode:设置绘制内容和View中已有内容的混合计算方式

# Xfermode 注意事项

### 使用离屏缓冲（Off-screen Buffer）

要想使用 setXfermode() 正常绘制，必须使用离屏缓存 (Off-screen Buffer) 把内容 绘制在额外的层上，再把绘制好的内容贴回 View 中。

通过使用离屏缓冲，把要绘制的内容单独绘制在缓冲层， Xfermode 的使用就不会 出现奇怪的结果了。使用离屏缓冲有两种方式：

- #####  Canvas.saveLayer() 

  saveLayer() 可以做短时的离屏缓冲。使用方法很简单，在绘制代码的前后各 加一行代码，在绘制之前保存，绘制之后恢复：

```
int saved = canvas.saveLayer(null, null, Canvas.ALL_SAVE_FLAG);//保存
canvas.drawBitmap(rectBitmap, 0, 0, paint); // 画方
paint.setXfermode(xfermode); // 设置 Xfermode
canvas.drawBitmap(circleBitmap, 0, 0, paint); // 画圆
paint.setXfermode(null); // 用完及时清除 Xfermode
canvas.restoreToCount(saved);//恢复
```

- ##### View.setLayerType()

  ```
  View.setLayerType() 是直接把整个 View 都绘制在离屏缓冲中。
  setLayerType(LAYER_TYPE_HARDWARE) 是使用 GPU 来缓冲，
  setLayerType(LAYER_TYPE_SOFTWARE) 是直接直接用一个 Bitmap 来缓冲。
  ```

  

如果没有特殊需求，可以选用第一种方法 Canvas.saveLayer() 来设置离屏缓冲， 以此来获得更高的性能。

## 控制好透明区域

使用 Xfermode 来绘制的内容，除了注意使用离屏缓冲，还应该注意控制它的透明 区域不要太小，要让它足够覆盖到要和它结合绘制的内容，否则得到的结果很可能 不是你想要的。(具体见文档的图片)

由于透明区域过小而覆盖不到的地方，将不会受到 Xfermode 的影 响。





到此为止，前面讲的就是 Paint 的第一类 API——关于颜色的三层设置：直接 设置颜色的 API 用来给图形和文字设置颜色； setColorFilter() 用来基于颜色进 行过滤处理； setXfermode() 用来处理源图像和 View 已有内容的关系。
