# object

## **static property / function**

刚才说到大家都不喜欢写 `final` 对吧？但有一种场景，大家是最喜欢用 `final` 的：常量。

```java
☕️
public static final String CONST_STRING = "A String";
```

Java

在 Java 里面写常量，我们用的是 `static` + `final`。而在 Kotlin 里面，除了 `final` 的写法不一样，`static` 的写法也不一样，而且是更不一样。确切地说：在 `Kotlin` 里，静态变量和静态方法这两个概念被去除了。

那如果想在 Kotlin 中像 Java 一样通过类直接引用该怎么办呢？Kotlin 的答案是 `companion object`：

```kotlin
🏝️
class Sample {
    ...
       👇
    companion object {
        val anotherString = "Another String"
    }
}
```

Kotlin

为啥 Kotlin 越改越复杂了？不着急，我们先看看 `object` 是个什么东西。

### `object`

Kotlin 里的 `object` ——首字母小写的，不是大写，Java 里的 `Object` 在 Kotlin 里不用了。

> Java 中的 `Object` 在 Kotlin 中变成了 `Any`，和 `Object` 作用一样：作为所有类的基类。

而 `object` 不是类，像 `class` 一样在 Kotlin 中属于关键字：

```kotlin
🏝️
object Sample {
    val name = "A name"
}
```

Kotlin

它的意思很直接：创建一个类，并且创建一个这个类的对象。这个就是 `object` 的意思：对象。

在代码中如果要使用这个对象，直接通过它的类名就可以访问：

```kotlin
🏝️
Sample.name
```

Kotlin

这不就是单例么，所以在 Kotlin 中创建单例不用像 Java 中那么复杂，只需要把 `class` 换成 `object` 就可以了。

- 单例类

  我们看一个单例的例子，分别用 Java 和 Kotlin 实现：

  - Java 中实现单例类（非线程安全）：

    ```java
    ☕️
    public class A {
        private static A sInstance;
        
        public static A getInstance() {
            if (sInstance == null) {
                sInstance = new A();
            }
            return sInstance;
        }
    
        // 👇还有很多模板代码
        ...
    }
    ```

    Java

    可以看到 Java 中为了实现单例类写了大量的模版代码，稍显繁琐。

  - Kotlin 中实现单例类：

    ```kotlin
     🏝️
    // 👇 class 替换成了 object
    object A {
        val number: Int = 1
        fun method() {
            println("A.method()")
        }
    }    
    ```

    Kotlin

    和 Java 相比的不同点有：

    - 和类的定义类似，但是把 `class` 换成了 `object` 。
    - 不需要额外维护一个实例变量 `sInstance`。
    - 不需要「保证实例只创建一次」的 `getInstance()` 方法。

    相比 Java 的实现简单多了。

    > 这种通过 `object` 实现的单例是一个饿汉式的单例，并且实现了线程安全。

- 继承类和实现接口

  Kotlin 中不仅类可以继承别的类，可以实现接口，`object` 也可以：

  ```kotlin
  🏝️
  open class A {
      open fun method() {
          ...
      }
  }
  
  interface B {
      fun interfaceMethod()
  }
    👇      👇   👇
  object C : A(), B {
  
      override fun method() {
          ...
      }
  
      override fun interfaceMethod() {
          ...
      }
  }
  ```

  Kotlin

  为什么 object 可以实现接口呢？简单来讲 object 其实是把两步合并成了一步，既有 class 关键字的功能，又实现了单例，这样就容易理解了。

- 匿名类

  另外，Kotlin 还可以创建 Java 中的匿名类，只是写法上有点不同：

  - Java：

    ```java
    ☕️                                              👇 
    ViewPager.SimpleOnPageChangeListener listener = new ViewPager.SimpleOnPageChangeListener() {
        @Override // 👈
        public void onPageSelected(int position) {
            // override
        }
    };
    ```

    Java

  - Kotlin：

    ```kotlin
    🏝️          
    val listener = object: ViewPager.SimpleOnPageChangeListener() {
        override fun onPageSelected(position: Int) {
            // override
        }
    }        
    ```

    Kotlin

    和 Java 创建匿名类的方式很相似，只不过把 `new` 换成了 `object:`：

    - Java 中 `new` 用来创建一个匿名类的对象
    - Kotlin 中 `object:` 也可以用来创建匿名类的对象

    这里的 `new ` 和 `object:` 修饰的都是接口或者抽象类。

### `companion object`

用 `object` 修饰的对象中的变量和函数都是静态的，但有时候，我们只想让类中的一部分函数和变量是静态的该怎么做呢：

```kotlin
🏝️
class A {
          👇
    object B {
        var c: Int = 0
    }
}
```

Kotlin

如上，可以在类中创建一个对象，把需要静态的变量或函数放在内部对象 B 中，外部可以通过如下的方式调用该静态变量：

```kotlin
🏝️
A.B.c
  👆
```

Kotlin

类中嵌套的对象可以用 `companion` 修饰：

```kotlin
🏝️
class A {
       👇
    companion object B {
        var c: Int = 0
    }
}
```

Kotlin

`companion` 可以理解为伴随、伴生，表示修饰的对象和外部类绑定。

但这里有一个小限制：一个类中最多只可以有一个伴生对象，但可以有多个嵌套对象。就像皇帝后宫佳丽三千，但皇后只有一个。

这样的好处是调用的时候可以省掉对象名：

```kotlin
🏝️
A.c // 👈 B 没了
```

Kotlin

所以，当有 `companion` 修饰时，对象的名字也可以省略掉：

```kotlin
🏝️
class A {
                // 👇 B 没了
    companion object {
        var c: Int = 0
    }
}
```

Kotlin

这就是这节最开始讲到的，Java 静态变量和方法的等价写法：`companion object` 变量和函数。

- 静态初始化

  Java 中的静态变量和方法，在 Kotlin 中都放在了 `companion object` 中。因此 Java 中的静态初始化在 Kotlin 中自然也是放在 `companion object` 中的，像类的初始化代码一样，由 `init` 和一对大括号表示：

  ```kotlin
  🏝️
  class Sample {
         👇
      companion object {
           👇
          init {
              ...
          }
      }
  }
  ```

  Kotlin

### top-level property / function 声明

除了静态函数这种简便的调用方式，Kotlin 还有更方便的东西：「`top-level declaration` 顶层声明」。其实就是把属性和函数的声明不写在 `class` 里面，这个在 Kotlin 里是允许的：

```kotlin
🏝️
package com.hencoder.plus

// 👇 属于 package，不在 class/object 内
fun topLevelFuncion() {
}
```

Kotlin

这样写的属性和函数，不属于任何 `class`，而是直接属于 `package`，它和静态变量、静态函数一样是全局的，但用起来更方便：你在其它地方用的时候，就连类名都不用写：

```kotlin
🏝️
import com.hencoder.plus.topLevelFunction // 👈 直接 import 函数

topLevelFunction()
```

Kotlin

写在顶级的函数或者变量有个好处：在 Android Studio 中写代码时，IDE 很容易根据你写的函数前几个字母自动联想出相应的函数。这样提高了写代码的效率，而且可以减少项目中的重复代码。

- 命名相同的顶级函数

  顶级函数不写在类中可能有一个问题：如果在不同文件中声明命名相同的函数，使用的时候会不会混淆？来看一个例子：

  - 在 `org.kotlinmaster.library` 包下有一个函数 method：

    ```kotlin
    🏝️
    package org.kotlinmaster.library1
                               👆
    fun method() {
        println("library1 method()")
    }
    ```

    Kotlin

  - 在 `org.kotlinmaster.library2` 包下有一个同名函数：

    ```kotlin
    🏝️
    package org.kotlinmaster.library2
                               👆
    fun method() {
        println("library2 method()")
    }
    ```

    Kotlin

  在使用的时候如果同时调用这两个同名函数会怎么样：

  ```kotlin
  🏝️
  import org.kotlinmaster.library1.method
                             👆
  fun test() {
      method()
                         👇
      org.kotlinmaster.library2.method()
  }
  ```

  Kotlin

  可以看到当出现两个同名顶级函数时，IDE 会自动加上包前缀来区分，这也印证了「顶级函数属于包」的特性。

### 对比

那在实际使用中，在 `object`、`companion object` 和 top-level 中该选择哪一个呢？简单来说按照下面这两个原则判断：

- 如果想写工具类的功能，直接创建文件，写 top-level「顶层」函数。
- 如果需要继承别的类或者实现接口，就用 `object` 或 `companion object`。

2020 7.20 17:15
