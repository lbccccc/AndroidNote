# ViewGroup 的触摸反馈、ViewPager

## ViewGroup 的触摸反馈

- 除了重写 onTouchEvent()，还需要重写 onInterceptTouchEvent()
- onInterceptTouchEvent() 中，ACTION_DOWN 事件做的事和 onTouchEvent() 基本⼀致或完全 ⼀致
  - 原因：ACTION_DOWN 在多数⼿势中起到的是起始记录的作⽤（例如记录⼿指落点），⽽ onInterceptTouchEvent() 调⽤后，onTouchEvent() 未必会被调⽤，因此需要把这个记录 责任转交给 onInterceptTouchEvent()。
  - 有时 ACTION_DOWN 事件也会在经过 onInterceptTouchEvent() 之后再转交给⾃⼰的 onTouchEvent()（例如当没有触摸到⼦ View 或者触摸到的⼦ View 没有消费事件时）。因 此需要确认在 onInterceptTouchEvent() 和 onTouchEvent() 都被调⽤时，程序状态不会出 问题。
- onInterceptTouchEvent() 中，ACTION_MOVE ⼀般的作⽤是确认滑动，即当⽤户朝某⼀⽅向滑 动⼀段距离（touch slop）后，ViewGroup 要向⾃⼰的⼦ View 和⽗ View 确认⾃⼰将消费事 件。
  - 确认滑动的⽅式：Math.abs(event.getX() - downX) > ViewConfiguration.getXxxSlop()
  - 告知⼦ View 的⽅式：在 onInterceptTouchEvent() 中返回 true，⼦ View 会收到 ACTION_CANCEL 事件，并且后续事件不再发给⼦ View
  - 告知⽗ View 的⽅式：调⽤ getParent().requestDisallowInterceptTouchEvent(true) 。这 个⽅法会递归通知每⼀级⽗ View，让他们在后续事件中不要再尝试通过 onInterceptTouchEvent() 拦截事件。这个通知仅在当前事件序列有效，即在这组事件结束 后（即⽤户抬⼿后），⽗ View 会⾃动对后续的新事件序列启⽤拦截机制

## VelocityTracker

- 如果 GestureDetector 不能满⾜需求，或者觉得 GestureDetector 过于复杂，可以⾃⼰处理 onTouchEvent() 的事件。但需要使⽤ VelocityTracker 来计算⼿指移动速度。
- 使⽤⽅法：
  - 在每个事件序列开始是（即 ACTION_DOWN 事件到来时），通过 VelocityTracker.obtain() 创建⼀个实例，或者使⽤ velocityTracker.clear() 把之前的某个实例重置
  - 对于每个事件（包括 ACTION_DOWN 事件），使⽤ velocityTracker.addMovement(event) 把事件添加进 VelocityTracker
  - 在需要速度的时候（例如在 ACTION_UP 中计算是否达到 fling 速度），使⽤ velocityTracker.computeCurrentVelocity(1000, maxVelocity) 来计算实时速度，并通过 getXVelocity() / getYVelocity() 来获取计算出的速度
    - ⽅法参数中的 1000 是指的计算的时间⻓度，单位是 ms。例如这⾥填⼊ 1000，那么 getXVelocity() 返回的值就是每 1000ms （即⼀秒）时间内⼿指移动的像素数
    - 第⼆个参数是速度上限，超过这个速度时，计算出的速度会回落到这个速度。例如这 ⾥填了 200，⽽实时速度是 300，那么实际的返回速度将是 200
      - maxVelocity 可以通过 viewConfiguration.getScaledMaximumFlingVelocity() 来获取

## scrollTo / scrollBy 和 computeScroll()

- scrollTo() / scrollBy() 会设置绘制时的偏移，通常⽤于滑动控件设置偏移

- scroll 值表示绘制⾏为在控件内部内容的起始偏移（类似：我要从内容的第 300 个像素开始绘 制），因此 scrollTo() 内的参数填正值时，绘制内容会向负向移动

- scrollTo() 是瞬时⽅法，不会⾃动使⽤动画。如果要⽤动画，需要配合 View.computeScroll() ⽅ 法

  - computeScroll() 在 View 重绘时被⾃动调⽤

  - 使⽤⽅式：

    ```
    // onTouchEvent() 中：
    overScroller.startScroll(startX, startY, dx, dy);
    postInvalidateOnAnimation();
    ......
    // onTouchEvent() 外：
    @Override
    public void computeScroll() {
     if (overScroller.computeScrollOffset()) { // 计算实时位置
     scrollTo(overScroller.getCurrX(),
    overScroller.getCurrY()); // 更新界⾯
     postInvalidateOnAnimation(); // 下⼀帧继续
     }
    }
    ```

    

2020 7.17 15:10
