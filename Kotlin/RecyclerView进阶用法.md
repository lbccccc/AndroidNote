# RecycleView

回收机制：往上滑的时候出去的一个view会被重新利用，从下面出来

## ListView的局限

- 只有纵向列表一种布局
- 没有支持动画的API
- 接口设计和系统不一致
  - setOnItemClickListener
  - setOnItenLongClickListener
  - setSecetion
- 没有强制实现ViewHolder
- 性能不如RecyclerView

## RecyclerView的优势

- 默认支持Linear,Gird,Staggered Grid 三种布局
- 友好的ItemAnimator动画API
- 强制实现ViewHolder
- 解耦的架构设计
- 比ListView更好的性能





## View holder

View holder 和Item view是一对一的关系

RecyclerView 和ListView 都可以复用View，但是RecyclerView在findviewbyId这一步有优化，节约了很多时间

- ListView中RcycleBin专门用于管理缓存，其中有两层
  - active view：处于界面中的view
  - scrap view：已经使用过了，被回收的view

- LayoutManager 的Recycler在Create View时有四步
  - Scrap 寻找屏幕内部(scrap和下面的cache都是通过position来找到缓存，而且数据没有被污染所以，不需要重新绑定数据)
  - cache
  - ViewCaheExtension 用户自定义的cache策略
  - RecyclerViewPool 缓存池(里面的数据dirty，需要重新绑定数据)

## RecyclerView性能优化

- 在onBindViewHolder里设置点击监听器会导致重复创建对象
  - 应该在onCreateViewHolder里设置监听

- 竖向的RecyclerView嵌套着横向的RecyclerView
  - 用户滑动到横向滑动的item RecyclerView的时候,由于需要创建更复杂的
    RecyclerView以及多个子view,可能会导致页面卡顿
  - 由于RenderThread的存在, RecyclerView会进行prefetch
  - LinearLayoutManager. setlnitialPrefetchltemCount(横向列表初次显示时可
    见的item个数)
    - 只有LinearL ayoutManager有这个API
    - 只有嵌套在内部的RecyclerView才会生效

- RecyclerView.setHasFixedSize()
  - 当有item插入，溢出或者其他内容变化的时候，且不导致RecyclerView的大小变化，就使用这个可以优化布局过程

- 多个RecyclerView共用RecycledViewPool

  ```
  RecycLerView.RecycledViewPool recycledViewPool =
  new RecyclerView.RecycledViewPool() ;
  recycLerView1.setRecycLedViewPool( recycLedViewPool) ;
  recycLerView2.setRecycledViewPool( recycledViewPool) ;
  recycLerView3.setRecycledViewPool( recycledViewPool);
  ```

- DiffUtil
  - 局部更新方法notifyltemXXX()不适用于所有情况
  - notifyDataSetChange()会导致整个布局重绘,重新绑定所有ViewHolder,而
    且会失去可能的动画效果
  - DiffUtil适用于整个页面需要刷新,但是有部分数据可能相同的情况

## ItemDecoration

- Drawing dividers between items   分割线
- Highlights  高亮
- Visual grouping boundaries  分组

2020 7.19 15:25
