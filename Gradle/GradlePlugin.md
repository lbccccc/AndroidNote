# Gradle Plugin

## Groovy 两个语法点

- getter / setter 
  - 每个 field，Groovy 会⾃动创建它的 getter 和 setter ⽅法，从外部可以直接调⽤，并且在 使⽤ object.fieldA 来获取值或者使⽤ object.fieldA = newValue 来赋值的时候，实际上会 ⾃动转⽽调⽤ object.getFieldA() 和 object.setFieldA(newValue)

- 字符中的单双引号 单引号是不带转义的，⽽双引号内的内容可以使⽤ "string1${var}string2"的⽅式来转义

# Gradle Plugin

- 本质：把逻辑独⽴的代码抽取和封装

# Plugin 的最基本写法

## 写在 build.gradle ⾥：

> build.gradle:

```
class PluginDemo implements Plugin<Project> {
 @Override
 void apply(Project target) {
 println 'Hello author!'
 }
}
apply plugin: PluginDemo
```

#### Extension

> build.gradle:

```
class ExtensionDemo {
 def author = 'Kai'
}
class PluginDemo implements Plugin<Project> {
 @Override
 void apply(Project target) {
 def extension = target.extensions.create('hencoder', ExtensionDemo)
 target.afterEvaluate {
 println "Hello ${extension.author}!"
 }
 }
}
apply plugin: PluginDemo
hencoder {
 author 'rengwuxian'
}
```

# 写在 buildSrc ⽬录下

- ⽬录结构：

  > ▼■buildSrc (~/HenCoder/priv. _projects/ GradleDemo/buildSrc)
  > 		srC 
  > 			main
  > 				groovy
  > 					com
  > 						hencoder
  > 							plugin_ demo
  > 								DemoExtension.groovy
  > 								DemoPlugin.groovy
  > 								DemoTransform.groovy
  > 			▼resources
  > 				▼META-INF
  > 						gradle plugins
  > 							com.hencoder. plugindemo.properties

- ```resources/META-INF/gradle-plugins/*.properties ```中的 * 是插件的名称，例如 *.properties 是 ```com.hencoder.plugindemo.properties```，最终在应⽤插件是的代码就 应该是：

  ```
  apply plugin: 'com.hencoder.plugindemo'
  ```

- *.properties 中只有⼀⾏，格式是：

  ```
  implementation-class=com.hencoder.plugin_demo.DemoPlugin
  ```

  其中等号右边指定了 Plugin 具体是哪个类

- Plugin 和 Extension 写法和在 build.gradle ⾥的写法⼀样

- 关于 buildSrc ⽬录

  - 这是 gradle 的⼀个特殊⽬录，这个⽬录的 build.gradle 会⾃动被执⾏，即使不配配置进 settings.gradle
  - buildSrc 的执⾏早于任何⼀个 project，也早于 settings.gradle。它是⼀个独⽴的存在
  - buildSrc 所配置出来的 Plugin 会被⾃动添加到编译过程中的每⼀个 project 的 classpath， 因此它们才可以直接使⽤ apply plugin: 'xxx' 的⽅式来便捷应⽤这些 plugin
  - settings.gradle 中如果配置了 ':buildSrc' ，buildSrc ⽬录就会被当做是⼦ Project ， 因此会被执⾏两遍。所以在 settings.gradle ⾥⾯应该删掉 ':buildSrc' 的配置

Transform

- 是什么：是由 Android 提供了，在项⽬构建过程中把编译后的⽂件（jar ⽂件和 class ⽂件）添 加⾃定义的中间处理过程的⼯具

- 怎么写

  - 先加上依赖：

    ```
    // 因为 buildSrc 早于任何⼀个 project 执⾏，因此需要⾃⼰添加仓库
    repositories {
     google()
     jcenter()
    }
    dependencies {
     implementation 'com.android.tools.build:gradle:3.1.4'
    }
    ```

  - 然后继承 com.android.build.api.transform.Transform ，创建⼀个⼦类：

    ```
    class DemoTransform extends Transform {
     // 构造⽅法
     DemoTransform() {
     }
     // 对应的 task 名
     @Override
     String getName() {
     return 'hencoderTransform'
     }
     // 你要对那些类型的结果进⾏转换(是字节码还是资源⽂件？)
     @Override
     Set<QualifiedContent.ContentType> getInputTypes() {
     return TransformManager.CONTENT_CLASS
     }
     // 适⽤范围包括什么(整个 project 还是别的什么？)
     @Override
     Set<? super QualifiedContent.Scope> getScopes() {
     return TransformManager.SCOPE_FULL_PROJECT
     }
     @Override
     boolean isIncremental() {
     return false
     }
     // 具体的「转换」过程
     @Override
     void transform(TransformInvocation transformInvocation) throws
    TransformException, InterruptedException, IOException {
     def inputs = transformInvocation.inputs
     def outputProvider = transformInvocation.outputProvider
     inputs.each {
     // jarInputs：各个依赖所编译成的 jar ⽂件
     it.jarInputs.each {
     // dest:
     //
    ./app/build/intermediates/transforms/hencoderTransform/...
     File dest =
    outputProvider.getContentLocation(it.name, it.contentTypes,
    it.scopes, Format.JAR)
     FileUtils.copyFile(it.file, dest)
     }
     // derectoryInputs：本地 project 编译成的多个 class ⽂件存
    放的⽬录
     it.directoryInputs.each {
     // dest:
     //
    ./app/build/intermediates/transforms/hencoderTransform/...
     File dest =
    outputProvider.getContentLocation(it.name, it.contentTypes,
    it.scopes, Format.DIRECTORY)
     FileUtils.copyDirectory(it.file, dest)
     }
     }
     }
    }
    ```

  - 还能做什么：修改字节码 上⾯的这段代码只是把编译完的内容原封不动搬运到⽬标位置， 没有实际⽤处。要修改字节码，需要引⼊其他⼯具，例如 javassist。 javassist 的使⽤教程 在⽹上有很多，可以搜索⼀下。

2020 7.21 16:30

