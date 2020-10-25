https://blog.csdn.net/weixin_44551646/article/details/94295677?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf

- Collection是**单列集合**的根接口，主要用于存储一系列符合某种规则的元素，它有两个重要的子接口List和Set。
- List接口的特点是**元素有序可重复**
- Set接口的特点是**元素无序，不可重复**
- ArrayList和LinkedList是List接口的**实现类**
- HashSet和TreeSet是Set接口的**实现类**

------

- Map是**双列集合**的根接口，特用蓝色标注，用于存储具有键（Key），值（Value）映射关系的元素，每个元素都包含一个键-值对，在使用该集合时可以通过指定的键找的对应的值。
- **Map为独立接口**
- HashMap和TreeMap是Map接口的实现类

# List集合

List集合为列表类型，以线性方式存储对象。List集合中的元素允许重复，各元素的顺序就是对象插入的顺序。用户可以通过使用**索引**来访问List集合中的元素。

**List集合的特点就是存取有序，可以存储重复的元素，可以用下标进行元素的操作**

## ArrayList集合

ArrayList支持可随需要而增长的动态数组。在Java数组中，长度是固定的，因此在数组被创建后，不能修改长度，这意味着开发者需要实现知道数组的长度。但在一般情况下，只有在运行时才知道数组长度。为了解决这个问题，ArrayList因此而生。底层是使用**数组**实现，所以**查询速度快，增删速度慢**

## LinkedList集合

LinkedList:是基于链表结构实现的，所以**查询速度慢，增删速度快**，提供了特殊的方法，对头尾的元素操作（进行增删查）

## ArrayList与LinkedList

- ArrayList和LinkedList顾名思义，ArrayList是Array(**动态数组**)的数据结构，相当于动态数组；LinkedList是Link(**链表**)的双向数据结构，**也可当作堆栈、队列、双端队列**。
- 对于随机访问List时（get和set操作），**ArrayList比LinkedList的效率更高**，因为LinkedList是线性的数据存储方式，所以需要移动指针从前往后依次查找。
- 对于新增和删除操作add和remove，**LinedList比较占优势**，因为ArrayList要移动数据（可以在上述ArrayList代码中体现）。
- 两者缺点都为线性不安全

**ArrayList和LinkedList线程不安全，在多线程中不建议使用。**

# Set集合

- Set集合中的对象**不按特定的方式排序**，只是简单的将对象加入到集合中，但是Set集合**不能包括重复对象**
- Set接口继承了Collection接口，因此也包含Collection接口的所有方法

HashSet**不允许重复，无序**。



# HashMap

https://blog.csdn.net/zhengwangzw/article/details/104889549?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf

看这个！！

 1.8还有三点主要的优化：

1. 数组+链表改成了数组+链表或红黑树；
2. 链表的插入方式从头插法改成了尾插法，简单说就是插入时，如果数组位置上已经有元素，1.7将新元素放到数组中，原始节点作为新节点的后继节点，1.8遍历链表，将元素放置到链表的最后；
3. 扩容的时候1.7需要对原数组中的元素进行重新hash定位在新数组的位置，1.8采用更简单的判断逻辑，位置不变或索引+旧容量大小；
4. 在插入时，1.7先判断是否需要扩容，再插入，1.8先进行插入，插入完成再判断是否需要扩容；



## 线程安全

HashTable是直接在操作方法上加synchronized关键字，锁住整个数组，粒度比较大，Collections.synchronizedMap是使用Collections集合工具的内部类，通过传入Map封装出一个SynchronizedMap对象，内部定义了一个对象锁，方法内通过对象锁实现；ConcurrentHashMap使用分段锁，降低了锁粒度，让并发度大大提高。



## ConcurrentHashMap

ConcurrentHashMap是由Segment数组结构和HashEntry数组结构组成。Segment是一种可重入锁ReentrantLock，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值对数据。

一个ConcurrentHashMap里包含一个Segment数组，Segment的结构和HashMap类似，是一种数组和链表结构， 一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素， 每个Segment守护者一个HashEntry数组里的元素,当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。

2020 10.8 21.12
