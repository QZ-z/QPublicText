# 集合概述

![image-20240516104518761](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240516104518761.png)

## [说说 List, Set, Queue, Map 四者的区别？](https://javaguide.cn/java/collection/java-collection-questions-01.html#说说-list-set-queue-map-四者的区别)

- `List`(对付顺序的好帮手): 存储的元素是有序的、可重复的。
- `Set`(注重独一无二的性质): 存储的元素不可重复的。
- `Queue`(实现排队功能的叫号机): 按特定的排队规则来确定先后顺序，存储的元素是有序的、可重复的。
- `Map`(用 key 来搜索的专家): 使用键值对（key-value）存储，类似于数学上的函数 y=f(x)，"x" 代表 key，"y" 代表 value，key 是无序的、不可重复的，value 是无序的、可重复的，每个键最多映射到一个值。

## 底层数据结构总结

### [List](https://javaguide.cn/java/collection/java-collection-questions-01.html#list)

- `ArrayList`：`Object[]` 数组。详细可以查看：[ArrayList 源码分析](https://javaguide.cn/java/collection/arraylist-source-code.html)。
- `Vector`：`Object[]` 数组。
- `LinkedList`：双向链表(JDK1.6 之前为循环链表，JDK1.7 取消了循环)。详细可以查看：[LinkedList 源码分析](https://javaguide.cn/java/collection/linkedlist-source-code.html)。

### [Set](https://javaguide.cn/java/collection/java-collection-questions-01.html#set)

- `HashSet`(无序，唯一): 基于 `HashMap` 实现的，底层采用 `HashMap` 来保存元素。
- `LinkedHashSet`: `LinkedHashSet` 是 `HashSet` 的子类，并且其内部是通过 `LinkedHashMap` 来实现的。
- `TreeSet`(有序，唯一): 红黑树(自平衡的排序二叉树)。

### [Queue](https://javaguide.cn/java/collection/java-collection-questions-01.html#queue)

- `PriorityQueue`: `Object[]` 数组来实现小顶堆。详细可以查看：[PriorityQueue 源码分析](https://javaguide.cn/java/collection/priorityqueue-source-code.html)。
- `DelayQueue`:`PriorityQueue`。详细可以查看：[DelayQueue 源码分析](https://javaguide.cn/java/collection/delayqueue-source-code.html)。
- `ArrayDeque`: 可扩容动态双向数组。

### [Map](https://javaguide.cn/java/collection/java-collection-questions-01.html#map)

- `HashMap`：JDK1.8 之前 `HashMap` 由数组+链表组成的，数组是 `HashMap` 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）。JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。详细可以查看：[HashMap 源码分析](https://javaguide.cn/java/collection/hashmap-source-code.html)。
- `LinkedHashMap`：`LinkedHashMap` 继承自 `HashMap`，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，`LinkedHashMap` 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。详细可以查看：[LinkedHashMap 源码分析](https://javaguide.cn/java/collection/linkedhashmap-source-code.html)
- `Hashtable`：数组+链表组成的，数组是 `Hashtable` 的主体，链表则是主要为了解决哈希冲突而存在的，是线程安全的，已经被淘汰。
- `TreeMap`：红黑树（自平衡的排序二叉树）

## 为什么使用集合

与数组相比：

- 集合更灵活，有效地存储多个数据对象
- 各种集合和接口可以存储不同类型和数量的对象，有多样化的操作方法
- 大小可变、支持泛型、具有内建算法

# [List](https://javaguide.cn/java/collection/java-collection-questions-01.html#list-1)

## [ArrayList 和 Array（数组）的区别？](https://javaguide.cn/java/collection/java-collection-questions-01.html#arraylist-和-array-数组-的区别)

`ArrayList` 内部基于动态数组实现，比 `Array`（静态数组） 使用起来更加灵活：

- `ArrayList`会根据实际存储的元素动态地扩容或缩容，而 `Array` 被创建之后就不能改变它的长度了。
- `ArrayList` 允许你使用泛型来确保类型安全，`Array` 则不可以。
- `ArrayList` 中只能存储对象。对于基本类型数据，需要使用其对应的包装类（如 Integer、Double 等）。`Array` 可以直接存储基本类型数据，也可以存储对象。
- `ArrayList` 支持插入、删除、遍历等常见操作，并且提供了丰富的 API 操作方法，比如 `add()`、`remove()`等。`Array` 只是一个固定长度的数组，只能按照下标访问其中的元素，不具备动态添加、删除元素的能力。
- `ArrayList`创建时不需要指定大小，而`Array`创建时必须指定大小

## [ArrayList 和 Vector 的区别?（了解即可）](https://javaguide.cn/java/collection/java-collection-questions-01.html#arraylist-和-vector-的区别-了解即可)

- `ArrayList` 是 `List` 的主要实现类，底层使用 `Object[]`存储，适用于频繁的查找工作，线程不安全 。
- `Vector` 是 `List` 的古老实现类，底层使用`Object[]` 存储，线程安全。

## [Vector 和 Stack 的区别?（了解即可）](https://javaguide.cn/java/collection/java-collection-questions-01.html#vector-和-stack-的区别-了解即可)

- `Vector` 和 `Stack` 两者都是线程安全的，都是使用 `synchronized` 关键字进行同步处理。
- `Stack` 继承自 `Vector`，是一个后进先出的栈，而 `Vector` 是一个列表。

随着 Java 并发编程的发展，`Vector` 和 `Stack` 已经被淘汰，推荐使用并发集合类（例如 `ConcurrentHashMap`、`CopyOnWriteArrayList` 等）或者手动实现线程安全的方法来提供安全的多线程操作支持。

## [ArrayList 可以添加 null 值吗？](https://javaguide.cn/java/collection/java-collection-questions-01.html#arraylist-可以添加-null-值吗)

`ArrayList` 中可以存储任何类型的对象，包括 `null` 值。不过，不建议向`ArrayList` 中添加 `null` 值， `null` 值无意义，会让代码难以维护比如忘记做判空处理就会导致空指针异常。

## [ArrayList 插入和删除元素的时间复杂度？](https://javaguide.cn/java/collection/java-collection-questions-01.html#arraylist-插入和删除元素的时间复杂度)

对于插入：

- 头部插入：由于需要将所有元素都依次向后移动一个位置，因此时间复杂度是 O(n)。
- 尾部插入：
  - 当 `ArrayList` 的容量未达到极限时，往列表末尾插入元素的时间复杂度是 O(1)，因为它只需要在数组末尾添加一个元素即可；
  - 当容量已达到极限并且需要扩容时，则需要执行一次 O(n) 的操作将原数组复制到新的更大的数组中，然后再执行 O(1) 的操作添加元素。
- 指定位置插入：需要将目标位置之后的所有元素都向后移动一个位置，然后再把新元素放入指定位置。这个过程需要移动平均 n/2 个元素，因此时间复杂度为 O(n)。

对于删除：

- 头部删除：由于需要将所有元素依次向前移动一个位置，因此时间复杂度是 O(n)。
- 尾部删除：当删除的元素位于列表末尾时，时间复杂度为 O(1)。
- 指定位置删除：需要将目标元素之后的所有元素向前移动一个位置以填补被删除的空白位置，因此需要移动平均 n/2 个元素，时间复杂度为 O(n)。

## [LinkedList 插入和删除元素的时间复杂度？](https://javaguide.cn/java/collection/java-collection-questions-01.html#linkedlist-插入和删除元素的时间复杂度)

- 头部插入/删除：只需要修改头结点的指针即可完成插入/删除操作，因此时间复杂度为 O(1)。
- 尾部插入/删除：只需要修改尾结点的指针即可完成插入/删除操作，因此时间复杂度为 O(1)。
- 指定位置插入/删除：需要先移动到指定位置，再修改指定节点的指针完成插入/删除，因此需要遍历平均 n/2 个元素，时间复杂度为 O(n)。

## [LinkedList 为什么不能实现 RandomAccess 接口？](https://javaguide.cn/java/collection/java-collection-questions-01.html#linkedlist-为什么不能实现-randomaccess-接口)

`RandomAccess` 是一个标记接口，用来表明实现该接口的类支持随机访问（即可以通过索引快速访问元素）。

由于 `LinkedList` 底层数据结构是链表，内存地址不连续，只能通过指针来定位，不支持随机快速访问，所以不能实现 `RandomAccess` 接口。

## [ArrayList 与 LinkedList 区别?](https://javaguide.cn/java/collection/java-collection-questions-01.html#arraylist-与-linkedlist-区别)

- **是否保证线程安全：** `ArrayList` 和 `LinkedList` 都是不同步的，也就是不保证线程安全；
- **底层数据结构：** `ArrayList` 底层使用的是 **`Object` 数组**；`LinkedList` 底层使用的是 **双向链表** 数据结构（JDK1.6 之前为循环链表，JDK1.7 取消了循环。）
- 插入和删除是否受元素位置的影响：
  - `ArrayList` 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。 
    - 比如：执行`add(E e)`方法的时候， `ArrayList` 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是 O(1)。
    - 但是如果要在指定位置 i 插入和删除元素的话（`add(int index, E element)`），时间复杂度就为 O(n)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。
  - `LinkedList` 采用链表存储，
    - 所以在头尾插入或者删除元素不受元素位置的影响（`add(E e)`、`addFirst(E e)`、`addLast(E e)`、`removeFirst()`、 `removeLast()`），时间复杂度为 O(1)，
    - 如果是要在指定位置 `i` 插入和删除元素的话（`add(int index, E element)`，`remove(Object o)`,`remove(int index)`）， 时间复杂度为 O(n) ，因为需要先移动到指定位置再插入和删除。
- **是否支持快速随机访问：** `LinkedList` 不支持高效的随机元素访问，而 `ArrayList`（实现了 `RandomAccess` 接口） 支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于`get(int index)`方法)。
- **内存空间占用：** `ArrayList` 的空间浪费主要体现在在 list 列表的结尾会预留一定的容量空间，而 LinkedList 的空间花费则体现在它的每一个元素都需要消耗比 ArrayList 更多的空间（因为要存放直接后继和直接前驱以及数据）。

> `LinkedList`不怎么用，删除只有头尾是$O(1)$，中间依旧是$O(n)$

## [说一说 ArrayList 的扩容机制吧](https://javaguide.cn/java/collection/java-collection-questions-01.html#说一说-arraylist-的扩容机制吧)

详见笔主的这篇文章: [ArrayList 扩容机制分析](https://javaguide.cn/java/collection/arraylist-source-code.html#_3-1-先从-arraylist-的构造函数说起)。

构造方法：

- 默认构造函数（无参构造），使用初始容量10构造一个空列表

- 带初始容量参数的构造函数（用户指定） 

- 构造包含指定collection元素的列表，这些元素利用该集合的迭代器按顺序返回，如果指定的集合为null，throws NullPointerException

> **以无参数构造方法创建 `ArrayList` 时，实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量。即向数组中添加第一个元素时，数组容量扩为 10**

**`int newCapacity = oldCapacity + (oldCapacity >> 1)`,所以 ArrayList 每次扩容之后容量都会变为原来的 1.5 倍左右（oldCapacity 为偶数就是 1.5 倍，否则是 1.5 倍左右）**

如果新容量仍然小于最小需求容量 `minCapacity`，则将新容量设置为 `minCapacity`

如果新容量超过 `MAX_ARRAY_SIZE`，则调用 `hugeCapacity` 方法进行处理，比较 `minCapacity `和 `MAX_ARRAY_SIZE`，如果`minCapacity`大于最大容量，则新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 `MAX_ARRAY_SIZE `即为 `Integer.MAX_VALUE - 8`。

## [`System.arraycopy()` 和 `Arrays.copyOf()`方法](https://javaguide.cn/java/collection/arraylist-source-code.html#system-arraycopy-和-arrays-copyof-方法)

**联系：**

看两者源代码可以发现 `copyOf()`内部实际调用了 `System.arraycopy()` 方法

**区别：**

`arraycopy()` 需要目标数组，将原数组拷贝到你自己定义的数组里或者原数组，而且可以选择拷贝的起点和长度以及放入新数组中的位置

 `copyOf()` 是系统自动在内部新建一个数组，并返回该数组

```java
/**
    *   复制数组
    * @param src 源数组
    * @param srcPos 源数组中的起始位置
    * @param dest 目标数组
    * @param destPos 目标数组中的起始位置
    * @param length 要复制的数组元素的数量
    */
public static native void arraycopy(Object src,  int  srcPos,
                                    Object dest, int destPos,
                                    int length);

public static int[] copyOf(int[] original, int newLength) {
    // 申请一个新的数组
    int[] copy = new int[newLength];
    // 调用System.arraycopy,将源数组中的数据进行拷贝,并返回新的数组
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
```

## [`ensureCapacity`方法](https://javaguide.cn/java/collection/arraylist-source-code.html#ensurecapacity方法)

理论上来说，最好在向 `ArrayList` 添加大量元素之前用 `ensureCapacity` 方法，以减少增量重新分配的次数

# [Set](https://javaguide.cn/java/collection/java-collection-questions-01.html#set-1)

## [Comparable 和 Comparator 的区别](https://javaguide.cn/java/collection/java-collection-questions-01.html#comparable-和-comparator-的区别)

`Comparable` 接口和 `Comparator` 接口都是 Java 中用于排序的接口，它们在实现类对象之间比较大小、排序等方面发挥了重要作用：

- `Comparable` 接口实际上是出自`java.lang`包 它有一个 `compareTo(Object obj)`方法用来排序
- `Comparator`接口实际上是出自 `java.util` 包它有一个`compare(Object obj1, Object obj2)`方法用来排序

## [无序性和不可重复性的含义是什么](https://javaguide.cn/java/collection/java-collection-questions-01.html#无序性和不可重复性的含义是什么)

- 无序性不等于随机性 ，无序性是指存储的数据在底层数组中并非按照数组索引的顺序添加 ，而是根据数据的哈希值决定的。
- 不可重复性是指添加的元素按照 `equals()` 判断时 ，返回 false，需要同时重写 `equals()` 方法和 `hashCode()` 方法。

## [比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同](https://javaguide.cn/java/collection/java-collection-questions-01.html#比较-hashset、linkedhashset-和-treeset-三者的异同)

- `HashSet`、`LinkedHashSet` 和 `TreeSet` 都是 `Set` 接口的实现类，都能保证元素唯一，并且都不是线程安全的。
- `HashSet`、`LinkedHashSet` 和 `TreeSet` 的主要区别在于底层数据结构不同。
  - `HashSet` 的底层数据结构是哈希表（基于 `HashMap` 实现）。	
  - `LinkedHashSet` 的底层数据结构是链表和哈希表，元素的插入和取出顺序满足 FIFO。
  - `TreeSet` 底层数据结构是红黑树，元素是有序的，排序的方式有自然排序和定制排序。
- 底层数据结构不同又导致这三者的应用场景不同。
  - `HashSet` 用于不需要保证元素插入和取出顺序的场景，
  - `LinkedHashSet` 用于保证元素的插入和取出顺序满足 FIFO 的场景，
  - `TreeSet` 用于支持对元素自定义排序规则的场景。

# [Queue](https://javaguide.cn/java/collection/java-collection-questions-01.html#queue-1)

## [Queue 与 Deque 的区别](https://javaguide.cn/java/collection/java-collection-questions-01.html#queue-与-deque-的区别)

`Queue` 是单端队列，只能从一端插入元素，另一端删除元素，实现上一般遵循 **先进先出（FIFO）** 规则。`Queue` 扩展了 `Collection` 的接口

`Deque` 是双端队列，在队列的两端均可以插入或删除元素。`Deque` 扩展了 `Queue` 的接口

两者都有两类方法，因为容量问题导致操作失败之后有两种返回：

- 抛出异常
- 返回特殊值

`Deque` 还提供有 `push()` 和 `pop()` 等其他方法，可用于模拟栈

## [ArrayDeque 与 LinkedList 的区别](https://javaguide.cn/java/collection/java-collection-questions-01.html#arraydeque-与-linkedlist-的区别)

`ArrayDeque` 和 `LinkedList` 都实现了 `Deque` 接口，两者都具有队列的功能，但两者有什么区别呢？

- `ArrayDeque` 是基于可变长的数组和双指针来实现，而 `LinkedList` 则通过链表来实现。
- `ArrayDeque` 不支持存储 `NULL` 数据，但 `LinkedList` 支持。
- `ArrayDeque` 是在 JDK1.6 才被引入的，而`LinkedList` 早在 JDK1.2 时就已经存在。
- `ArrayDeque` 插入时可能存在扩容过程, 不过均摊后的插入操作依然为 O(1)。虽然 `LinkedList` 不需要扩容，但是每次插入数据时均需要申请新的堆空间，均摊性能相比更慢。

- `ArrayDeque` 也可以用于实现栈

- `ArrayDeque`的数组长度是2的幂次方

> 扩展阅读：[ArrayDeque原理详解](https://juejin.cn/post/6973280919918477320)

## [说一说 PriorityQueue](https://javaguide.cn/java/collection/java-collection-questions-01.html#说一说-priorityqueue)

`PriorityQueue` 是在 JDK1.5 中被引入的, 其与 `Queue` 的区别在于元素出队顺序是与优先级相关的，即总是优先级最高的元素先出队。

这里列举其相关的一些要点：

- `PriorityQueue` 利用了二叉堆的数据结构来实现的，底层使用可变长的数组来存储数据
- `PriorityQueue` 通过堆元素的上浮和下沉，实现了在 O(logn) 的时间复杂度内插入元素和删除堆顶元素。
- `PriorityQueue` 是非线程安全的，且不支持存储 `NULL` 和 `non-comparable` 的对象。
- `PriorityQueue` 默认是小顶堆，但可以接收一个 `Comparator` 作为构造参数，从而来自定义元素优先级的先后。

## [什么是 BlockingQueue？](https://javaguide.cn/java/collection/java-collection-questions-01.html#什么是-blockingqueue)

`BlockingQueue` （阻塞队列）是一个接口，继承自 `Queue`。

- `BlockingQueue`阻塞的原因是其支持当队列没有元素时一直阻塞，直到有元素；
- 还支持如果队列已满，一直等到队列可以放入新元素时再放入

常用于生产者消费者模型

## [BlockingQueue 的实现类有哪些？](https://javaguide.cn/java/collection/java-collection-questions-01.html#blockingqueue-的实现类有哪些)

![image-20240516111944843](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240516111944843.png)

1. `ArrayBlockingQueue`：使用数组实现的有界阻塞队列。在创建时需要指定容量大小，并支持公平和非公平两种方式的锁访问机制。
2. `LinkedBlockingQueue`：使用单向链表实现的可选有界阻塞队列。在创建时可以指定容量大小，如果不指定则默认为`Integer.MAX_VALUE`。和`ArrayBlockingQueue`不同的是， 它仅支持非公平的锁访问机制。
3. `PriorityBlockingQueue`：支持优先级排序的无界阻塞队列。元素必须实现`Comparable`接口或者在构造函数中传入`Comparator`对象，并且不能插入 null 元素。
4. `SynchronousQueue`：同步队列，是一种不存储元素的阻塞队列。每个插入操作都必须等待对应的删除操作，反之删除操作也必须等待插入操作。因此，`SynchronousQueue`通常用于线程之间的直接传递数据。
5. `DelayQueue`：延迟队列，其中的元素只有到了其指定的延迟时间，才能够从队列中出队

> 开发中用的都不多，了解就行。`LinkedBlockingQueue` 、`SynchronousQueue`和线程池有关

## [ArrayBlockingQueue 和 LinkedBlockingQueue 有什么区别？](https://javaguide.cn/java/collection/java-collection-questions-01.html#arrayblockingqueue-和-linkedblockingqueue-有什么区别)

`ArrayBlockingQueue` 和 `LinkedBlockingQueue` 是 Java 并发包中常用的两种阻塞队列实现，它们都是线程安全的。不过，不过它们之间也存在下面这些区别：

- 底层实现：`ArrayBlockingQueue` 基于数组实现，而 `LinkedBlockingQueue` 基于链表实现。
- 是否有界：`ArrayBlockingQueue` 是有界队列，必须在创建时指定容量大小。`LinkedBlockingQueue` 创建时可以不指定容量大小，默认是`Integer.MAX_VALUE`，也就是无界的。但也可以指定队列大小，从而成为有界的。
- 锁是否分离： `ArrayBlockingQueue`中的锁是没有分离的，即生产和消费用的是同一个锁；`LinkedBlockingQueue`中的锁是分离的，即生产用的是`putLock`，消费是`takeLock`，这样可以防止生产者和消费者线程之间的锁争夺。
- 锁的公平性：`ArrayBlockingQueue`支持公平和非公平锁，`LinkedBlockingQueue`仅支持非公平锁
- 内存占用：`ArrayBlockingQueue` 需要提前分配数组内存，而 `LinkedBlockingQueue` 则是动态分配链表节点内存。这意味着，`ArrayBlockingQueue` 在创建时就会占用一定的内存空间，且往往申请的内存比实际所用的内存更大，而`LinkedBlockingQueue` 则是根据元素的增加而逐渐占用内存空间。

# [Map（重要）](https://javaguide.cn/java/collection/java-collection-questions-02.html#map-重要)

## [HashMap 和 Hashtable 的区别](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-和-hashtable-的区别)

- **线程是否安全：** 
  - `HashMap` 是非线程安全的，
  - `Hashtable` 是线程安全的,因为 `Hashtable` 内部的方法基本都经过`synchronized` 修饰。（如果你要保证线程安全的话就使用 `ConcurrentHashMap` 吧！）；
- **效率：** 
  - 因为线程安全的问题，`HashMap` 要比 `Hashtable` 效率高一点。
  - 另外，`Hashtable` 基本被淘汰，不要在代码中使用它；
- **对 Null key 和 Null value 的支持：** 
  - `HashMap` 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个；
  - Hashtable 不允许有 null 键和 null 值，否则会抛出 `NullPointerException`。
- **初始容量大小和每次扩充容量大小的不同：**
  -  ① 创建时如果不指定容量初始值，`Hashtable` 默认的初始大小为 11，之后每次扩充，容量变为原来的 2n+1。`HashMap` 默认的初始化大小为 16。之后每次扩充，容量变为原来的 2 倍。
  - ② 创建时如果给定了容量初始值，那么 `Hashtable` 会直接使用你给定的大小，而 `HashMap` 会将其扩充为 2 的幂次方大小（`HashMap` 中的`tableSizeFor()`方法保证）。也就是说 `HashMap` 总是使用 2 的幂作为哈希表的大小,后面会介绍到为什么是 2 的幂次方。
- **底层数据结构：** 
  - JDK1.8 以后的 `HashMap` 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树），以减少搜索时间（后文中我会结合源码对这一过程进行分析）。
  - `Hashtable` 没有这样的机制

## [HashMap 和 HashSet 区别](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-和-hashset-区别)

`HashSet` 底层就是基于 `HashMap` 实现的，只有`clone()`、`writeObject()`、`readObject()`是自己实现的

## [HashMap 和 TreeMap 区别](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-和-treemap-区别)

`TreeMap` 和`HashMap` 都继承自`AbstractMap` 

但是需要注意的是`TreeMap`它还实现了`NavigableMap`接口和`SortedMap` 接口

实现 `NavigableMap` 接口让 `TreeMap` 有了对集合内元素的搜索的能力：

- **定向搜索**: `ceilingEntry()`, `floorEntry()`, `higherEntry()`和 `lowerEntry()` 等方法可以用于定位大于、小于、大于等于、小于等于给定键的最接近的键值对。
- **子集操作**: `subMap()`, `headMap()`和 `tailMap()` 方法可以高效地创建原集合的子集视图，而无需复制整个集合。
- **逆序视图**:`descendingMap()` 方法返回一个逆序的 `NavigableMap` 视图，使得可以反向迭代整个 `TreeMap`。
- **边界操作**: `firstEntry()`, `lastEntry()`, `pollFirstEntry()`和 `pollLastEntry()` 等方法可以方便地访问和移除元素

> 基于红黑树数据结构的属性实现的，保证了搜索操作的时间复杂度为 O(log n)

实现`SortedMap`接口让 `TreeMap` 有了对集合中的元素根据键排序的能力。默认是按 key 的升序排序，不过我们也可以指定排序的比较器

## [HashSet 如何检查重复?](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashset-如何检查重复)

先比较`hashcode`有相等的再调用`equals()`，都相同说明有重复元素，不会让加入操作成功

JDK1.8中，`HashSet`的`add()`方法只是简单的调用了`HashMap`的`put()`方法，并且判断了一下返回值以确保是否有重复元素

```java
// Returns: true if this set did not already contain the specified element
// 返回值：当 set 中没有包含 add 的元素时返回真
public boolean add(E e) {
        return map.put(e, PRESENT)==null;
}
```

而在`HashMap`的`putVal()`方法中也能看到如下说明：

```java
// Returns : previous value, or null if none
// 返回值：如果插入位置没有元素返回null，否则返回上一个元素
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
...
}
```

也就是说，在 JDK1.8 中，实际上无论`HashSet`中是否已经存在了某元素，`HashSet`都会直接插入，只是会在`add()`方法的返回值处告诉我们插入前是否存在相同元素

## [HashMap 的底层实现](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-的底层实现)

### [JDK1.8 之前](https://javaguide.cn/java/collection/java-collection-questions-02.html#jdk1-8-之前)

JDK1.8 之前 `HashMap` 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**。

HashMap 通过 key 的 `hashcode` 经过扰动函数处理过后得到 hash 值，然后通过 `(n - 1) & hash` 判断当前元素存放的位置（这里的 n 指的是数组的长度）

- 如果当前位置存在元素的话，就判断该元素与要存入的元素 key 是否相同
- 如果相同的话，直接覆盖，不相同就通过拉链法解决冲突

扰动函数指HashMap中的`hash`方法，是为了防止实现比较差的`hashCode()`方法造成碰撞次数过多

 JDK1.8 的 hash 方法 ，JDK 1.7 的 hash 方法的性能会稍差一点点，因为扰动计算进行了4次

### [JDK1.8 之后](https://javaguide.cn/java/collection/java-collection-questions-02.html#jdk1-8-之后)

解决哈希冲突时发生变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间

下图未包含抓换红黑树之前的判断

![image-20240729162536386](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240729162536386.png)

## [HashMap 的长度为什么是 2 的幂次方](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-的长度为什么是-2-的幂次方)

Hash值范围过大，不能直接用，需要对数组的长度进行取模运算

**取余(%)操作中如果除数是 2 的幂次则等价于与其除数减一的与(&)操作（也就是说 hash % length == hash&(length-1) 的前提是 length 是 2 的 n 次方；）**

## [HashMap 多线程操作导致死循环问题](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-多线程操作导致死循环问题)

JDK1.7 及之前版本的 `HashMap` 在多线程环境下扩容操作可能存在死循环问题，这是由于当一个桶位中有多个元素需要进行扩容时，多个线程同时对链表进行操作，头插法可能会导致链表中的节点指向错误的位置，从而形成一个环形链表，进而使得查询元素的操作陷入死循环无法结束

1.8之后采用尾插法来避免链表倒置，插入节点放尾部，避免了链表中的环形结构。

> 但是还是不建议在多线程下使用 `HashMap`，因为多线程下使用 `HashMap` 还是会存在数据覆盖的问题。并发环境下，推荐使用 `ConcurrentHashMap`

## [HashMap 为什么线程不安全？](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-为什么线程不安全)

JDK1.7 及之前版本，在多线程环境下，`HashMap` 扩容时会造成死循环和数据丢失的问题。

1.8之后死循环问题解决，但是当线程1,2的数据发生哈希冲突时，线程1判断哈希冲突之后时间片消耗尽被挂起，线程2进行插入，之后线程1会直接进行插入，将线程2的数据覆盖。

## [HashMap 常见的遍历方式?](https://javaguide.cn/java/collection/java-collection-questions-02.html#hashmap-常见的遍历方式)

[HashMap 的 7 种遍历方式与性能分析！](https://mp.weixin.qq.com/s/zQBN3UvJDhRTKP6SzcZFKw)

1. 使用迭代器（Iterator）EntrySet 的方式进行遍历；
2. 使用迭代器（Iterator）KeySet 的方式进行遍历；
3. 使用 For Each EntrySet 的方式进行遍历；
4. 使用 For Each KeySet 的方式进行遍历；
5. 使用 Lambda 表达式的方式进行遍历；` map.forEach((key, value) -> {
               System.out.println(key);
               System.out.println(value);
           })`
6. 使用 Streams API 单线程的方式进行遍历；

```java
map.entrySet().Stream().forEach((entry) -> {
            System.out.println(entry.getKey());
            System.out.println(entry.getValue());
        });
```

7. 使用 Streams API 多线程的方式进行遍历

```java
map.entrySet().parallelStream().forEach((entry) -> {
            System.out.println(entry.getKey());
            System.out.println(entry.getValue());
        });
```

这篇文章对于 parallelStream 遍历方式的性能分析有误，先说结论：**存在阻塞时 parallelStream 性能最高, 非阻塞时 parallelStream 性能最低**

## [ConcurrentHashMap 和 Hashtable 的区别](https://javaguide.cn/java/collection/java-collection-questions-02.html#concurrenthashmap-和-hashtable-的区别)

- **底层数据结构：** 
  - JDK1.7 的 `ConcurrentHashMap` 底层采用 **分段的数组+链表** 实现，
  - JDK1.8 采用的数据结构跟 `HashMap1.8` 的结构一样，数组+链表/红黑二叉树。
  - `Hashtable` 和 JDK1.8 之前的 `HashMap` 的底层数据结构类似都是采用 **数组+链表** 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
- 实现线程安全的方式（重要）：
  - 在 JDK1.7 的时候，`ConcurrentHashMap` 对整个桶数组进行了分割分段(`Segment`，分段锁)，每一把锁只锁容器其中一部分数据（下面有示意图），多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。
  - 到了 JDK1.8 的时候，`ConcurrentHashMap` 已经摒弃了 `Segment` 的概念，而是直接用 `Node` 数组+链表+红黑树的数据结构来实现，并发控制使用 `synchronized` 和 CAS 来操作。（JDK1.6 以后 `synchronized` 锁做了很多优化） 整个看起来就像是优化过且线程安全的 `HashMap`，虽然在 JDK1.8 中还能看到 `Segment` 的数据结构，但是已经简化了属性，只是为了兼容旧版本；
  - **`Hashtable`(同一把锁)** :使用 `synchronized` 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。

## [ConcurrentHashMap 线程安全的具体实现方式/底层具体实现](https://javaguide.cn/java/collection/java-collection-questions-02.html#concurrenthashmap-线程安全的具体实现方式-底层具体实现)

### [JDK1.8 之前](https://javaguide.cn/java/collection/java-collection-questions-02.html#jdk1-8-之前-1)

![image-20240516143020942](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240516143020942.png)
`Segment` 继承了 `ReentrantLock`，`Segment`数组一旦初始化就不能改变，默认大小是16，也就是说同时支持16个线程并发写。

### [JDK1.8 之后](https://javaguide.cn/java/collection/java-collection-questions-02.html#jdk1-8-之后-1)

![image-20240516143219418](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240516143219418.png)

采用 `Node + CAS + synchronized` 来保证并发安全

Java 8 中，锁粒度更细，`synchronized` 只锁定当前链表或红黑二叉树的首节点，这样只要 hash 不冲突，就不会产生并发，就不会影响其他 Node 的读写，效率大幅提升

当然，红黑树情况下需要使用`TreeNode`，被`TreeBin`包装。`TreeBin`通过`root`属性维护红黑树的根结点。

红黑树旋转的时候，根节点可能被子节点替换，其他线程访问会产生线程安全问题，在 `ConcurrentHashMap` 中`TreeBin`通过`waiter`属性维护当前使用这棵红黑树的线程，来防止其他线程的进入。

## [JDK 1.7 和 JDK 1.8 的 ConcurrentHashMap 实现有什么不同？](https://javaguide.cn/java/collection/java-collection-questions-02.html#jdk-1-7-和-jdk-1-8-的-concurrenthashmap-实现有什么不同)

- **线程安全实现方式**：JDK 1.7 采用 `Segment` 分段锁来保证安全， `Segment` 是继承自 `ReentrantLock`。JDK1.8 放弃了 `Segment` 分段锁的设计，采用 `Node + CAS + synchronized` 保证线程安全，锁粒度更细，`synchronized` 只锁定当前链表或红黑二叉树的首节点。
- **Hash 碰撞解决方法** : JDK 1.7 采用拉链法，JDK1.8 采用拉链法结合红黑树（链表长度超过一定阈值时，将链表转换为红黑树）。
- **并发度**：JDK 1.7 最大并发度是 Segment 的个数，默认是 16。JDK 1.8 最大并发度是 Node 数组的大小，并发度更大。

## [ConcurrentHashMap 为什么 key 和 value 不能为 null？](https://javaguide.cn/java/collection/java-collection-questions-02.html#concurrenthashmap-为什么-key-和-value-不能为-null)

主要为了避免二义性

例如，get 方法，返回的结果为 null 存在两种情况：

- 值没有在集合中 ；
- 值本身就是 null。

非并发中，get得到null，可以用`contains(key)`进行进一步判断，但是并发环境下，存在一个线程操作该 `ConcurrentHashMap` 时，其他的线程将该 `ConcurrentHashMap` 修改的情况，所以无法通过 `containsKey(key)` 来判断否存在这个键值对，也就没办法解决二义性问题了。

`HashMap` 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个。如果传入 null 键作为参数，就会返回 hash 值为 0 的位置的值。

> 多线程下无法正确判定键值对是否存在（存在其他线程修改的情况），单线程是可以的（不存在其他线程修改的情况）
>
> 单线程下可以容忍歧义，多线程下无法容忍

## [ConcurrentHashMap 能保证复合操作的原子性吗？](https://javaguide.cn/java/collection/java-collection-questions-02.html#concurrenthashmap-能保证复合操作的原子性吗)

`ConcurrentHashMap` 提供了一些原子性的复合操作，如 `putIfAbsent`、`compute`、`computeIfAbsent` 、`computeIfPresent`、`merge`等。

这些方法都可以接受一个函数作为参数，根据给定的 key 和 value 来计算一个新的 value，并且将其更新到 map 中

# [Collections 工具类（不重要）](https://javaguide.cn/java/collection/java-collection-questions-02.html#collections-工具类-不重要)

**`Collections` 工具类常用方法**:

- 排序
- 查找,替换操作
- 同步控制(不推荐，需要线程安全的集合类型时请考虑使用 JUC 包下的并发集合)

## [排序操作](https://javaguide.cn/java/collection/java-collection-questions-02.html#排序操作)

```java
void reverse(List list)//反转
void shuffle(List list)//随机排序
void sort(List list)//按自然排序的升序排序
void sort(List list, Comparator c)//定制排序，由Comparator控制排序逻辑
void swap(List list, int i , int j)//交换两个索引位置的元素
void rotate(List list, int distance)//旋转。当distance为正数时，将list后distance个元素整体移到前面。当distance为负数时，将 list的前distance个元素整体移到后面
```

## [查找,替换操作](https://javaguide.cn/java/collection/java-collection-questions-02.html#查找-替换操作)

```java
int binarySearch(List list, Object key)//对List进行二分查找，返回索引，注意List必须是有序的
int max(Collection coll)//根据元素的自然顺序，返回最大的元素。 类比int min(Collection coll)
int max(Collection coll, Comparator c)//根据定制排序，返回最大元素，排序规则由Comparatator类控制。类比int min(Collection coll, Comparator c)
void fill(List list, Object obj)//用指定的元素代替指定list中的所有元素
int frequency(Collection c, Object o)//统计元素出现次数
int indexOfSubList(List list, List target)//统计target在list中第一次出现的索引，找不到则返回-1，类比int lastIndexOfSubList(List source, list target)
boolean replaceAll(List list, Object oldVal, Object newVal)//用新元素替换旧元素
```

## [同步控制](https://javaguide.cn/java/collection/java-collection-questions-02.html#同步控制)

`Collections` 提供了多个`synchronizedXxx()`方法·，该方法可以将指定集合包装成线程同步的集合，从而解决多线程并发访问集合时的线程安全问题。

我们知道 `HashSet`，`TreeSet`，`ArrayList`,`LinkedList`,`HashMap`,`TreeMap` 都是线程不安全的。`Collections` 提供了多个静态方法可以把他们包装成线程同步的集合。

**最好不要用下面这些方法，效率非常低，需要线程安全的集合类型时请考虑使用 JUC 包下的并发集合。**

方法如下：

```java
synchronizedCollection(Collection<T>  c) //返回指定 collection 支持的同步（线程安全的）collection。
synchronizedList(List<T> list)//返回指定列表支持的同步（线程安全的）List。
synchronizedMap(Map<K,V> m) //返回由指定映射支持的同步（线程安全的）Map。
synchronizedSet(Set<T> s) //返回指定 set 支持的同步（线程安全的）set。
```