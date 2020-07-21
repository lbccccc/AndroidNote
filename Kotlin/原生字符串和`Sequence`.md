### raw string (原生字符串)

有时候我们不希望写过多的转义字符，这种情况 Kotlin 通过「原生字符串」来实现。

用法就是使用一对 `"""` 将字符串括起来：

```kotlin
🏝️
val name = "world"
val myName = "kotlin"
           👇
val text = """
      Hi $name!
    My name is $myName.\n
"""
println(text)
```

Kotlin

这里有几个注意点：

- `\n` 并不会被转义
- 最后输出的内容与写的内容完全一致，包括实际的换行
- `$` 符号引用变量仍然生效

这就是「原生字符串」。输出结果如下：

```
      Hi world!
    My name is kotlin.\n
      Hi world!
```

但对齐方式看起来不太优雅，原生字符串还可以通过 `trimMargin()` 函数去除每行前面的空格：

```kotlin
🏝️
val text = """
     👇 
      |Hi world!
    |My name is kotlin.
""".trimMargin()
println(text)
```

Kotlin

输出结果如下：

```
Hi world!
My name is kotlin.
```

这里的 `trimMargin()` 函数有以下几个注意点：

- `|` 符号为默认的边界前缀，前面只能有空格，否则不会生效
- 输出时 `|` 符号以及它前面的空格都会被删除
- 边界前缀还可以使用其他字符，比如 `trimMargin("/")`，只不过上方的代码使用的是参数默认值的调用方式

字符串的部分就先到这里，下面来看看数组与集合有哪些更方便的操作。

### `Sequence`

在上一期中我们已经熟悉了 `Sequence` 的基本概念，这次让我们更加深入地了解 `Sequence` 序列的使用方式。

序列 `Sequence` 又被称为「惰性集合操作」，关于什么是惰性，我们通过下面的例子来理解：

```kotlin
🏝️
val sequence = sequenceOf(1, 2, 3, 4)
val result: Sequence<Int> = sequence
    .map { i ->
        println("Map $i")
        i * 2 
    }
    .filter { i ->
        println("Filter $i")
        i % 3  == 0 
    }
👇
println(result.first()) // 👈 只取集合的第一个元素
```

Kotlin

- 惰性的概念首先就是说在「👇」标注之前的代码运行时不会立即执行，它只是定义了一个执行流程，只有 `result` 被使用到的时候才会执行

- 当「👇」的 `println` 执行时数据处理流程是这样的：

  - 取出元素 1 -> map 为 2 -> filter 判断 2 是否能被 3 整除
  - 取出元素 2 -> map 为 4 -> filter 判断 4 是否能被 3 整除
  - ...

  惰性指当出现满足条件的第一个元素的时候，`Sequence` 就不会执行后面的元素遍历了，即跳过了 `4` 的遍历。

而 `List` 是没有惰性的特性的：

```kotlin
🏝️
val list = listOf(1, 2, 3, 4)
val result: List = list
    .map { i ->
        println("Map $i")
        i * 2 
    }
    .filter { i ->
        println("Filter $i")
        i % 3  == 0 
    }
👇
println(result.first()) // 👈 只取集合的第一个元素
```

Kotlin

包括两点：

- 声明之后立即执行
- 数据处理流程如下：
  - {1, 2, 3, 4} -> {2, 4, 6, 8}
  - 遍历判断是否能被 3 整除

`Sequence` 这种类似懒加载的实现有下面这些优点：

- 一旦满足遍历退出的条件，就可以省略后续不必要的遍历过程。
- 像 `List` 这种实现 `Iterable` 接口的集合类，每调用一次函数就会生成一个新的 `Iterable`，下一个函数再基于新的 `Iterable` 执行，每次函数调用产生的临时 `Iterable` 会导致额外的内存消耗，而 `Sequence` 在整个流程中只有一个。

因此，`Sequence` 这种数据类型可以在数据量比较大或者数据量未知的时候，作为流式处理的解决方案。
