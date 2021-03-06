### 基本类型

在 Kotlin 中，所有东西都是对象，Kotlin 中使用的基本类型有：数字、字符、布尔值、数组与字符串。

```kotlin
🏝️
var number: Int = 1 // 👈还有 Double Float Long Short Byte 都类似
var c: Char = 'c'
var b: Boolean = true
var array: IntArray = intArrayOf(1, 2) // 👈类似的还有 FloatArray DoubleArray CharArray 等，intArrayOf 是 Kotlin 的 built-in 函数
var str: String = "string"
```

Kotlin

这里有两个地方和 Java 不太一样：

- Kotlin 里的 Int 和 Java 里的 int 以及 Integer 不同，主要是在装箱方面不同。

  Java 里的 int 是 unbox 的，而 Integer 是 box 的：

  ```java
  ☕️
  int a = 1;
  Integer b = 2; // 👈会被自动装箱 autoboxing
  ```

  Java

  Kotlin 里，Int 是否装箱根据场合来定：

  ```kotlin
  🏝️
  var a: Int = 1 // unbox
  var b: Int? = 2 // box
  var list: List<Int> = listOf(1, 2) // box
  ```

  Kotlin

  Kotlin 在语言层面简化了 Java 中的 int 和 Integer，但是我们对是否装箱的场景还是要有一个概念，因为这个牵涉到程序运行时的性能开销。

  因此在日常的使用中，对于 Int 这样的基本类型，尽量用不可空变量。

- Java 中的数组和 Kotlin 中的数组的写法也有区别：

  ```java
  ☕️
  int[] array = new int[] {1, 2};
  ```

  Java

  而在 Kotlin 里，上面的写法是这样的：

  ```kotlin
  🏝️
  var array: IntArray = intArrayOf(1, 2)
  // 👆这种也是 unbox 的
  ```

  Kotlin

简单来说，原先在 Java 里的基本类型，类比到 Kotlin 里面，条件满足如下之一就不装箱：

- 不可空类型。
- 使用 IntArray、FloatArray 等。

 - ### 属性的 getter/setter 函数

   我们知道，在 Java 里面的 field 经常会带有 getter/setter 函数：

   ```java
   ☕️
   public class User {
       String name;
       public String getName() {
           return this.name;
       }
       public void setName(String name) {
           this.name = name;
       }
   }
   ```

   Java

   它们的作用就是可以自定义函数内部实现来达到「钩子」的效果，比如下面这种：

   ```java
   ☕️
   public class User {
       String name;
       public String getName() {
           return this.name + " nb";
       }
       public void setName(String name) {
           this.name = "Cute " + name;
       }
   }
   ```

   Java

   在 Kotlin 里，这种 getter / setter 是怎么运作的呢？

   ```kotlin
   🏝️
   class User {
       var name = "Mike"
       fun run() {
           name = "Mary"
           // 👆的写法实际上是👇这么调用的
           // setName("Mary")
           // 建议自己试试，IDE 的代码补全功能会在你打出 setn 的时候直接提示 name 而不是 setName
           
           println(name)
           // 👆的写法实际上是👇这么调用的
           // print(getName())
           // IDE 的代码补全功能会在你打出 getn 的时候直接提示 name 而不是 getName
       }
   }
   ```

   Kotlin

   那么我们如何来操作前面提到的「钩子」呢？看下面这段代码：

   ```kotlin
   🏝️
   class User {
       var name = "Mike"
           👇
           get() {
               return field + " nb"
           }
           👇   👇 
           set(value) {
               field = "Cute " + value
           }
   }
   ```

   Kotlin

   格式上和 Java 有一些区别：

   - getter / setter 函数有了专门的关键字 get 和 set
   - getter / setter 函数位于 var 所声明的变量下面
   - setter 函数参数是 value

   除此之外还多了一个叫 field 的东西。这个东西叫做「**Backing Field**」，中文翻译是**幕后字段**或**后备字段**（马云背后的女人😝）。具体来说，你的这个代码：

   ```kotlin
   🏝️
   class Kotlin {
     var name = "kaixue.io"
   }
   ```

   Kotlin

   在编译后的字节码大致等价于这样的 Java 代码：

   ```java
   ☕️
   public final class Kotlin {
      @NotNull
      private String name = "kaixue.io";
   
      @NotNull
      public final String getName() {
         return this.name;
      }
   
      public final void setName(@NotNull String name) {
         this.name = name;
      }
   }
   ```

   Java

   上面的那个 `String name` 就是 Kotlin 帮我们自动创建的一个 Java field。这个 field 对编码的人不可见，但会自动应用于 getter 和 setter，因此它被命名为「Backing Field」（backing 的意思是在背后进行支持，例如你闯了大祸，我动用能量来保住你的人头，我就是在 back you）。

   所以，虽然 Kotlin 的这个 `field` 本质上确实是一个 Java 中的 field，但对于 Kotlin 的语法来讲，它和 Java 里面的 field 完全不是一个概念。在 Kotlin 里，它相当于每一个 var 内部的一个变量。

   我们前面讲过 val 是只读变量，只读的意思就是说 val 声明的变量不能进行重新赋值，也就是说不能调用 setter 函数，因此，val 声明的变量是不能重写 setter 函数的，但它可以重写 getter 函数：

   ```kotlin
   🏝️
   val name = "Mike"
       get() {
           return field + " nb"
       }
   ```

   Kotlin

   val 所声明的只读变量，在取值的时候仍然可能被修改，这也是和 Java 里的 final 的不同之处。

   关于「钩子」的作用，除了修改取值和赋值，也可以加一些自己的逻辑，就像我们在 Activity 的生命周期函数里做的事情一样。

### 类型的判断和强转

刚才讲的实例化的例子中，我们实际上是把子类对象赋值给父类的变量，这个概念在 Java 里叫多态，Kotlin 也有这个特性，但在实际工作中我们很可能会遇到需要使用子类才有的函数。

比如我们先在子类中定义一个函数：

```kotlin
🏝️
class NewActivity : MainActivity() {
    fun action() {}
}
```

Kotlin

那么接下来这么写是无法调用该函数的：

```kotlin
🏝️
fun main() {
    var activity: Activity = NewActivity()
    // 👆activity 是无法调用 NewActivity 的 action 方法的
}
```

Kotlin

在 Java 里，需要先使用 `instanceof` 关键字判断类型，再通过强转来调用：

```java
☕️
void main() {
    Activity activity = new NewActivity();
    if (activity instanceof NewActivity) {
        ((NewActivity) activity).action();
    }
}
```

Java

Kotlin 里同样有类似解决方案，使用 `is` 关键字进行「类型判断」，并且因为编译器能够进行类型推断，可以帮助我们省略强转的写法：

```kotlin
🏝️
fun main() {
    var activity: Activity = NewActivity()
    if (activity is NewActivity) {
        // 👇的强转由于类型推断被省略了
        activity.action()
    }
}
```

Kotlin

那么能不能不进行类型判断，直接进行强转调用呢？可以使用 `as` 关键字：

```kotlin
🏝️
fun main() {
    var activity: Activity = NewActivity()
    (activity as NewActivity).action()
}
```

Kotlin

这种写法如果强转类型操作是正确的当然没问题，但如果强转成一个错误的类型，程序就会抛出一个异常。

我们更希望能进行安全的强转，可以更优雅地处理强转出错的情况。

这一点，Kotlin 在设计上自然也考虑到了，我们可以使用 `as?` 来解决：

```kotlin
🏝️
fun main() {
    var activity: Activity = NewActivity()
    // 👇'(activity as? NewActivity)' 之后是一个可空类型的对象，所以，需要使用 '?.' 来调用
    (activity as? NewActivity)?.action()
}
```

Kotlin

它的意思就是说如果强转成功就执行之后的调用，如果强转不成功就不执行。

  2020 7.20 16:44
