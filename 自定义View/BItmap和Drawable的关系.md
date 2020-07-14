# Bitmap 和 Drawable的关系

- ### Bitmap 是什么 

Bitmap 是位图信息的存储，即⼀个矩形图像每个像素的颜⾊信息的存储器。

- ### Drawable 是什么

Drawable 是⼀个可以调⽤ Canvas 来进⾏绘制的上层⼯具。调⽤ Drawable.draw(Canvas) 可以把 Drawable 设置的绘制内容绘制到 Canvas 中。 Drawable 内部存储的是绘制规则，这个规则可以是⼀个具体的 Bitmap，也可以是⼀个纯粹的颜⾊， 甚⾄可以是⼀个抽象的、灵活的描述。Drawable 可以不含有具体的像素信息，只要它含有的信息⾜ 以在 draw(Canvas) ⽅法被调⽤时进⾏绘制就够了。 由于 Drawable 存储的只是绘制规则，因此在它的 draw() ⽅法被调⽤前，需要先调⽤ Drawable.setBounds() 来为它设置绘制边界。

- ### Bitmap 和 Drawable 的互相转换

 事实上，由于 Bitmap 和 Drawable 是两个不同的概念，因此确切地说它们并不是互相「转换」，⽽ 是从其中⼀个获得另⼀个的对象：

-  Bitmap -> Drawable：创建⼀个 BitmapDrawable。
-  Drawable -> Bitmap：如果是 BitmapDrawable，使⽤ BitmapDrawable.getBitmap() 直接 获取；如果不是，创建⼀个 Bitmap 和⼀个 Canvas，使⽤ Drawable 通过 Canvas 把内容绘制 到 Bitmap 中。

## ⾃定义 Drawable

- ####  怎么做

  -  重写⼏个抽象⽅法
  -  重写 setAlpha() 的时候记得重写 getAlpha() 
  - 重写 draw(Canvas) ⽅法，然后在⾥⾯做具体的绘制⼯作

- 有⽤吗
  -  有⽤。它就是⼀个更加抽象和专注的、仅仅⽤于绘制的⾃定义 View 模块。

- ⽤来⼲嘛

  - 需要共享在多个 View 之间的绘制代码，写在 Drawable ⾥，然后在多个⾃定义 View ⾥只 要引⽤相同的 Drawable 就好，⽽不⽤互相粘贴代码。
  - 例如在股票软件的多个蜡烛图界⾯，可以把共享的蜡烛图界⾯放进去

  

2020 7.14 23:54
