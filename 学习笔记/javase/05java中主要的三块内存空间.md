[toc]
<font size=3>
# JVM中主要的三块内存空间

## 方法区
- 类加载器classloader，将硬盘上的**xxx.class字节码**文件装载到JVM当中的时候，会将字节码文件存放到**方法区**中。也就是说**方法区**中存放的是**代码片段**
- **静态变量**和**静态代码块**都在这里存储

## 栈
- 方法被调用的时候，该方法需要的**内存空间**从栈中分配
- 方法被调用的时候在栈中分配，这是**压栈**
- 方法执行完毕，释放空间**弹栈**
>栈中存储**方法运行过程中**需要的内存，和该方法的**局部变量**

## 堆区
- 凡是通过new运算符创建的对象，都存储在**堆内存**当中。
- new运算符的**作用**就是在堆内存中开辟一块空间
- 实例化的对象保存的是**堆内存**里边的地址，这样的变量也称作**引用**
- java中程序员无权利操作**堆内存**
>Student s1 = 0x1234； s1就是引用  &emsp; 0x1234指向堆内存的一个地址
><br> **垃圾回收器GC**主要回收的是堆内存中的垃圾数据。当没有任何引用指向该对象的时候，会被回收

## 内存图

![image-20231019220615018](https://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20231019220615018.png)

1. **程序计数器**：
   1. 概念：可以看做当前线程所执行的字节码的行号指示器。
   2. 特点：线程私有的内存

2. **java 虚拟机栈（重点）**：

   1. 概念：描述的是 java 方法执行的内存模型。（每个方法在执行的时候会创建一个**栈帧**，用于存储**局部变量**表，操作数栈，动态链接，方法出口等信息。每个方法从调用直至完成的过程，就对应一个栈帧从入栈到出栈的过程。）

   2.  特 点 ： 线 程 私 有， 生 命 周期 和 线 程 相同 。

      > 这 个 区域 会 出 现 两种 异 常 ：
      >
      > StackOverflowError 异常： 若线 程请求 的深 度大于 虚拟 机所允 许的 深度 。OutOfMemoryError 异常：若虚拟机可以动态扩展，如果扩展时无法申请到足够的内存。

3. **本地方法栈**：
   1. 概念：它与虚拟机栈所发挥的作用是相似的，区别是 java 虚拟机栈为执行 java 方法服务，而本地方法栈是为本地方法服务。
   2. 特点：线程私有，也会抛出两类异常：StackOverflowError 和 OutOfMemoryError。

4. **java 堆（重点）**：
   1. 概念：是被所有线程共享的一块区域，在虚拟机启动时创建。
   2. 特点：线程共享，存放的是**对象实例**（所有的对象实例和数组），GC 管理的主要区域。可以处于物理上不连续的内存空间。

5. **方法区（重点）**：
   1. 概念：存储已被虚拟机加载的**类信息、常量、静态变量**，即时编译器编译后的代码等数据。
   2. 特点：线程共享的区域，抛出异常 OutOfMemory 异常：当方法区无法满足内存分配需求的时候。

</font>