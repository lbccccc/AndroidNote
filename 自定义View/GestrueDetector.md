# ScalableImageView

## 放缩和移动

由于有两次移动，⼀次是初始偏移，另⼀次是随⼿指拖动，所以要分开两次写 translate()

```
canvas.translate(offsetX * scalingFraction, offsetY * scalingFraction); //
⼆次⼿动偏移
float imageScale = smallImageScale + (bigImageScale - smallImageScale) *
scalingFraction;
canvas.scale(imageScale, imageScale, getWidth() / 2, getHeight() / 2); //
放缩
canvas.translate(originalOffsetX, originalOffsetY); // 初始偏移
canvas.drawBitmap(bitmap, 0, 0, paint);
```

## GestrueDetector

⽤于在点击和⻓按之外，增加其他⼿势的监听，例如双击、滑动。通过在 View.onTouchEvent() ⾥调⽤ GestureDetector.onTouchEvent() ，以代理的形式来实现：

```
@Override
public boolean onTouchEvent(MotionEvent event) {
 return gestureDetector.onTouchEvent(event);
}
```

GeasureDetector 的默认监听器：OnGestureListener 通过构造⽅法 GeasureDetector(Context, OnGestureListener) 来配置：

```
gestureDetector = new GestureDetector(context, gestureListener);
```

##### OnGestureListener 的⼏个回调⽅法：

```
@Override
public boolean onDown(MotionEvent e) {
 // 每次 ACTION_DOWN 事件出现的时候都会被调⽤，在这⾥返回 true 可以保证必然消费掉
事件
 return true;
}
@Override
public void onShowPress(MotionEvent e) {
 // ⽤户按下 100ms 不松⼿后会被调⽤，⽤于标记「可以显示按下状态了」
}
@Override
public boolean onSingleTapUp(MotionEvent e) {
 // ⽤户单击时被调⽤(⻓按后松⼿不会调⽤、双击的第⼆下时不会被调⽤)
 return false;
}
@Override
public boolean onScroll(MotionEvent downEvent, MotionEvent event, float
distanceX, float distanceY) {
 // ⽤户滑动时被调⽤
 // 第⼀个事件是⽤户按下时的 ACTION_DOWN 事件，第⼆个事件是当前事件
 // 偏移是按下时的位置 - 当前事件的位置
 return false;
}
@Override
public void onLongPress(MotionEvent e) {
 // ⽤户⻓按（按下 500ms 不松⼿）后会被调⽤
 // 这个 500ms 在 GestureDetectorCompat 中变成了 600ms (？？？)
}
@Override
public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
float velocityY) {
 // ⽤于滑动时迅速抬起时被调⽤，⽤于⽤户希望控件进⾏惯性滑动的场景
 return false;
}
```

## 双击监听器：OnDoubleTapListener

通过 GestureDetector.setOnDoubleTapListener(OnDoubleTapListener) 来配置：

```
gestureDetector.setOnDoubleTapListener(doubleTapListener);
```

#### OnDoubleTapListener 的⼏个回调⽅法：

```
@Override
public boolean onSingleTapConfirmed(MotionEvent e) {
 // ⽤户单击时被调⽤
 // 和 onSingltTapUp() 的区别在于，⽤户的⼀次点击不会⽴即调⽤这个⽅法，⽽是在⼀定
时间后（300ms），确认⽤户没有进⾏双击，这个⽅法才会被调⽤
 return false;
}
@Override
public boolean onDoubleTap(MotionEvent e) {
 // ⽤户双击时被调⽤
 // 注意：第⼆次触摸到屏幕时就调⽤，⽽不是抬起时
 return false;
}
@Override
public boolean onDoubleTapEvent(MotionEvent e) {
 // ⽤户双击第⼆次按下时、第⼆次按下后移动时、第⼆次按下后抬起时都会被调⽤
 // 常⽤于「双击拖拽」的场景
 return false;
}
```

## OverScroller

⽤于⾃动计算滑动的偏移。

```
scroller = new OverScroller(context);
```

常⽤于 onFling() ⽅法中，调⽤ OverScroller.fling() ⽅法来启动惯性滑动的计算：

```
@Override
public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX,
float velocityY) {
 // 初始化滑动
 scroller.fling(startX, startY, velocityX, velocityY, minX, maxX, minY,
maxY);
 // 下⼀帧刷新
 postOnAnimation(flingRunner);
 return false;
}
...
@Override
public void run() {
 // 计算此时的位置，并且如果滑动已经结束，就停⽌
 if (scroller.computeScrollOffset()) {
// 把此时的位置应⽤于界⾯
offsetX = scroller.getCurrX();
offsetY = scroller.getCurrY();
invalidate();
// 下⼀帧刷新
postOnAnimation(this);
 }
}
```

2020 7.16 16:12
