https://www.jianshu.com/p/d607dbb09bd6

在讨论怎么减小Apk体积之前，理解一个应用的APK结构是非常有帮助的。一个apk文件就是由一个zip压缩包组成，这个zip包含了所有组成你应用的文件。这些文件包含了java的字节码文件，资源文件和一个包含了编译后资源的文件。
 一个apk包含了如下的目录：

- **META-INF/:** 包含了`CERT.SF`和`CERT.RSA`签名文件，以及`MANIFEST.MF`manifest 文件.
- **assets/:**包含了应用的`assets`，应用可以通过 [AssetManager](https://link.jianshu.com?t=https://developer.android.google.cn/reference/android/content/res/AssetManager.html)对象来获取这些资源.
- **res/:**包含了没有被编译成`resources.arsc`的所有资源.
- **lib/:**包含了用于软件处理的编译后的代码，这个目录还包含了针对不同平台类型的子目录 `armeabi`, `armeabi-v7a`, `arm64-v8a`, `x86`, `x86_64`和`mips`.

一个APK还包含了如下的文件，但是其中只有`AndroidManifest.xml`是必须的。

- **resources.arsc:**包含了编译后的资源。这个文件包含了`res/values/`文件夹下面的所有XML文件内容，打包工具抽取了XML文件内容，并把它编译成二进制文件格式，并且进行压缩。该内容包含了language strings(语言相关的字符串)和styles，并且包括没有直接放在`resources.arsc`文件中的内容路径，比如layout文件以及图片文件.
- **classes.dex:**包含了class文件编译成的dex文件，这是可以被Dalvik/ART虚拟机识别的文件格式。
- **AndroidManifest.xml:**包含了Android核心manifest文件。这个文件罗列了应用的名字，版本，访问权限和引用的library库。该文件采用Android的二进制XML格式。

### 2.减小资源数量和体积

 你apk的体积直接影响了你程序的加载速度，占用内存大小，消耗的电量。一个简单有效的方法是减小apk包含的资源的数量和体积。特别是，你可以移除一些不再使用的资源，或者你可以使用一些可扩展的 [Drawable](https://link.jianshu.com?t=https://developer.android.google.cn/reference/android/graphics/drawable/Drawable.html)对象来替代图片资源。这部分主要讨论这些方法以及另外可以减少资源占用的方法。

##### 移除无用的资源



##### 最小化Library库中资源使用

##### 只支持特定的分辨率

##### 使用drawable对象

##### 重用资源

##### 从代码中渲染

##### 处理PNG文件





2020 10.9 12.31
