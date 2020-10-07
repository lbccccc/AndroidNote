## Intent 详解 

  三大组件——Activity、Service和Broadcast Receiver都是被什么激活的？

 答案：Intent是一种运行时绑定的消息机制，而三大组件——Activity、Service和Broadcast Receiver都是被消息激活的，这种消息就是Intent。    

##### 一个Intent对象包括六个属性，请一一列举这六个属性。 

##### 答案：组件名（Component Name）、动作（Action）、数据（Data）、分类（Category）、额外信息（Extra）和标志（Flags）。   

在一个Android应用中，主要是由一些组件组成，（Activity,Service,ContentProvider,etc.)在这些组件之间的通讯中，由Intent协助完成。 正如网上一些人解析所说，Intent负责对应用中一次操作的动作、动作涉及数据、附加数据进行描述，Android则根据此Intent的描述，负责找到对应的组件，将 Intent传递给调用的组件，并完成组件的调用。Intent在这里起着实现调用者与被调用者之间的解耦作用。

 Intent传递过程中，要找到目标消费者（另一个Activity,IntentReceiver或Service），也就是Intent的响应者，有两种方法来匹配：   

1. 显示意图： 伪代码

   ~~~java
     ```
   public TestB extents Activity  
   {  
    .........  
   };  
    public class Test extends Activity {  
        ......  
        public void switchActivity()  
        {  
               Intent i = new Intent(Test.this, TestB.class);  
               this.startActivity(i);  
        }  
   } 
   ~~~

   2、隐式意图:

   隐式匹配，首先要匹配Intent的几项值：Action, Category, Data/Type,Component
   如果填写了Componet就是上例中的Test.class)这就形成了显示匹配。所以此部分只讲前几种匹配。匹配规则为最大匹配规则，

   1、如果你填写了Action，如果有一个程序的Manifest.xml中的某一个Activity的IntentFilter段中定义了包含了相同的Action那么这个Intent就与这个目标Action匹配，如果这个Filter段中没有定义Type,Category，那么这个Activity就匹配了。但是如果手机中有两个以上的程序匹配，那么就会弹出一个对话可框来提示说明。
   Action的值在Android中有很多预定义，如果你想直接转到你自己定义的Intent接收者，你可以在接收者的IntentFilter中加入一个自定义的Action值（同时要设定Category值为”android.intent.category.DEFAULT”），在你的Intent中设定该值为Intent的Action,就直接能跳转到你自己的Intent接收者中。因为这个Action在系统中是唯一的。
   2、data/type，你可以用Uri来做为data,比如Uri uri = Uri.parse([http://www.google.com](http://www.google.com/) );
   Intent i = new Intent(Intent.ACTION_VIEW,uri);手机的Intent分发过程中，会根据[http://www.google.com](http://www.google.com/) 的scheme判断出数据类型type
   手机的Brower则能匹配它，在Brower的Manifest.xml中的IntenFilter中首先有ACTION_VIEW Action,也能处理http:的type，

   3、至于分类Category，一般不要去在Intent中设置它，如果你写Intent的接收者，就在Manifest.xml的Activity的IntentFilter中包含android.category.DEFAULT,这样所有不设置Category（Intent.addCategory(String c);）的Intent都会与这个Category匹配。

   4、extras（附加信息），是其它所有附加信息的集合。使用extras可以为组件提供扩展信息，比如，如果要执行“发送电子邮件”这个动作，可以将电子邮件的标题、正文等保存在extras里，传给电子邮件发送组件。

   1. **IntentFilter（Intent过滤器）**
      为什么要引入IntentFilter？
      答案：对于显式Intent，它的接受者已被指定，所以系统会自动把这个Intent发给指定的组件。但是对于隐式Intent，由于并没有指定其组件名属性，所以系统不知道该把它发给哪个组件名，于是系统就直接将其发出去，算是所有的组件都有权接收，这就需要定义一个组件可以接收到哪些Intent，所以就引入了IntentFilter（Intent过滤器）。
   2. **Intent 传递数据时,可以传递哪些类型数据?**
      Intent 可以传递的数据类型非常的丰富,java 的基本数据类型和 String 以及他们的数组形式都可以,除此之 外还可以传递实现了 Serializable 和 Parcelable 接口的对象。
   3. **Serializable 和 Parcelable 的区别?**
      1.在使用内存的时候,Parcelable 类比 Serializable 性能高,所以推荐使用 Parcelable 类。
      2.Serializable 在序列化的时候会产生大量的临时变量,从而引起频繁的 GC。
      3.Parcelable 不能使用在要将数据存储在磁盘上的情况。尽管 Serializable 效率低点,但在这种情况下,还是建
      议你用 Serializable 。 实现:
      1 Serializable 的实现,只需要继承 Serializable 即可。这只是给对象打了一个标记,系统会自动将其序列化。
      2 Parcelabel 的实现,需要在类中添加一个静态成员变量 CREATOR,这个变量需要继承 Parcelable.Creator 接 口。
      
      2020  10.7 20.52
