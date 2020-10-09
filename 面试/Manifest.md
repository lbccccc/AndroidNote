https://www.cnblogs.com/dong-liu/p/7466685.html

# ProGuard

ProGuard是一个压缩、优化和混淆Java[字节码](https://baike.baidu.com/item/字节码)文件的免费的工具，它可以删除无用的类、字段、方法和属性。可以删除没用的注释，最大限度地优化字节码文件。它还可以使用[简短](https://baike.baidu.com/item/简短/7933503)的无意义的名称来重命名已经存在的类、字段、方法和属性。常常用于Android开发用于混淆最终的项目，增加项目被反编译的难度。



# Manifest

整体介绍：作为整个Android应用的入口，Manifest.xml 描述了package中暴露的组件，他们各自的实现类，各种能被处理的数据和启动位置。出了四大组件的声明，还指定了app的一些权限（permission）和instrumentation（安全控制和测试）。

####  Manifest属性

package：指定应用中java主程序的包名，这个也是整个应用程序默认的名称

还有一些常用的可以加进去

android:versionCode = "1" ：版本号

android:versionName="first version" ：版本名字

android:installLocation=["auto" | "internalOnly" | "preferExternal"] ：几个选项，分别是auto自动选择安装位置，internalOnly必须安装在ROM上，preferExternal优先安装到SD卡上面去

 

#### permission属性

uses-sdk: 这里是对最小版本sdk的要求

各种需要的permission权限都需要添加到文件中。



#### application属性：

声明一个应用程序的组件及其属性（icon，label，permission等等）

基本上大多数在生成project的时候就会默认生成，一般的开发者也不需要多管

android:allowBackup: 允许用户通过adb backup和adb restore来进行对应数据的备份和恢复。

android:label：project的工程名字

android:roundIcon: 现在android新建项目一般有两个图标，一个是icon（普通图标），一个是roundIcon（圆形图标）。

android:supportsRtl: 是否支持right-to-left布局，API 17新添加的功能

android:theme: android应用的风格，可以自定义风格，上面的是默认风格



### Activity属性

一个最简单的activity声明就是上图所示，一般activity的生命会自动添加

android：launchMode，Activity的加载模式，一共有四种，在我的另一篇文章中讲到过

android：multiprocess：是否允许多进程，默认是false的

android：screenOrientation：显示模式，默认是unspecified（自动调整方向）



### Intent-filter属性



这个当时被问到这个问题，问我filter有什么属性，常见的属性就这两种，结果面试官说有三种，回来看了一下，真的有三种

action，只有android：name这个属性，常见的也就是上图所示，表示这个activity是程序的入口

category，也只有一个android：name属性，决定应用程序是否显示在程序列表中

data，就是这个东西，貌似可以在这里存一下元数据，当在程序中需要的时候可以获取他的value值

当然intent-filter本身也有一些属性

android：icon

android：label

android：priority=“Integer”      有序广播按声明的优先级来进行，取值范围是-1000到1000，数字越大优先级越高



### 其他的三大组件，除了BroadcastReceiver可以动态注册外，都需要在manifest.xml中进行注册，



2020 10.9 13.21
