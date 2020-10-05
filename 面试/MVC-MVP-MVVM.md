https://blog.csdn.net/u011315960/article/details/82869206

https://blog.csdn.net/napolunyishi/article/details/22722345?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-4.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-4.channel_param

后端主要使用MVC，因为平台的特殊性，Android根据MVC模式演变出MVP模式，广泛的使用在开发中。

# MVC、MVP和MVVM的优缺点  

#### 1.MVC：

简介：MVC是模型(model)－视图(view)－控制器(controller)的缩写，用一种业务逻辑、数据、界面显示分离的方法组织代码。

其中M层处理数据，业务逻辑等；V层处理界面的显示结果；C层起到桥梁的作用，来控制V层和M层通信以此来达到分离视图显示和业务逻辑层

Android中的MVC

模型层（M）：针对业务模型，建立的数据结构以及相关的类，例如对数据库的操作，对网络数据的操作以及业务中的计算等操作应该放在该层。

视图层（V）：Android中的xml文件可以理解为视图层

控制层（C）：Android中的逻辑处理在Activity中体现。

MVC的优点：

1）：分工明确，各司所职。

2）：一定程度上降低了代码间的耦合性。

MVC的缺点：

1）：随着界面及其逻辑的复杂度不断提升，Activity类的职责不断增加，以致变得庞大臃肿。

2）：视图和控制器间过于紧密的联系，妨碍了各自的重用。

#### 2.MVP

简介：MVP是模型(model)－视图(view)－协调者(presenter)的缩写。

模型层（M）：负责存储，检索以及操纵数据。

视图层（V）：负责绘制UI，与用户进行交互（在Android中体现为Activity）

协调者（P）：作为View与Model交互的中间纽带，处理与用户交互的负责逻辑。

MVP的优点：

1）复杂的逻辑处理放在presenter进行处理，减少了activity的臃肿。

2）M层与V层完全分离，修改V层不会影响M层，降低了耦合性。

3）可以将一个Presenter用于多个视图，而不需要改变Presenter的逻辑。

3）P层与V层的交互是通过接口来进行的，便于单元测试。

MVP的缺点：

由于对视图的渲染放在了Presenter中，所以视图和Presenter的交互会过于频繁，视图需要改变，一般presenter也需要跟着改变

#### 3.MVVM:

简介：MVP的升级版本，VM是ViewModel的缩写，VM可以理解为View的数据模型和Presenter的合体，ViewModel和View之间的交互通过data binding完成。

ViewModel（VM）：ViewModel就是包含View的一些数据属性和操作，关键点就是使用databinding，View的变化会直接影响ViewModel，ViewModel的变化或者内容也会直接体现在View上。

MVVM的优点：

Data Binding可以实现双向的交互，使得视图和控制层之间的耦合程度进一步降低，分离更为彻底，同时减轻了Activity的压力。

# MVP与MVC的区别

1. **Activity职责不同**，Activity在MVP中是View层，在MVC中是Controller层，这是MVC和MVP很主要的一个区别，可以说Android从MVC转向MVP开发也主要是优化Activity的代码，避免Activity的代码臃肿庞大。
2. **View层不同**，MVC的View层指的是XML布局文件或者是用Java自定义的View，MVP的View层是Activity或者Fragment。使用传统的MVC，其中的View，对应的是各种Layout布局文件，但是这些布局文件中并不像Web端那样强大，能做的事情非常有限。MVP的View层Activity在实际项目中，随着逻辑的复杂度越来越大，Activity臃肿的缺点仍然体现出来了，因为Activity中还是充满了大量与View层无关的代码，比如各种事件的处理派发，就如MVC中的那样View层和Controller代码耦合在一起无法自拔。
3. **控制层不同**，MVC的控制层是Activity，或者是Fragment，Controller对应的是Activity，而Activity中却又具有操作UI的功能，我们在实际的项目中也会有很多UI操作在这一层，也做了很多View中应该做的事情，当然Controller层Activity中也包含Controller应该做的事情，比如各种事件的派发回调，而且在一层中我们会根据事件再去调用Model层操作数据，所以这种MVC的方式在实际项目中，Activity所在的Controller是非常重的，各层次之间的耦合情况也比较严重，不方便单元测试。MVP的控制层是Presenter，里面没有很多的实际东西，主要是做Model和View层的交互。
4. **关系链不同**，MVP中Model层与View是没有关系的，彼此不会通讯和操作，Model与View的通讯都是Presenter层来传达的。但是在MVC中，Model层和View是曾在交互的。比如我们自定义的View控件里面肯定是要使用Model的数据的，View也要根据不同的Model数据做出不同的展现！这点尤其是体现在自定义的View中，自定义View需要设置数据，用户操作了自定义控件需要改变数据，View要操作Model怎么办？有人说把Controller传到自定义的View啊，现实是不可能没一个自定义View都去持有Controller的引用，其实在MVP中就不会这么尴尬，接口就可以完成。
5. **适用范围不同**，在Android中，MVP和MVC都用自己的适用情况，使用MVP可以更好的解耦三大模块，模块之间比较清晰，也很方便使用MVP来组件化架构整体项目。但是MVC也是有用武之地的，在组件化的Module或者中间件我们可以使用MVC来做，Module或者中间件不会存在很复杂的View层，使用MVC可以更加方便我们实现功能。
6. **交互方式不同**，MVP中通讯交互基本都是通过接口的，MVC中的通讯交互很多时候都是实打实的调用对象的方法，简单粗暴！
7. **实现方法不同** ，MVC和MVP的Model几乎一样的，都是处理数据，只要不在Activity或者Fragment中请求数据，其他的所有控制都放在Activity或者Fragment中，这样写就基本是MVC的模式，这样写不麻烦，但是很容易把Activity写出上万行代码。用MVP的时候我们需要写很多View和Presenter接口来实现模块之间的通讯，会增加很多类。

### **网络流行对比段子：**

**（1）相同点：**
**优点：**
1.降低耦合度
2.模块职责划分明显
3.利于测试驱动开发
4.代码复用
5.隐藏数据
6.代码灵活性
**缺点：**
额外的代码复杂度及学习成本。

**（2）不同点：**
**MVP模式：**
1.View不直接与Model交互，而是通过与Presenter交互来与Model间接交互
2.Presenter与View的交互是通过接口来进行的，更有利于添加单元测试
3.通常View与Presenter是一对一的，但复杂的View可能绑定多个Presenter来处理逻辑，业务相似的时候也可以多同个View共享一个Presenter。
**MVC模式：**
1.View可以与Model直接交互
2.Controller是基于行为的，并且可以被多个View共享
3.Controller可以负责决定显示哪个View









1. Controller接收View的操作事件，根据事件不同，或者调用Model的接口进行数据操作，或者进行View的跳转，从而也意味着一个Controller可以对应多个View。Controller对View的实现不太关心，只会被动地接收，Model的数据变更不通过Controller直接通知View，通常View采用观察者模式监听Model的变化。
2. Presenter，与Controller一样，接收View的命令，对Model进行操作；与Controller不同的是Presenter会反作用于View，Model的变更通知首先被Presenter获得，然后Presenter再去更新View。一个Presenter只对应于一个View。根据Presenter和View对逻辑代码分担的程度不同，这种模式又有两种情况：Passive View和Supervisor Controller。
3. ViewModel，注意这里的“Model”指的是View的Model，跟上面那个Model不是一回事。所谓View的Model就是包含View的一些数据属性和操作的这么一个东东，这种模式的关键技术就是数据绑定（data binding），View的变化会直接影响ViewModel，ViewModel的变化或者内容也会直接体现在View上。这种模式实际上是框架替应用开发者做了一些工作，开发者只需要较少的代码就能实现比较复杂的交互。

2020 10.5 17.06
