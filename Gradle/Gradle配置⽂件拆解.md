# Gradle 配置⽂件拆解

## gradle 是什么

- 是构建⼯具，不是语⾔ 
- 它⽤了 Groovy 这个语⾔，创造了⼀种 DSL，但它本身不是语⾔

## 怎么构建？

- 按照 gradle 的规则（build.gradle、settings.gradle、gradle-wrapper、gradle 语法） 闭包 相当于可以被传递的代码块

## 闭包

- 相当于可以被传递的代码块

## buildType 和 productFlavors

- buildType   各种版本(debug，release)
- productFlavors  自定义版本 (比如免费版，付费版)

## compile, implementation 和 api 

- implementation：不会传递依赖 
- compile / api：会传递依赖；api 是 compile 的替代品，效果完全等同 
- 当依赖被传递时，⼆级依赖的改动会导致 0 级项⽬重新编译；当依赖不传递时，⼆级依赖的改动 不会导致 0 级项⽬重新编译

# 项⽬结构

- 单 project：build.gradle 

- 多 project：由 settings.gradle 配置多个 

  查找 settings 的顺序： 

  1. 当前⽬录
  2. 兄弟⽬录 master 
  3. ⽗⽬录

## task

- 使⽤⽅法： ./gradlew taskName 

- task 的结构：

  ```
  task taskName {
   初始化代码
   doFirst {
   task 代码
   }
   doLast {
   task 代码
   }
  }
  ```

- doFirst() doLast() 和普通代码段的区别： 

  - 普通代码段：在 task 创建过程中就会被执⾏，发⽣在 configuration 阶段 
  - doFirst() 和 doLast()：在 task 执⾏过程中被执⾏，发⽣在 execution 阶段。如果⽤户没有 直接或间接执⾏ task，那么它的 doLast() doFirst() 代码不会被执⾏ 
  - doFirst() 和 doLast() 都是 task 代码，其中 doFirst() 是往队列的前⾯插⼊代码，doLast() 是往队列的后⾯插⼊代码
  - task 的依赖：可以使⽤ task taskA(dependsOn: b) 的形式来指定依赖。指定依赖后，task 会在⾃⼰执⾏前先执⾏⾃⼰依赖的 task。

## gradle 执⾏的⽣命周期

三个阶段：

- 初始化阶段：执⾏ settings.gradle，确定主 project 和⼦ project
- 定义阶段：执⾏每个 project 的 bulid.gradle，确定出所有 task 所组成的有向⽆环图 
- 执⾏阶段：按照上⼀阶段所确定出的有向⽆环图来执⾏指定的 task

在阶段之间插⼊代码：

- ⼀⼆阶段之间：

  - settings.gradle 的最后

- ⼆三阶段之间：

  ```
  afterEvaluate {
   插⼊代码
  }
  ```

  

2020 7.21 0:33
