布局
居中
xml 代码不要去记，看⼀遍就够了
居中于⽗容器
居中于控件中⼼
⽔平⽅向居中
垂直⽅向居中
居中于控件的边
控件垂直居中于 view 的「下边」
app:layout_constraintStart_toStartOf="parent"
app:layout_constraintEnd_toEndOf="parent"
app:layout_constraintTop_toTopOf="parent"
app:layout_constraintBottom_toBottomOf="parent"
app:layout_constraintStart_toStartOf="@id/view"
app:layout_constraintEnd_toEndOf="@id/view"
app:layout_constraintTop_toTopOf="@id/view"
app:layout_constraintBottom_toBottomOf="@id/view"
填充
⽔平⽅向填充⽗容器（通过 match_constraint ）
备注：在早期版本中 match_parent 没有效果。
权重
为⽔平⽅向的控件设置权重，⼤⼩为 2:1:1 。
⽂字基准线对⻬
app:layout_constraintTop_toBottomOf="@id/view"
app:layout_constraintBottom_toBottomOf="@id/view"
app:layout_constraintStart_toStartOf="parent"
app:layout_constraintEnd_toEndOf="parent"
android:layout_width="0dp"
<!-- (view-1) -->
android:layout_width="0dp"
app:layout_constraintHorizontal_weight="2"
<!-- (view-2) -->
android:layout_width="0dp"
app:layout_constraintHorizontal_weight="1"
<!-- (view-3) -->
android:layout_width="0dp"
app:layout_constraintHorizontal_weight="1"
圆形定位
通过「圆⼼」「⻆度」「半径」设置圆形定位
特殊属性
约束限制
限制控件⼤⼩不会超过约束范围。
偏向
控制控件在垂直⽅向的 30%的位置
除了配合百分⽐定位，还有⽤于配合有时在「约束限制」的条件下不需要居中效果
的情况
垂直⽅向居顶部
app:layout_constraintBaseline_toBaselineOf
app:layout_constraintCircle="@id/view"
app:layout_constraintCircleAngle="90"
app:layout_constraintCircleRadius="180dp"
app:layout_constrainedWidth="true"
app:layout_constrainedHeight="true
app:layout_constraintTop_toBottomOf="parent"
app:layout_constraintBottom_toBottomOf="parent"
app:layout_constraintVertical_bias="0.3"
约束链
在约束链上的第⼀个控件上加上 chainStyle ，⽤来改变⼀组控件的布局⽅式
packed（打包
spread （扩散）
spread_inside（内部扩散）
垂直⽅向 packed
宽⾼⽐
⾄少需要⼀个⽅向的值为 match_constraint
默认的都是「宽⾼⽐」，然后根据另外⼀条边和⽐例算出
match_constraint 的值
x:y 默认表示的都是 width:height
宽是 0dp，⾼是 100dp，ratio 是 2:1
默认情况下是宽是 200dp，但是我们可以指定被约束的边是 height，那么宽度
就是50 dp
⾼是 0dp，宽是 100 dp，ratio 是 2:1
默认情况下是⾼是 50 dp，但是我们指定被约束的边是 width，那么⾼度为
200dp
百分⽐布局
app:layout_constraintTop_toBottomOf="parent"
app:layout_constraintBottom_toBottomOf="parent"
app:layout_constrainedHeight="true"
app:layout_constraintVertical_bias="0.0"
app:layout_constraintVertical_chainStyle="packed"
百分⽐布局
需要对应⽅向上的值为 match_constraint
百分⽐是 parent 的百分⽐，⽽不是约束区域的百分⽐
宽度是⽗容器的 30%
辅助控件
GuideLine
设置辅助线的⽅向 android:orientation="vertical"
设置辅助线的位置，根据⽅向不同
距离左侧或上侧的距离 layout_constraintGuide_begin
距离右侧或下侧的距离 layout_constraintGuide_end
百分⽐ layout_constraintGuide_percent
Group
通过 constraint_referenced_ids 使⽤引⽤的⽅式来避免布局嵌套。
可以为⼀组控件统⼀设置 setVisibility
只有设置可⻅度的功能，不要指望这个来通知设置点击事件...
Layer
和 Group 类似，同样通过引⽤的⽅式来避免布局嵌套，可以为⼀组控件统⼀设置旋
转/缩放/ 位移。
android:layout_width="0dp"
app:layout_constraintWidth_percent="0.3"
Barrier
通过设置⼀组控件的某个⽅向的屏障，来 避免布局嵌套 。
Placeholder
通过 setContentId 来将指定控件放到占位符的位置。
Flow
通过引⽤的⽅式来避免布局嵌套。
wrapMode
chain
aligned
none(默认)
注意这个控件是可以被测量的，所以对应⽅向上的值可能需要被确定（即不能只约
束同⼀⽅ 向的单个约束）
ConstraintSet
使⽤ ConstraintSet 对象来动态修改布局。
防⽌布局中有⽆ id 控件时报错，需要设置 isForceId = false
通过 ConstraintSet#clone 来从 xml 布局中获取约束集。
布局扁平化更加容易做过渡动画
在布局修改之前加上 TransitionManager 来⾃动完成过渡动画。
2020 9.16 16.56
