activitythread会利用反射创建一个activity，activity中所有的生命周期回调方法onstart都是被这个activity对象调用的





在activitythread里首先会调用activity.attache方法对phonewindow的实例mwindow进行赋值，在去调用oncreate方法，而activity的setcontentview方法是在oncreate方法里，所以顺序是先phonewindow->oncreate->setcontentview





19.20  

activity和window是通过attach方法进行关联，window和view是通过setcontentview进行关联



# setContentView

> 23.31 
>
> 在setcontentview方法内会判断有没有mcontentparent，没有则进入installdecor()方法，接着进入后会判断有没有mdecor（这是个decorview，是window里面的顶层view）
>
> 没有mdecor的话会对mdecor进行初始化，然后在判断有没有mcontentparent，没有的话也进行初始化
>
> 
>
> 
>
> 接着两个变量，一个layoutresource和features，这个feature就是我们在oncreate方法里面调用requestWindowFeature方法时候所传进去的（这个方法可以设置没有标题栏之类的），而且这个方法要放在setcontentview前面，因为setcontentview需要用到这个feature
>
> 29.57
>
> 如果feature什么都不设置的话，layoutresource会被赋值为一个linearlayout布局内嵌着viewstub，这个里面就是actionbar，之所以这样而不是直接设置一个actionbar是因为如果viewstub是懒加载，只有需要的时候才能用，所以如果actionbar被隐藏了就不需要再去加载，节省性能。
>
> viewstub下面是一个framelayout，id为content
>
> 
>
> 然后就会把这个layoutsource通过inflate加载出来一个view加载到mdecor里
>
> 
>
> 接着findviewbyid找到mdecor里面那个名字是content的view，然后进行一系列初始化设置
>
> 
>
> 
>
> 最后就是利用setcontentview传入的布局id把传进来的布局加载到content的framelayout里面





首先要知道子线程更新ui报错都是因为viewrootimp会调用requestlayout，接着调用checkthread方法检查线程



decorview的父view为viewrootimp，然后requestlayout方法会判断当前view有没有父view，这样一直递归到decorview，如果这个时候viewrootimp还没有被创建出来，requestlayout链在这就停了，自然也不会报错



此外 viewrootimp调用checktread方法里检查的是当前想要更新ui的线程和viewrootimp被创建出来的线程是不是同一个





47.58

# handleResumeActivity

在activitythread中调用handleresumeactivity时候，

首先调用生命周期的onresume方法

从activity对象里面获得 decor实例

然后从activity对象里面获得windowmanager

接着再获得布局属性，并在windowmanager的addview方法里面传入布局属性和decor

- ## 在addview方法里面

创建viewrootimp



在addview里面还会把相关的view和root都加入到mWindowmanagerglobal（55.39）



然后调用viewrootimp的setview方法，看后面





先小小总结一下（51.43），activity实例中有个mwindowmanager，这个东西指向mWindowmanagerglobal（这是个单例，它会记录所有的view和imp，但是这个是隐藏的，要反射去拿），然后在由这个创建viewrootimp





# viewrootimp.setView

这个方法会把decorview的父类设置为viewrootimp

2020 10.11 0.04
