# [基础知识]()

## [线程和进程]()

![image-20240509202818315](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240509202818315.png)

一个进程中多个线程，共享堆区，方法区（1.8之后是元空间），有自己独有的虚拟机栈，本地方法栈，程序计数器

**线程是进程划分成的更小的运行单位。线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反**

## [内核线程和用户线程]()

- 用户线程：由用户空间程序管理和调度的线程，运行在用户空间（专门给应用程序使用）。
- 内核线程：由操作系统内核管理和调度的线程，运行在内核空间（只有内核程序可以访问）

- 用户线程创建和切换成本低，但不可以利用多核。

- 内核态线程，创建和切换成本高，可以利用多核。

线程模型是用户线程和内核线程之间的关联方式，常见的线程模型有这三种：

1. 一对一（一个用户线程对应一个内核线程）
2. 多对一（多个用户线程映射到一个内核线程）
3. 多对多（多个用户线程映射到多个内核线程）

![image-20240509205434415](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240509205434415.png)

JDK 1.2 及以后，Java 线程改为基于原生线程（Native Threads）实现，也就是说 JVM 直接使用操作系统原生的内核级线程（内核线程）来实现 Java 线程，由操作系统内核进行线程的调度和管理。

一句话概括 Java 线程和操作系统线程的关系：**现在的 Java 线程的本质其实就是操作系统的线程**

Java 线程采用的是一对一的线程模型，也就是一个 Java 线程对应一个系统内核线程

## [线程创建]()

继承`Thread`类、实现`Runnable`接口、实现`Callable`接口、使用线程池、使用`CompletableFuture`类等等

但严格来说，只有`new Thread().start()`，其他方法都依赖于这个

## [线程生命周期状态]()

![image-20240509203143924](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240509203143924.png)

- NEW: 初始状态，线程被创建出来但没有被调用 `start()` 。
- RUNNABLE: 运行状态，线程被调用了 `start()`等待运行的状态。
- BLOCKED：阻塞状态，需要等待锁释放。
- WAITING：等待状态，表示该线程需要等待其他线程做出一些特定动作（通知或中断）。
- TIME_WAITING：超时等待状态，可以在指定的时间后自行返回而不是像 WAITING 那样一直等待。
- TERMINATED：终止状态，表示该线程已经运行完毕

> 仅仅在操作系统层面区分READY 和 RUNNING 状态，jvm层面不区分

## [什么是线程上下文切换?](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#什么是线程上下文切换)

线程从cpu占用中退出：

- 主动让出 CPU，比如调用了 `sleep()`, `wait()` 等。
- 时间片用完，因为操作系统要防止一个线程或者进程长时间占用 CPU 导致其他线程或者进程饿死。
- 调用了阻塞类型的系统中断，比如请求 IO，线程被阻塞。
- 被终止或结束运行

前三种会发生线程切换，需要保存当前线程的上下文，并加载下一个线程的上下文

## [Thread#sleep() 方法和 Object#wait() 方法对比](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#thread-sleep-方法和-object-wait-方法对比)

**共同点**：两者都可以暂停线程的执行。

**区别**：

- **`sleep()` 方法没有释放锁，而 `wait()` 方法释放了锁** 。
- `wait()` 通常被用于线程间交互/通信，`sleep()`通常被用于暂停执行。
- `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify()`或者 `notifyAll()` 方法。`sleep()`方法执行完成后，线程会自动苏醒，或者也可以使用 `wait(long timeout)` 超时后线程会自动苏醒。
- `sleep()` 是 `Thread` 类的静态本地方法，`wait()` 则是 `Object` 类的本地方法。

## [为什么 wait() 方法不定义在 Thread 中？](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#为什么-wait-方法不定义在-thread-中)

`wait()` 是让获得对象锁的线程实现等待，会自动释放当前线程占有的对象锁。

`sleep()` 是让当前线程暂停执行，不涉及到对象类，也不需要获得对象锁。

## [可以直接调用 Thread 类的 run 方法吗？](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#可以直接调用-thread-类的-run-方法吗)

new 一个 `Thread`，线程进入了新建状态。调用 `start()`方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。

直接执行 `run()` 方法，会把 `run()` 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作

# [多线程](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#多线程)

## [并发与并行的区别](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#并发与并行的区别)

- **并发**：两个及两个以上的作业在同一 **时间段** 内执行。
- **并行**：两个及两个以上的作业在同一 **时刻** 执行。

## [同步和异步的区别](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#同步和异步的区别)

- **同步**：发出一个调用之后，在没有得到结果之前， 该调用就不可以返回，一直等待。
- **异步**：调用在发出之后，不用等待返回结果，该调用直接返回

## [为什么要使用多线程?](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#为什么要使用多线程)

先从总体上来说：

- **从计算机底层来说：** 
  - 线程可以比作是轻量级的进程，是程序执行的最小单位,线程间的切换和调度的成本远远小于进程。
  - 另外，多核 CPU 时代意味着多个线程可以同时运行，这减少了线程上下文切换的开销。
- **从当代互联网发展趋势来说：** 现在的系统动不动就要求百万级甚至千万级的并发量，而多线程并发编程正是开发高并发系统的基础，利用好多线程机制可以大大提高系统整体的并发能力以及性能。

再深入到计算机底层来探讨：

- **单核时代**：在单核时代多线程主要是为了提高单进程利用 CPU 和 IO 系统的效率。 假设只运行了一个 Java 进程的情况，当我们请求 IO 的时候，如果 Java 进程中只有一个线程，此线程被 IO 阻塞则整个进程被阻塞。CPU 和 IO 设备只有一个在运行，那么可以简单地说系统整体效率只有 50%。当使用多线程的时候，一个线程被 IO 阻塞，其他线程还可以继续使用 CPU。从而提高了 Java 进程利用系统资源的整体效率。
- **多核时代**: 多核时代多线程主要是为了提高进程利用多核 CPU 的能力。举个例子：假如我们要计算一个复杂的任务，我们只用一个线程的话，不论系统有几个 CPU 核心，都只会有一个 CPU 核心被利用到。而创建多个线程，这些线程可以被映射到底层多个 CPU 上执行，在任务中的多个线程没有资源竞争的情况下，任务执行的效率会有显著性的提高，约等于（单核时执行时间/CPU 核心数）

## [单核 CPU 上运行多个线程效率一定会高吗？](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#单核-cpu-上运行多个线程效率一定会高吗)

CPU密集型任务，多个线程频繁切换使用单核，增加系统开销，降低效率

IO密集型，可以在IO阻塞时间运行别的线程，提高效率

## [死锁的必要条件]()

1. 互斥条件：该资源任意一个时刻只由一个线程占用。
2. 请求与保持条件：一个线程因请求资源而阻塞时，对已获得的资源保持不放。
3. 不剥夺条件:线程已获得的资源在未使用完之前不能被其他线程强行剥夺，只有自己使用完毕后才释放资源。
4. 循环等待条件:若干线程之间形成一种头尾相接的循环等待资源关系

## [如何检测死锁？](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#如何检测死锁)

- 使用`jmap`、`jstack`等命令查看 JVM 线程栈和堆内存的情况。如果有死锁，`jstack` 的输出中通常会有 `Found one Java-level deadlock:`的字样，后面会跟着死锁相关的线程信息。另外，实际项目中还可以搭配使用`top`、`df`、`free`等命令查看操作系统的基本情况，出现死锁可能会导致 CPU、内存等资源消耗过高。
- 采用 VisualVM、JConsole 等工具进行排查。

## [如何预防和避免线程死锁?](https://javaguide.cn/java/concurrent/java-concurrent-questions-01.html#如何预防和避免线程死锁)

**如何预防死锁？**

 破坏死锁的产生的必要条件即可：

1. **破坏请求与保持条件**：一次性申请所有的资源。
2. **破坏不剥夺条件**：占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。
3. **破坏循环等待条件**：靠按序申请资源来预防。按某一顺序申请资源，释放资源则反序释放。破坏循环等待条件。

**如何避免死锁？**

避免死锁就是在资源分配时，借助于算法（比如银行家算法）对资源分配进行计算评估，使其进入安全状态。

# [JMM（Java 内存模型）]()

## [cpu缓存模型]()

![cpu缓存模型示意图](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240509212112960.png)

**CPU Cache 缓存的是内存数据用于解决 CPU 处理速度和内存不匹配的问题，内存缓存的是硬盘数据用于解决硬盘访问速度过慢的问题**

**CPU Cache 的工作方式：** 先复制一份数据到 CPU Cache 中，当 CPU 需要用到的时候就可以直接从 CPU Cache 中读取数据，当运算完成后，再将运算得到的数据写回 Main Memory 中。但是，这样存在 **内存缓存不一致性的问题** ！比如我执行一个 i++ 操作的话，如果两个线程同时执行的话，假设两个线程从 CPU Cache 中读取的 i=1，两个线程做了 i++ 运算完之后再写回 Main Memory 之后 i=2，而正确结果应该是 i=3。

**CPU 为了解决内存缓存不一致性问题可以通过制定缓存一致协议（比如 [MESI 协议](https://zh.wikipedia.org/wiki/MESI协议)）或者其他手段来解决**

我们的程序运行在操作系统之上，操作系统屏蔽了底层硬件的操作细节，将各种硬件资源虚拟化。于是，操作系统也就同样需要解决内存缓存不一致性问题。

操作系统通过 **内存模型（Memory Model）** 定义一系列规范来解决这个问题。无论是 Windows 系统，还是 Linux 系统，它们都有特定的内存模型。

## [指令重排序](https://javaguide.cn/java/concurrent/jmm.html#指令重排序)

**什么是指令重排序？** 简单来说就是系统在执行代码的时候并不一定是按照你写的代码的顺序依次执行。

常见的指令重排序有下面 2 种情况：

- **编译器优化重排**：编译器（包括 JVM、JIT 编译器等）在不改变单线程程序语义的前提下，重新安排语句的执行顺序。
- **指令并行重排**：现代处理器采用了指令级并行技术(Instruction-Level Parallelism，ILP)来将多条指令重叠执行。如果不存在数据依赖性，处理器可以改变语句对应机器指令的执行顺序

> 内存系统也会有“重排序”，但又不是真正意义上的重排序。在 JMM 里表现为主存和本地内存的内容可能不一致，进而导致程序在多线程下执行可能出现问题

Java 源代码会经历 **编译器优化重排 —> 指令并行重排 —> 内存系统重排** 的过程，最终才变成操作系统可执行的指令序列

**指令重排序可以保证串行语义一致，但是没有义务保证多线程间的语义也一致** 

- 对于编译器，通过禁止特定类型的编译器重排序的方式来禁止重排序。
- 对于处理器级别重排序（指令并行重排，内存系统重排），通过插入内存屏障（Memory Barrier，或有时叫做内存栅栏，Memory Fence）的方式来禁止特定类型的处理器重排序

> 内存屏障（Memory Barrier，或有时叫做内存栅栏，Memory Fence）是一种 CPU 指令，用来禁止处理器指令发生重排序（像屏障一样），从而保障指令执行的有序性。另外，为了达到屏障的效果，它也会使处理器写入、读取值之前，将主内存的值写入高速缓存，清空无效队列，从而保障变量的可见性。

## [什么是 JMM？为什么需要 JMM？](https://javaguide.cn/java/concurrent/jmm.html#什么是-jmm-为什么需要-jmm)

编程语言可以直接用操作系统层面的内存模型，但是无法跨系统。

对于 Java 来说，你可以把 JMM 看作是 Java 定义的并发编程相关的一组规范，除了抽象了线程和主内存之间的关系之外，其还规定了从 Java 源代码到 CPU 可执行指令的这个转化过程要遵守哪些和并发相关的原则和规范，其主要目的是为了简化多线程编程，增强程序可移植性的。

## [JMM 是如何抽象线程和主内存之间的关系？](https://javaguide.cn/java/concurrent/jmm.html#jmm-是如何抽象线程和主内存之间的关系)

![image-20240509213555247](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240509213555247.png)

定义八种同步操作：

- **锁定（lock）**: 作用于主内存中的变量，将他标记为一个线程独享变量。
- **解锁（unlock）**: 作用于主内存中的变量，解除变量的锁定状态，被解除锁定状态的变量才能被其他线程锁定。
- **read（读取）**：作用于主内存的变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的 load 动作使用。
- **load(载入)**：把 read 操作从主内存中得到的变量值放入工作内存的变量的副本中。
- **use(使用)**：把工作内存中的一个变量的值传给执行引擎，每当虚拟机遇到一个使用到变量的指令时都会使用该指令。
- **assign（赋值）**：作用于工作内存的变量，它把一个从执行引擎接收到的值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作。
- **store（存储）**：作用于工作内存的变量，它把工作内存中一个变量的值传送到主内存中，以便随后的 write 操作使用。
- **write（写入）**：作用于主内存的变量，它把 store 操作从工作内存中得到的变量的值放入主内存的变量中。

同步规则：

- 不允许一个线程无原因地（没有发生过任何 assign 操作）把数据从线程的工作内存同步回主内存中。
- 一个新的变量只能在主内存中 “诞生”，不允许在工作内存中直接使用一个未被初始化（load 或 assign）的变量，换句话说就是对一个变量实施 use 和 store 操作之前，必须先执行过了 assign 和 load 操作。
- 一个变量在同一个时刻只允许一条线程对其进行 lock 操作，但 lock 操作可以被同一条线程重复执行多次，多次执行 lock 后，只有执行相同次数的 unlock 操作，变量才会被解锁。
- 如果对一个变量执行 lock 操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行 load 或 assign 操作初始化变量的值。
- 如果一个变量事先没有被 lock 操作锁定，则不允许对它执行 unlock 操作，也不允许去 unlock 一个被其他线程锁定住的变量。
- ……

## [Java 内存区域和 JMM 有何区别？](#java-内存区域和-jmm-有何区别)

这是一个比较常见的问题，很多初学者非常容易搞混。 **Java 内存区域和内存模型是完全不一样的两个东西**：

- JVM 内存结构和 Java 虚拟机的运行时区域相关，定义了 JVM 在运行时如何分区存储程序数据，就比如说堆主要用于存放对象实例。
- Java 内存模型和 Java 的并发编程相关，抽象了线程和主内存之间的关系
  - 就比如说线程之间的共享变量必须存储在主内存中，
  - 规定了从 Java 源代码到 CPU 可执行指令的这个转化过程要遵守哪些和并发相关的原则和规范，
  - 其主要目的是为了简化多线程编程，增强程序可移植性的

## [happens-before 原则是什么？](https://javaguide.cn/java/concurrent/jmm.html#happens-before-原则是什么)

分布式环境中，需要一定规则定义逻辑时钟变化，来判断事件先后顺序

设计思想：禁止会改变程序结果的重排序

 JSR-133 对 happens-before 原则的定义：

- 如果一个操作 happens-before 另一个操作，那么第一个操作的执行结果将对第二个操作可见，并且第一个操作的执行顺序排在第二个操作之前。
- 两个操作之间存在 happens-before 关系，并不意味着 Java 平台的具体实现必须要按照 happens-before 关系指定的顺序来执行。如果重排序之后的执行结果，与按 happens-before 关系来执行的结果一致，那么 JMM 也允许这样的重排序。

happens-before最准确的含义是，前一个操作对后一个操作是可见的，**无论这两个操作是否在同一个线程里**

## [happens-before 常见规则有哪些？谈谈你的理解？](#happens-before-常见规则有哪些-谈谈你的理解)

happens-before 的规则就 8 条，说多不多，重点了解下面列举的 5 条即可。全记是不可能的，很快就忘记了，意义不大，随时查阅即可。

1. **程序顺序规则**：一个线程内，按照代码顺序，书写在前面的操作 happens-before 于书写在后面的操作；
2. **解锁规则**：解锁 happens-before 于加锁；
3. **volatile 变量规则**：对一个 volatile 变量的写操作 happens-before 于后面对这个 volatile 变量的读操作。说白了就是对 volatile 变量的写操作的结果对于发生于其后的任何操作都是可见的。
4. **传递规则**：如果 A happens-before B，且 B happens-before C，那么 A happens-before C；
5. **线程启动规则**：Thread 对象的 `start()`方法 happens-before 于此线程的每一个动作。

如果两个操作不满足上述任意一个 happens-before 规则，那么这两个操作就没有顺序的保障，JVM 可以对这两个操作进行重排序。

## [happens-before 和 JMM 什么关系？](https://javaguide.cn/java/concurrent/jmm.html#happens-before-和-jmm-什么关系)

happens-before 与 JMM 的关系用《Java 并发编程的艺术》这本书中的一张图就可以非常好的解释清楚。

![image-20240510005718501](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240510005718501.png)

## [并发编程三个重要特性](#再看并发编程三个重要特性)

### [原子性](#原子性)

一次操作或者多次操作，要么所有的操作全部都得到执行并且不会受到任何因素的干扰而中断，要么都不执行。

在 Java 中，可以借助`synchronized`、各种 `Lock` 以及各种原子类实现原子性。

`synchronized` 和各种 `Lock` 可以保证任一时刻只有一个线程访问该代码块，因此可以保障原子性。各种原子类是利用 CAS (compare and swap) 操作（可能也会用到 `volatile`或者`final`关键字）来保证原子操作。

### [可见性](#可见性)

当一个线程对共享变量进行了修改，那么另外的线程都是立即可以看到修改后的最新值。

在 Java 中，可以借助`synchronized`、`volatile` 以及各种 `Lock` 实现可见性。

如果我们将变量声明为 `volatile` ，这就指示 JVM，这个变量是共享且不稳定的，每次使用它都到主存中进行读取。

### [有序性](#有序性)

由于指令重排序问题，代码的执行顺序未必就是编写代码时候的顺序。

我们上面讲重排序的时候也提到过：

> **指令重排序可以保证串行语义一致，但是没有义务保证多线程间的语义也一致** ，所以在多线程下，指令重排序可能会导致一些问题。

在 Java 中，`volatile` 关键字可以禁止指令进行重排序优化

# [volatile 关键字](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#volatile-关键字)

## [如何保证变量的可见性](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#如何保证变量的可见性)

在 Java 中，`volatile` 关键字可以保证变量的可见性，如果我们将变量声明为 **`volatile`** ，这就指示 JVM，这个变量是共享且不稳定的，每次使用它都到主存中进行读取

C语言中也有，最原始的意思就是禁用CPU缓存，迫使每次使用该变量都在主存中读取

`volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保证

## [如何禁止指令重排序？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#如何禁止指令重排序)

**在 Java 中，`volatile` 关键字除了可以保证变量的可见性，还有一个重要的作用就是防止 JVM 的指令重排序。** 如果我们将变量声明为 **`volatile`** ，在对这个变量进行读写操作的时候，会通过插入特定的 **内存屏障** 的方式来禁止指令重排序

还可以使用`Unsafe`类提供的三个开箱即用的内存屏障相关方法，但麻烦。

```java
public native void loadFence();
public native void storeFence();
public native void fullFence();
```

## [volatile 可以保证原子性么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#volatile-可以保证原子性么)

**`volatile` 关键字能保证变量的可见性，但不能保证对变量的操作是原子性的。**

# [乐观锁和悲观锁](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#乐观锁和悲观锁)

## [什么是悲观锁？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#什么是悲观锁)

**共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程**

 Java 中`synchronized`和`ReentrantLock`等独占锁就是悲观锁思想的实现

高并发的场景下，激烈的锁竞争会造成线程阻塞，大量阻塞线程会导致系统的上下文切换，增加系统的性能开销。并且，悲观锁还可能会存在死锁问题，影响代码的正常运行

## [什么是乐观锁？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#什么是乐观锁)

乐观锁总是假设最好的情况，认为共享资源每次被访问的时候不会出现问题，线程可以不停地执行，无需加锁也无需等待，只是在提交修改的时候去验证对应的资源（也就是数据）是否被其它线程修改了（具体方法可以使用版本号机制或 CAS 算法）

 Java 中`java.util.concurrent.atomic`包下面的原子变量类（比如`AtomicInteger`、`LongAdder`）就是使用了乐观锁的一种实现方式 **CAS** 实现的。

优点：不存在锁竞争造成线程阻塞，也不会有死锁的问题，在性能上往往会更胜一筹

缺点：如果冲突频繁发生（写占比非常多的情况），会频繁失败和重试，这样同样会非常影响性能，导致 CPU 飙升

## [悲观锁和乐观锁的适用范围]()

- 悲观锁通常用于写较多场景，避免频繁失败重试
  - 如果乐观锁解决了频繁失败和重试这个问题的话（比如`LongAdder`），也是可以考虑使用乐观锁的，要视实际情况而定
- 乐观锁通常用于写比较少场景（多读），避免频繁加锁影响性能
  - 主要针对单个共享变量（`java.util.concurrent.atomic`包下面的原子变量类）

## [如何实现乐观锁？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#如何实现乐观锁)

**版本号机制**：在数据中增加版本号version字段，当数据被修改时，修改版本号；当一个线程要访问数据时，先读出数据，在准备写时，对比当前数据和开始读出来的数据版本号是否一致，如果一致，则写，不一致则认为冲突。

**CAS算法**：CAS全称是比较于交换Compare And Swap。CAS的思想是用一个预期值和要更新的变量值进行比较，如果与预期相符才更新（预期＝当前）。

CAS在执行更新操作前，先比较当前变量与预期是否相等，如果不相等，则放弃更新；如果相等，则调用一个原子操作，把新值写入

`sun.misc`包下的`Unsafe`类提供了`compareAndSwapObject`、`compareAndSwapInt`、`compareAndSwapLong`方法来实现的对`Object`、`int`、`long`类型的 CAS 操作

## [CAS 算法存在哪些问题？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#cas-算法存在哪些问题)

### [ABA 问题](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#aba-问题)

乐观锁最常见的问题

变量V初次读取的时候是A值，准备赋值的时候仍然是A，但是可能被其他线程修改为其他值，然后又改回A，CAS会误认为它从没被修改过，这就是ABA问题

解决方法：变量前加版本号或时间戳

JDK 1.5 以后的 `AtomicStampedReference` 类就是用来解决 ABA 问题的，其中的 `compareAndSet()` 方法就是首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志。

### [循环时间长开销大](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#循环时间长开销大)

CAS 经常会用到自旋操作来进行重试，也就是不成功就一直循环执行直到成功。如果长时间不成功，会给 CPU 带来非常大的执行开销

解决方法：如果 JVM 能支持处理器提供的 pause 指令那么效率会有一定的提升，pause 指令有两个作用：

1. 可以延迟流水线执行指令，使 CPU 不会消耗过多的执行资源，延迟的时间取决于具体实现的版本，在一些处理器上延迟时间是零。
2. 可以避免在退出循环的时候因内存顺序冲突而引起 CPU 流水线被清空，从而提高 CPU 的执行效率

### [只能保证一个共享变量的原子操作](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#只能保证一个共享变量的原子操作)

CAS 只对单个共享变量有效，当操作涉及跨多个共享变量时 CAS 无效。

解决方法：使用锁或者利用`AtomicReference`类把多个共享变量合并成一个共享变量来操作

# [synchronized 关键字](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-关键字)

## [synchronized 是什么？有什么用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-是什么-有什么用)

`synchronized`是Java中的关键字，用于实现线程的同步，主要用于解决多线程的并发访问共享资源可能出现的并发问题。

## [如何使用 synchronized？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#如何使用-synchronized)

**:one:修饰实例方法** （锁当前对象实例）

给当前对象实例加锁，进入同步代码前要获得 **当前对象实例的锁** 。

```java
synchronized void method() {
    //业务代码
}
```

**:two:修饰静态方法** （锁当前类）

给当前类加锁，会作用于类的所有对象实例 ，进入同步代码前要获得 **当前 class 的锁**。

```java
synchronized static void method() {
    //业务代码
}
```

**:three:修饰代码块** （锁指定对象/类）

- `synchronized(object)` 表示进入同步代码库前要获得 **给定对象的锁**。
- `synchronized(类.class)` 表示进入同步代码前要获得 **给定 Class 的锁**

```java
synchronized(this) {
    //业务代码
}
```

## [构造方法可以用 synchronized 修饰么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#构造方法可以用-synchronized-修饰么)

构造方法本身就是线程安全的，如果在其内部涉及共享资源的操作，可以使用`synchronized`

## [synchronized 底层原理了解吗？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-底层原理了解吗)

### [synchronized 同步语句块](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-同步语句块的情况)

**`synchronized` 同步语句块的实现使用的是 `monitorenter` 和 `monitorexit` 指令**，能够保证锁在同步代码块代码正常执行以及出现异常的这两种情况下都能被正确释放

- `monitorenter` 指令指向同步代码块的开始位置

- `monitorexit` 指令则指明同步代码块的结束位置

当执行 `monitorenter` 指令时，线程试图获取锁也就是获取 **对象监视器 `monitor`** 的持有权。

> 在 Java 虚拟机(HotSpot)中，Monitor 是基于 C++实现的，由[ObjectMonitor](https://github.com/openjdk-mirror/jdk7u-hotspot/blob/50bdefc3afe944ca74c3093e7448d6b889cd20d1/src/share/vm/runtime/objectMonitor.cpp)实现的。每个对象中都内置了一个 `ObjectMonitor`对象。
>
> 另外，`wait/notify`等方法也依赖于`monitor`对象，这就是为什么只有在同步的块或者方法中才能调用`wait/notify`等方法，否则会抛出`java.lang.IllegalMonitorStateException`的异常的原因。

在执行`monitorenter`时，会尝试获取对象的锁，如果锁的计数器为 0 则表示锁可以被获取，获取后将锁计数器设为 1 也就是加 1。获取失败则阻塞。

对象锁的的拥有者线程才可以执行 `monitorexit` 指令来释放锁。在执行 `monitorexit` 指令后，将锁计数器设为 0，表明锁被释放，其他线程可以尝试获取锁。

### [synchronized 修饰方法的的情况](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-修饰方法的的情况)

使用`ACC_SYNCHRONIZED` 标识，JVM 通过该 `ACC_SYNCHRONIZED` 访问标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用。

如果是实例方法，JVM 会尝试获取实例对象的锁。如果是静态方法，JVM 会尝试获取当前 class 的锁

:warning: 两者的本质都是对对象监视器 monitor 的获取。

## [JDK1.6 之后的 synchronized 底层做了哪些优化？锁升级原理了解吗？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#jdk1-6-之后的-synchronized-底层做了哪些优化-锁升级原理了解吗)

在 Java 6 之后， `synchronized` 引入了大量的优化如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销，这些优化让 `synchronized` 锁的效率提升了很多（JDK18 中，偏向锁已经被彻底废弃，前面已经提到过了）。

锁主要存在四种状态，依次是：无锁状态、偏向锁状态、轻量级锁状态、重量级锁状态，他们会随着竞争的激烈而逐渐升级。

:warning:注意锁可以升级不可降级，这种策略是为了提高获得锁和释放锁的效率。

:question:读写锁的降级

`synchronized` 锁升级是一个比较复杂的过程，面试也很少问到，如果你想要详细了解的话，可以看看这篇文章：[浅析 synchronized 锁升级的原理与实现](https://www.cnblogs.com/star95/p/17542850.html)

## [synchronized 和 volatile 有什么区别？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-和-volatile-有什么区别)

`synchronized` 关键字和 `volatile` 关键字是两个互补的存在，而不是对立的存在！

- `volatile` 关键字是线程同步的轻量级实现，所以 `volatile`性能肯定比`synchronized`关键字要好 。
- 但是 `volatile` 关键字只能用于变量而 `synchronized` 关键字可以修饰方法以及代码块 。
- `volatile` 关键字能保证数据的可见性，但不能保证数据的原子性。`synchronized` 关键字两者都能保证。
- `volatile`关键字主要用于解决变量在多个线程之间的可见性，而 `synchronized` 关键字解决的是多个线程之间访问资源的同步性。

# [ReentrantLock](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#reentrantlock)

## [ReentrantLock 是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#reentrantlock-是什么)

`ReentrantLock` 实现了 `Lock` 接口，是一个可重入且独占式的锁，和 `synchronized` 关键字类似。

`ReentrantLock` 更灵活、更强大，增加了轮询、超时、中断、公平锁和非公平锁等高级功能。

`ReentrantLock` 里面有一个内部类 `Sync`，`Sync` 继承 AQS（`AbstractQueuedSynchronizer`），添加锁和释放锁的大部分操作实际上都是在 `Sync` 中实现的。`Sync` 有公平锁 `FairSync` 和非公平锁 `NonfairSync` 两个子类。

`ReentrantLock` 的底层就是由 AQS 来实现的

## [公平锁和非公平锁有什么区别？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#公平锁和非公平锁有什么区别)

- **公平锁**：公平锁会按照线程申请锁的顺序来分配锁，即先来先得。

  - 优点：公平，所有线程都有机会获取锁，避免了线程饥饿
  - 缺点：是公平锁需要额外的性能开销，需要维护一个等待队列，使得线程调度和切换更加频繁。

  > 非公平锁，当前释放锁线程可再次获得锁，减少了上下文切换

- **非公平锁**：锁被释放之后，后申请的线程可能会先获取到锁，是随机或者按照其他优先级排序的。

  - 优点：性能更好
  - 缺点：可能造成线程饥饿问题。

## [synchronized 和 ReentrantLock 有什么区别？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-和-reentrantlock-有什么区别)

### [两者都是可重入锁](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#两者都是可重入锁)

**可重入锁** 也叫递归锁，指的是线程可以再次获取自己的内部锁。

JDK 提供的所有现成的 `Lock` 实现类，包括 `synchronized` 关键字锁都是可重入的（StampedLock是不可重入锁，没实现Lock接口）

### [synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#synchronized-依赖于-jvm-而-reentrantlock-依赖于-api)

`synchronized` 是依赖于 JVM 实现的，前面我们也讲到了 虚拟机团队在 JDK1.6 为 `synchronized` 关键字进行了很多优化，但是这些优化都是在虚拟机层面实现的，并没有直接暴露给我们。

`ReentrantLock` 是 JDK 层面实现的（也就是 API 层面，需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成），所以我们可以通过查看它的源代码，来看它是如何实现的。

### [ReentrantLock 比 synchronized 增加了一些高级功能](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#reentrantlock-比-synchronized-增加了一些高级功能)

- **等待可中断** : 线程放弃锁等待，处理其他事情，通过`lock.lockInterruptibly()` 来实现这个机制
- **可实现公平锁** : `ReentrantLock`可以指定是公平锁还是非公平锁。而`synchronized`只能是非公平锁。`ReentrantLock`默认情况是非公平的，可以通过 `ReentrantLock`类的`ReentrantLock(boolean fair)`构造方法来指定是否是公平的。
- **可实现选择性通知（锁可以绑定多个条件）**:
  - `synchronized`关键字与`wait()`和`notify()`/`notifyAll()`方法相结合可以实现等待/通知机制，被通知线程是JVM选择的。
  - `ReentrantLock`类结合`Condition`实例可以实现“选择性通知”，实现方法：在一个`Lock`对象中可以创建多个`Condition`实例（即对象监视器），线程对象注册在指定的`Condition`中，`Condition`实例的`signalAll()`方法，唤醒注册在该`Condition`实例中的所有等待线程

## [可中断锁和不可中断锁有什么区别？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#可中断锁和不可中断锁有什么区别)

- **可中断锁**：获取锁的过程中可以被中断，不需要一直等到获取锁之后 才能进行其他逻辑处理。`ReentrantLock` 就属于是可中断锁。
- **不可中断锁**：一旦线程申请了锁，就只能等到拿到锁以后才能进行其他的逻辑处理。 `synchronized` 就属于是不可中断锁

# [ReentrantReadWriteLock](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#reentrantreadwritelock)

`ReentrantReadWriteLock` 在实际项目中使用的并不多，面试中也问的比较少，简单了解即可。JDK 1.8 引入了性能更好的读写锁 `StampedLock`

## [ReentrantReadWriteLock 是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#reentrantreadwritelock-是什么)

`ReentrantReadWriteLock` 实现了 `ReadWriteLock` ，是一个可重入的读写锁，既可以保证多个线程同时读的效率，同时又可以保证有写入操作时的线程安全。

相比与一般锁，读读不互斥了

`ReentrantReadWriteLock` 其实是两把锁，一把是 `WriteLock` (写锁)，一把是 `ReadLock`（读锁） 。读锁是共享锁，写锁是独占锁

支持公平锁和非公平锁

## [ReentrantReadWriteLock 适合什么场景？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#reentrantreadwritelock-适合什么场景)

由于 `ReentrantReadWriteLock` 既可以保证多个线程同时读的效率，同时又可以保证有写入操作时的线程安全。

因此，在读多写少的情况下，使用 `ReentrantReadWriteLock` 能够明显提升系统性能。

## [共享锁和独占锁有什么区别？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#共享锁和独占锁有什么区别)

共享锁可以被多个线程同时获得，独占锁只能被一个线程获得

## [线程持有读锁还能获取写锁吗？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#线程持有读锁还能获取写锁吗)

- 在线程持有读锁的情况下，该线程不能获得写锁

  - 获得写锁的时候，如果发现当前的读锁被占用，会马上返回失败，不管读锁是不是被当前线程持有

- 在线程持有写锁的情况下，该线程可以继续获得读锁

  - 获取读锁的时候写锁被当前线程占用，成功
  - 写锁被其他线程占用，失败

  > 实际上持有写锁的过程中占有读锁是一种锁降级的现象，这样持有读锁的过程中不能获得写锁就十分直观（这是一个不被允许的锁升级的过程）

## [读锁为什么不能升级为写锁？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#读锁为什么不能升级为写锁)

写锁可以降级为读锁，但是读锁却不能升级为写锁。这是因为读锁升级为写锁会引起线程的争夺，毕竟写锁属于是独占锁，这样的话，会影响性能。

另外，还可能会有死锁问题发生。举个例子：假设两个线程的读锁都想升级写锁，则需要对方都释放自己锁，而双方都不释放，就会产生死锁。

> 持有读锁的情况下获得写锁会失败，两个线程都持有读锁的同时又想获得写锁，满足了死锁产生的条件

## [锁降级的好处]()

:question:

# [StampedLock](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#stampedlock)

`StampedLock` 面试中问的比较少，不是很重要，简单了解即可。

## [StampedLock 是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#stampedlock-是什么)

`StampedLock` 是 JDK 1.8 引入的性能更好的读写锁，不可重入且不支持条件变量 `Condition`。

`StampedLock` 并不是直接实现 `Lock`或 `ReadWriteLock`接口，而是基于 **CLH 锁** 独立实现的（AQS 也是基于这玩意）

`StampedLock` 提供了三种模式的读写控制模式：读锁、写锁和乐观读。

- **写锁**：独占锁，一把锁只能被一个线程获得。当一个线程获取写锁后，其他请求读锁和写锁的线程必须等待。类似于 `ReentrantReadWriteLock` 的写锁，不过这里的写锁是不可重入的。
- **读锁** （悲观读）：共享锁，没有线程获取写锁的情况下，多个线程可以同时持有读锁。如果己经有线程持有写锁，则其他线程请求获取该读锁会被阻塞。类似于 `ReentrantReadWriteLock` 的读锁，不过这里的读锁是不可重入的。
- **乐观读**：允许多个线程获取乐观读以及读锁。同时允许一个写线程获取写锁。

## [StampedLock 的性能为什么更好？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#stampedlock-的性能为什么更好)

相比于传统读写锁多出来的乐观读是`StampedLock`比 `ReadWriteLock` 性能更好的关键原因。

`StampedLock` 的乐观读允许一个写线程获取写锁，所以不会导致所有写线程阻塞，也就是当读多写少的时候，写线程有机会获取写锁，减少了线程饥饿的问题，吞吐量大大提高。

## [StampedLock 适合什么场景？](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#stampedlock-适合什么场景)

和 `ReentrantReadWriteLock` 一样，`StampedLock` 同样适合读多写少的业务场景，可以作为 `ReentrantReadWriteLock`的替代品，性能更好。

不过，需要注意的是`StampedLock`不可重入，不支持条件变量 `Condition`，对中断操作支持也不友好（使用不当容易导致 CPU 飙升）。如果你需要用到 `ReentrantLock` 的一些高级性能，就不太建议使用 `StampedLock` 了。

另外，`StampedLock` 性能虽好，但使用起来相对比较麻烦，一旦使用不当，就会出现生产问题。强烈建议你在使用`StampedLock` 之前，看看 [StampedLock 官方文档中的案例](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/locks/StampedLock.html)。

# [Atomic 原子类](https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html#atomic-原子类)

:question:

Atomic 原子类部分的内容我单独写了一篇文章来总结：[Atomic 原子类总结](https://javaguide.cn/java/concurrent/atomic-classes.html) 。

# [ThreadLocal](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#threadlocal)

## [ThreadLocal 有什么用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#threadlocal-有什么用)

**`ThreadLocal`类主要解决的就是让每个线程绑定自己的值，可以将`ThreadLocal`类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据**

## [如何使用 ThreadLocal？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#如何使用-threadlocal)

ThreadLocal中主要涉及`get`、`set`和`initialValue()`方法。`get`、`set`分别用于给这个ThreadLocal变量赋值，`initialValue`用于重写指定初始值。然后ThreadLocal需要指定泛型，泛型即为这个ThreadLocal维护的数据类型

:question:进行更加详细的补充[ThreadLocal详解](https://javaguide.cn/java/concurrent/threadlocal.html)

## [ThreadLocal 原理了解吗？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#threadlocal-原理了解吗)

`Thread` 类中有一个 `threadLocals` 和 一个 `inheritableThreadLocals` 变量，它们都是 `ThreadLocalMap` 类型的变量

> 我们可以把 `ThreadLocalMap` 理解为`ThreadLocal` 类实现的定制化的 `HashMap`。默认情况下这两个变量都是 null，只有当前线程调用 `ThreadLocal` 类的 `set`或`get`方法时才创建它们，实际上调用这两个方法的时候，我们调用的是`ThreadLocalMap`类对应的 `get()`、`set()`方法。

`threadLocals`保存的是当前线程的ThreadLocal变量，而`inheritableThread`变量保存的是该线程父线程的`inheritableThread`，以实现继承关系。

**每个`Thread`中都具备一个`ThreadLocalMap`，而`ThreadLocalMap`可以存储以`ThreadLocal`为 key ，Object 对象为 value 的键值对**

![image-20240510165657574](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240510165657574.png)

## [ThreadLocal 内存泄露问题是怎么导致的？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#threadlocal-内存泄露问题是怎么导致的)

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用，而 value 是强引用。

所以，如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。`ThreadLocalMap` 中就会出现 key 为 null 的 Entry。假如我们不做任何措施的话，value 永远无法被 GC 回收，这个时候就可能会产生内存泄露。

`ThreadLocalMap` 实现中已经考虑了这种情况，在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录。使用完 `ThreadLocal`方法后最好手动调用`remove()`方法（remove方法就是移除掉threadLocals中当前线程ThreadLocal的键值对）

> 弱引用不管空间够不够，都会在GC扫描的时候被回收
>
> 弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java 虚拟机就会把这个弱引用加入到与之关联的引用队列中。

# [线程池](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#线程池)

## [什么是线程池?](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#什么是线程池)

顾名思义，线程池就是管理一系列线程的资源池。当有任务要处理时，直接从线程池中获取线程来处理，处理完之后线程并不会立即被销毁，而是等待下一个任务。

## [为什么要用线程池？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#为什么要用线程池)

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

## [如何创建线程池？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#如何创建线程池)

**方式一：通过`ThreadPoolExecutor`构造函数来创建（推荐）。**

**方式二：通过 `Executor` 框架的工具类 `Executors` 来创建。**

- `FixedThreadPool`：固定线程数量的线程池。该线程池中的线程数量始终不变。当有一个新的任务提交时，线程池中若有空闲线程，则立即执行。若没有，则新的任务会被暂存在一个任务队列（无界队列`LinkedBlockQueue`）中，待有线程空闲时，便处理在任务队列中的任务。
- `SingleThreadExecutor`： 只有一个线程的线程池。若多余一个任务被提交到该线程池，任务会被保存在一个任务队列（无界队列`LinkedBlockQueue`）中，待线程空闲，按先入先出的顺序执行队列中的任务。
- `CachedThreadPool`： 可根据实际情况调整线程数量的线程池。线程池的线程数量不确定，但若有空闲线程可以复用，则会优先使用可复用的线程。若所有线程均在工作，又有新的任务提交，则会创建新的线程处理任务。所有线程在当前任务执行完毕后，将返回线程池进行复用。
- `ScheduledThreadPool`：给定的延迟后运行任务或者定期执行任务的线程池

## [为什么不推荐使用内置线程池？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#为什么不推荐使用内置线程池)

《阿里巴巴 Java 开发手册》中强制线程池不允许使用 `Executors` 去创建，而是通过 `ThreadPoolExecutor` 构造函数的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险

- `FixedThreadPool` 和 `SingleThreadExecutor`:使用的是无界的 `LinkedBlockingQueue`，任务队列最大长度为 `Integer.MAX_VALUE`,可能堆积大量的请求，从而导致 OOM。
- `CachedThreadPool`:使用的是同步队列 `SynchronousQueue`没有容量, 允许创建的线程数量为 `Integer.MAX_VALUE` ，如果任务数量过多且执行速度较慢，可能会创建大量的线程，从而导致 OOM。
- `ScheduledThreadPool` 和 `SingleThreadScheduledExecutor`:使用的无界的延迟阻塞队列`DelayedWorkQueue`，任务队列最大长度为 `Integer.MAX_VALUE`,可能堆积大量的请求，从而导致 OOM

## [线程池常见参数有哪些？如何解释？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#线程池常见参数有哪些-如何解释)

![image-20240511101608063](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511101608063.png)

- `corePoolSize` : 任务队列未达到队列容量时，最大可以同时运行的线程数量。
- `maximumPoolSize` : 任务队列中存放的任务达到队列容量的时候，当前可以同时运行的线程数量变为最大线程数。
- `workQueue`: 新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，新任务就会被存放在队列中。

> 先放核心线程，然后队列，最后扩大核心线程数到最大线程数

- `keepAliveTime`:线程池中的线程数量大于 `corePoolSize` 的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了 `keepAliveTime`才会被回收销毁。
- `unit` : `keepAliveTime` 参数的时间单位。
- `threadFactory` :executor 创建新线程的时候会用到。
- `handler` :拒绝策略

## [线程池的拒绝策略有哪些？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#线程池的拒绝策略有哪些)

同时运行的线程数量达到最大线程数量并且队列也已经被放满了任务时，`ThreadPoolExecutor` 定义一些策略:

- `ThreadPoolExecutor.AbortPolicy`：抛出 `RejectedExecutionException`来拒绝新任务的处理（默认）

- `ThreadPoolExecutor.CallerRunsPolicy`：调用执行自己的线程运行任务，也就是直接在==调用`execute`方法的线程中==运行(`run`)被拒绝的任务，如果执行程序已关闭，则会丢弃该任务。

  > 因此这种策略会降低对于新任务提交速度，影响程序的整体性能。如果你的应用程序可以承受此延迟并且你要求任何一个任务请求都要被执行的话，你可以选择这个策略。

- `ThreadPoolExecutor.DiscardPolicy`：不处理新任务，直接丢弃掉。

- `ThreadPoolExecutor.DiscardOldestPolicy`：此策略将丢弃最早的未处理的任务请求。

## [如果不允许丢弃任务任务，应该选择哪个拒绝策略？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#如果不允许丢弃任务任务-应该选择哪个拒绝策略)

`CallerRunsPolicy`源码

```java
public static class CallerRunsPolicy implements RejectedExecutionHandler {

        public CallerRunsPolicy() { }

        public void rejectedExecution(Runnable r, ThreadPoolExecutor e) {
            //只要当前程序没有关闭，就用执行execute方法的线程执行该任务
            if (!e.isShutdown()) {
                r.run();
            }
        }
    }

```

## [CallerRunsPolicy 拒绝策略有什么风险？如何解决？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#callerrunspolicy-拒绝策略有什么风险-如何解决)

如果走到`CallerRunsPolicy`的任务是个非常耗时的任务，且处理提交任务的线程是主线程，可能会导致主线程阻塞，影响程序的正常运行

从线程的调度入手避免有任务不被处理

![image-20240511103231258](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511103231258.png)

还可以使用**任务持久化**的思路，这里所谓的任务持久化，包括但不限于:

1. 设计一张任务表间任务存储到 MySQL 数据库中。
2. `Redis`缓存任务。
3. 将任务提交到消息队列中

用其他手段暂时存储阻塞队列中的任务

## [线程池常用的阻塞队列有哪些？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#线程池常用的阻塞队列有哪些)

- 容量为 `Integer.MAX_VALUE` 的 `LinkedBlockingQueue`（无界队列）：`FixedThreadPool` 和 `SingleThreadExector` 。`FixedThreadPool`最多只能创建核心线程数的线程（核心线程数和最大线程数相等），`SingleThreadExector`只能创建一个线程（核心线程数和最大线程数都是 1）
- `SynchronousQueue`（同步队列）：`CachedThreadPool` 。`SynchronousQueue` 没有容量，不存储元素，目的是保证对于提交的任务，如果有空闲线程，则使用空闲线程来处理；否则新建一个线程来处理任务。也就是说，`CachedThreadPool` 的最大线程数是 `Integer.MAX_VALUE` ，可以理解为线程数是可以无限扩展的，可能会创建大量线程，从而导致 OOM。

> 当一个线程尝试将元素放入 `SynchronousQueue` 时，该元素并不会真正存储在队列中，而是立即被另一个线程消费

- `DelayedWorkQueue`（延迟阻塞队列）：`ScheduledThreadPool` 和 `SingleThreadScheduledExecutor` 。`DelayedWorkQueue` 的内部元素并不是按照放入的时间排序，而是会按照延迟的时间长短对任务进行排序，内部采用的是“堆”的数据结构，可以保证每次出队的任务都是当前队列中执行时间最靠前的。`DelayedWorkQueue` 添加元素满了之后会自动扩容原来容量的 1/2，即永远不会阻塞，最大扩容可达 `Integer.MAX_VALUE`，所以最多只能创建核心线程数的线程

## [线程池处理任务的流程了解吗？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#线程池处理任务的流程了解吗)

![image-20240511103753413](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511103753413.png)

1. 如果当前运行的线程数小于核心线程数，那么就会新建一个线程来执行任务。
2. 如果当前运行的线程数等于或大于核心线程数，但是小于最大线程数，那么就把该任务放入到任务队列里等待执行。
3. 如果向任务队列投放任务失败（任务队列已经满了），但是当前运行的线程数是小于最大线程数的，就新建一个线程来执行任务。
4. 如果当前运行的线程数已经等同于最大线程数了，新建线程将会使当前运行的线程超出最大线程数，那么当前任务会被拒绝，拒绝策略会调用`RejectedExecutionHandler.rejectedExecution()`方法

## [线程池中线程异常后，销毁还是复用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#线程池中线程异常后-销毁还是复用)

先说结论，需要分两种情况：

- **使用`execute()`提交任务**：当任务通过`execute()`提交到线程池并在执行过程中抛出异常时，如果这个异常没有在任务内被捕获，那么该异常会导致当前线程终止，并且异常会被打印到控制台或日志文件中。线程池会检测到这种线程终止，并创建一个新线程来替换它，从而保持配置的线程数不变。
- **使用`submit()`提交任务**：对于通过`submit()`提交的任务，如果在任务执行中发生异常，这个异常不会直接打印出来。相反，异常会被封装在由`submit()`返回的`Future`对象中。当调用`Future.get()`方法时，可以捕获到一个`ExecutionException`。在这种情况下，线程不会因为异常而终止，它会继续存在于线程池中，准备执行后续的任务

使用`execute()`时，未捕获异常导致线程终止，线程池创建新线程替代；使用`submit()`时，异常被封装在`Future`中，线程继续复用。

这种设计允许`submit()`提供更灵活的错误处理机制，因为它允许调用者决定如何处理异常，而`execute()`则适用于那些不需要关注执行结果的场景。

## [如何给线程池命名？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#如何给线程池命名)

默认情况下创建的线程名字类似 `pool-1-thread-n` 这样的，没有业务含义，不利于我们定位问题

**1、利用 guava 的 `ThreadFactoryBuilder`**

**2、自己实现 `ThreadFactory`。**

## [如何设定线程池的大小？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#如何设定线程池的大小)

`最佳线程数 = N（CPU 核心数）∗（1+WT（线程等待时间）/ST（线程计算时间））`，其中 `WT（线程等待时间）=线程运行总时间 - ST（线程计算时间）`

- **CPU 密集型任务(N+1)：** 这种任务消耗的主要是 CPU 资源，可以将线程数设置为 N（CPU 核心数）+1。比 CPU 核心数多出来的一个线程是为了防止线程偶发的缺页中断，或者其它原因导致的任务暂停而带来的影响。一旦任务暂停，CPU 就会处于空闲状态，而在这种情况下多出来的一个线程就可以充分利用 CPU 的空闲时间。`WT/ST 接近0`
- **I/O 密集型任务(2N)：** 这种任务应用起来，系统会用大部分的时间来处理 I/O 交互，而线程在处理 I/O 的时间段内不会占用 CPU 来处理，这时就可以将 CPU 交出给其它线程使用。因此在 I/O 密集型任务的应用中，我们可以多配置一些线程，具体的计算方法是 2N。

> 线程等待时间很长，但未避免线程创建过多，采用2N

## [如何动态修改线程池的参数？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#如何动态修改线程池的参数)

![image-20240511105653252](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511105653252.png)

 程序运行期间的时候，我们调用 `setCorePoolSize()`这个方法的话，线程池会首先判断当前工作线程数是否大于`corePoolSize`，如果大于的话就会回收工作线程。

不支持动态指定队列长度，美团的方式是自定义了一个叫做 `ResizableCapacityLinkedBlockIngQueue` 的队列（主要就是把`LinkedBlockingQueue`的 capacity 字段的 final 关键字修饰给去掉了，让它变为可变的）

## [如何设计一个能够根据任务的优先级来执行的线程池？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#如何设计一个能够根据任务的优先级来执行的线程池)

使用 `PriorityBlockingQueue` （优先级阻塞队列）作为任务队列

风险：

1. `PriorityBlockingQueue` 是无界的，可能堆积大量的请求，从而导致 OOM。
2. 可能会导致饥饿问题，即低优先级的任务长时间得不到执行。
3. 由于需要对队列中的元素进行排序操作以及保证线程安全（并发控制采用的是可重入锁 `ReentrantLock`），因此会降低性能。

解决方法：

1. 继承`PriorityBlockingQueue` 并重写一下 `offer` 方法，超过指定数量返回false
2. 优化设计，例如，等待时间过长的任务会被移除并重新添加到队列中，但是优先级会被提升
3. 无法避免，毕竟要排序

# [Future](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#future)

## [Future 类有什么用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#future-类有什么用)

`Future` 类是异步思想的典型运用，是Future模式的体现，Future模式不是java独有的

在 Java 中，`Future` 类只是一个泛型接口，位于 `java.util.concurrent` 包下，其中定义了 5 个方法，主要包括下面这 4 个功能：

- 取消任务；
- 判断任务是否被取消;
- 判断任务是否已经执行完成;
- 获取任务执行结果

```java
// V 代表了Future执行的任务返回值的类型
public interface Future<V> {
    // 取消任务执行
    // 成功取消返回 true，否则返回 false
    boolean cancel(boolean mayInterruptIfRunning);
    // 判断任务是否被取消
    boolean isCancelled();
    // 判断任务是否已经执行完成
    boolean isDone();
    // 获取任务执行结果
    V get() throws InterruptedException, ExecutionException;
    // 指定时间内没有返回计算结果就抛出 TimeOutException 异常
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutExceptio
}
```

## [Callable 和 Future 有什么关系？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#callable-和-future-有什么关系)

`FutureTask` 提供了 `Future` 接口的基本实现，常用来封装 `Callable` 和 `Runnable`，具有取消任务、查看任务是否执行完成以及获取任务执行结果的方法

`ExecutorService.submit()` 方法返回的其实就是 `Future` 的实现类 `FutureTask` 。

`FutureTask` 不光实现了 `Future`接口，还实现了`Runnable` 接口，因此可以作为任务直接被线程执行

![image-20240511111223786](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511111223786.png)

`FutureTask` 有两个构造函数，可传入 `Callable` 或者 `Runnable` 对象。实际上，传入 `Runnable` 对象也会在方法内部转换为`Callable` 对象

总结：`FutureTask`相当于对`Callable` 进行了封装，管理着任务执行的情况，存储了 `Callable` 的 `call` 方法（相当于Runnable的run方法）的任务执行结果

[一个例子](https://github.com/QZ-z/QPublicText/blob/79390b57760eed2d26854d87e583e6c9d5a44002/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/javase/19%E5%A4%9A%E7%BA%BF%E7%A8%8B.md#%E5%AE%9E%E7%8E%B0%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%AC%AC%E4%B8%89%E7%A7%8D%E6%96%B9%E5%BC%8F)

## [CompletableFuture 类有什么用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#completablefuture-类有什么用)

`Future` 在实际使用过程中存在一些局限性比如

- 不支持异步任务的编排组合
- 获取计算结果的 `get()` 方法为阻塞调用

`CompletableFuture`java8之后引入

- 提供了更为好用和强大的 `Future` 特性之外
- 支持函数式编程
- 支持异步任务编排组合（可以将多个异步任务串联起来，组成一个完整的链式调用）

`CompletableFuture` 同时实现了 `Future` 和 `CompletionStage` 接口。

![image-20240511111223786](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511111223786.png)

`CompletionStage` 接口描述了一个异步计算的阶段。很多计算可以分成多个阶段或步骤，此时可以通过它将所有步骤组合起来，形成异步计算的流水线，`CompletableFuture` 的函数式能力就是这个接口赋予的

# [AQS](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#aqs)

## [AQS 是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#aqs-是什么)

AQS 的全称为 `AbstractQueuedSynchronizer` ，翻译过来的意思就是抽象队列同步器。这个类在 `java.util.concurrent.locks` 包下面

AQS 就是一个抽象类，主要用来构建锁和同步器，为构建锁和同步器提供了一些通用的功能实现。

## [AQS 的原理是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#aqs-的原理是什么)

核心思想：

- 如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。
- 如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是用 **CLH 队列锁** 实现的

CLH 锁是对自旋锁的一种改进，是一个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系），暂时获取不到锁的线程将被加入到该队列中。AQS 将每条请求共享资源的线程封装成一个 CLH 队列锁的一个结点（Node）来实现锁的分配.

在 CLH 队列锁中，一个节点表示一个线程，它保存着线程的引用（thread）、 当前节点在队列中的状态（waitStatus）、前驱节点（prev）、后继节点（next）。

![image-20240511155840941](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511155840941.png)

核心原理图：

AQS 使用 **int 成员变量 `state` 表示同步状态**，通过内置的 **FIFO 线程等待队列** 来完成获取资源线程的排队工作

`state` 变量由 `volatile` 修饰，用于展示当前临界资源的获锁情况

状态信息 `state` 可以通过 `protected` 类型的`getState()`、`setState()`和`compareAndSetState()` 进行操作。并且，这几个方法都是 `final` 修饰的，在子类中无法被重写

![image-20240511155952152](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511155952152.png)

以可重入的互斥锁 `ReentrantLock` 为例，它的内部维护了一个 `state` 变量，用来表示锁的占用状态。

`state` 的初始值为 0，表示锁处于未锁定状态。当线程 A 调用 `lock()` 方法时，会尝试通过 `tryAcquire()` 方法独占该锁，并让 `state` 的值加 1。如果成功了，那么线程 A 就获取到了锁。如果失败了，那么线程 A 就会被加入到一个等待队列（CLH 队列）中，直到其他线程释放该锁。

假设线程 A 获取锁成功了，释放锁之前，A 线程自己是可以重复获取此锁的（`state` 会累加）。这就是可重入性的体现：一个线程可以多次获取同一个锁而不会被阻塞。但是，这也意味着，一个线程必须释放与获取的次数相同的锁，才能让 `state` 的值回到 0，也就是让锁恢复到未锁定状态。只有这样，其他等待的线程才能有机会获取该锁。

## [AQS 资源共享方式](https://javaguide.cn/java/concurrent/aqs.html#aqs-资源共享方式)

AQS 定义两种资源共享方式：`Exclusive`（独占，只有一个线程能执行，如`ReentrantLock`）和`Share`（共享，多个线程可同时执行，如`Semaphore`/`CountDownLatch`）。

一般来说，自定义同步器的共享方式要么是独占，要么是共享，他们也只需实现`tryAcquire-tryRelease`、`tryAcquireShared-tryReleaseShared`中的一种即可。但 AQS 也支持自定义同步器同时实现独占和共享两种方式，如`ReentrantReadWriteLock`。

## [Semaphore(信号量) 有什么用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#semaphore-有什么用)

`synchronized` 和 `ReentrantLock` 都是一次只允许一个线程访问某个资源，而`Semaphore`(信号量)可以用来控制同时访问特定资源的线程数量

```java
// 初始共享资源数量
final Semaphore semaphore = new Semaphore(5);
// 获取1个许可
semaphore.acquire();
// 释放1个许可
semaphore.release();
```

当初始的资源个数为 1 的时候，`Semaphore` 退化为排他锁。

`Semaphore` 有公平和非公平模式

```java
public Semaphore(int permits) {
    sync = new NonfairSync(permits);
}

public Semaphore(int permits, boolean fair) {
    sync = fair ? new FairSync(permits) : new NonfairSync(permits);
}
```

可用于单机限流，实际项目中推荐使用redis+lua

## [Semaphore 的原理是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#semaphore-的原理是什么)

`Semaphore` 是共享锁的一种实现，它默认构造 AQS 的 `state` 值为 `permits`（资源数量）

`semaphore.acquire()` ，线程尝试获取许可证，如果 `state >= 0` 的话，则表示可以获取成功。如果获取成功的话，使用 CAS 操作去修改 `state` 的值 `state=state-1`，前边所说`state >= 0`获取成功中的`state`其实是CAS更新后的返回值。如果 `state<0` 的话，则表示许可证数量不足。此时会创建一个 Node 节点加入阻塞队列，挂起当前线程。

`semaphore.release();` ，线程尝试释放许可证，并使用 CAS 操作去修改 `state` 的值 `state=state+1`。释放许可证成功之后，同时会唤醒同步队列中的一个线程。被唤醒的线程会重新尝试去修改 `state` 的值 `state=state-1` ，如果 `state>=0` 则获取令牌成功，否则重新进入阻塞队列，挂起线程。

## [CountDownLatch(倒计时器) 有什么用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#countdownlatch-有什么用)

`CountDownLatch` 允许 `count` 个线程阻塞在一个地方，直至所有线程的任务都执行完毕。

`CountDownLatch` 是一次性的，计数器的值只能在构造方法中初始化一次，之后没有任何机制再次对其设置值，当 `CountDownLatch` 使用完毕后，它不能再次被使用。

## [CountDownLatch 的原理是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#countdownlatch-的原理是什么)

`CountDownLatch` 是共享锁的一种实现,它默认构造 AQS 的 `state` 值为 `count`。当线程使用 `countDown()` 方法时,其实使用了`tryReleaseShared`方法以 CAS 的操作来减少 `state`,直至 `state` 为 0 。

当`CountDownLatch` 对象调用 `await()` 方法的时候，如果 `state` 不为 0，那就证明任务还没有执行完毕，`await()` 方法就会一直阻塞，也就是说 `await()` 方法之后的语句不会被执行。直到`count` 个线程调用了`countDown()`使 state 值被减为 0，或者调用`await()`的线程被中断，该线程才会从阻塞中被唤醒，`await()` 方法之后的语句得到执行

## [用过 CountDownLatch 么？什么场景下用的？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#用过-countdownlatch-么-什么场景下用的)

 例如，读取处理 6 个文件，这 6 个任务都是没有执行顺序依赖的任务，但是我们需要返回给用户的时候将这几个文件的处理的结果进行统计整理。

```java
public class CountDownLatchExample1 {
    // 处理文件的数量
    private static final int threadCount = 6;

    public static void main(String[] args) throws InterruptedException {
        // 创建一个具有固定线程数量的线程池对象（推荐使用构造方法创建）
        ExecutorService threadPool = Executors.newFixedThreadPool(10);
        final CountDownLatch countDownLatch = new CountDownLatch(threadCount);
        for (int i = 0; i < threadCount; i++) {
            final int threadnum = i;
            threadPool.execute(() -> {
                try {
                    //处理文件的业务操作
                    //......
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    //表示一个文件已经被完成
                    countDownLatch.countDown();
                }

            });
        }
        countDownLatch.await();
        threadPool.shutdown();
        System.out.println("finish");
    }
}
```

改进：可以使用Java8 的 `CompletableFuture` ，它提供了很多对多线程友好的方法，使用它可以很方便地为我们编写多线程程序，什么异步、串行、并行或者等待所有线程执行完任务什么的都非常方便。

```java
CompletableFuture<Void> task1 =
    CompletableFuture.supplyAsync(()->{
        //自定义业务操作
    });
......
CompletableFuture<Void> task6 =
    CompletableFuture.supplyAsync(()->{
    //自定义业务操作
    });
......
CompletableFuture<Void> headerFuture=CompletableFuture.allOf(task1,.....,task6);

try {
    headerFuture.join();
} catch (Exception ex) {
    //......
}
System.out.println("all done. ");
```

## [CyclicBarrier (循环栅栏)有什么用？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#cyclicbarrier-有什么用)

`CyclicBarrier` 和 `CountDownLatch` 非常类似，它也可以实现线程间的技术等待，但是它的功能比 `CountDownLatch` 更加复杂和强大。主要应用场景和 `CountDownLatch` 类似。

> `CountDownLatch` 的实现是基于 AQS 的，而 `CycliBarrier` 是基于 `ReentrantLock`(`ReentrantLock` 也属于 AQS 同步器)和 `Condition` 的。

`CyclicBarrier` 的字面意思是可循环使用（Cyclic）的屏障（Barrier）。它要做的事情是：让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。

## [CyclicBarrier 的原理是什么？](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#cyclicbarrier-的原理是什么)

`CyclicBarrier` 内部通过一个 `count` 变量作为计数器，`count` 的初始值为 `parties` 属性的初始化值，每当一个线程到了栅栏这里了，那么就将计数器减 1。如果 count 值为 0 了，表示这是这一代最后一个线程到达栅栏，就尝试执行我们构造方法中输入的任务。

# [虚拟线程](https://javaguide.cn/java/concurrent/java-concurrent-questions-03.html#虚拟线程)

java21引入

## [什么是虚拟线程？](https://javaguide.cn/java/concurrent/virtual-thread.html#什么是虚拟线程)

虚拟线程（Virtual Thread）是 JDK 而不是 OS 实现的轻量级线程(Lightweight Process，LWP），由 JVM 调度。许多虚拟线程共享同一个操作系统线程，虚拟线程的数量可以远大于操作系统线程的数量。

## [虚拟线程和平台线程有什么关系？](https://javaguide.cn/java/concurrent/virtual-thread.html#虚拟线程和平台线程有什么关系)

在引入虚拟线程之前，`java.lang.Thread` 包已经支持所谓的平台线程（Platform Thread），也就是没有虚拟线程之前，我们一直使用的线程。

JVM 调度程序通过平台线程（载体线程）来管理虚拟线程，一个平台线程可以在不同的时间执行不同的虚拟线程（多个虚拟线程挂载在一个平台线程上），当虚拟线程被阻塞或等待时，平台线程可以切换到执行另一个虚拟线程。

![image-20240511154855757](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240511154855757.png)

> 在 Windows 和 Linux 等主流操作系统中，Java 线程采用的是一对一的线程模型，也就是一个平台线程对应一个系统内核线程。Solaris 系统是一个特例，HotSpot VM 在 Solaris 上支持多对多和一对一

## [虚拟线程有什么优点和缺点？](https://javaguide.cn/java/concurrent/virtual-thread.html#虚拟线程有什么优点和缺点)

### [优点](https://javaguide.cn/java/concurrent/virtual-thread.html#优点)

- 非常轻量级：可以在单个线程中创建成百上千个虚拟线程而不会导致过多的线程创建和上下文切换。
- 简化异步编程： 虚拟线程可以简化异步编程，使代码更易于理解和维护。它可以将异步代码编写得更像同步代码，避免了回调地狱（Callback Hell）。
- 减少资源开销： 相比于操作系统线程，虚拟线程的资源开销更小。本质上是提高了线程的执行效率，从而减少线程资源的创建和上下文切换。

### [缺点](https://javaguide.cn/java/concurrent/virtual-thread.html#缺点)

- 不适用于计算密集型任务： 虚拟线程适用于 I/O 密集型任务，但不适用于计算密集型任务，因为密集型计算始终需要 CPU 资源作为支持。
- 依赖于语言或库的支持： 协程需要编程语言或库提供支持。不是所有编程语言都原生支持协程。比如 Java 实现的虚拟线程
