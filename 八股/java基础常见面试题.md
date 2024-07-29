

# [基础概念与常识](#基础概念与常识)

## [Java 语言有哪些特点?](#java-语言有哪些特点)

1. 简单易学；
2. 面向对象（封装，继承，多态）；
3. 平台无关性（ Java 虚拟机实现平台无关性）；
4. 支持多线程（ C++ 语言没有内置的多线程机制，因此必须调用操作系统的多线程功能来进行多线程程序设计，而 Java 语言却提供了多线程支持）；

> C++11 开始（2011 年的时候）,C++就引入了多线程库，在 windows、linux、macos 都可以使用`std::thread`和`std::async`来创建线程

5. 可靠性（具备异常处理和自动内存管理机制）；
6. 安全性（Java 语言本身的设计就提供了多重安全防护机制如访问权限修饰符、限制程序直接访问操作系统资源）；
7. 高效性（通过 Just In Time 编译器等技术的优化，Java 语言的运行效率还是非常不错的）；
8. 支持网络编程并且很方便；
9. 编译与解释并存；
10. ……

## [Java SE vs Java EE](#java-se-vs-java-ee)

- Java SE（Java Platform，Standard Edition）: Java 平台标准版，Java 编程语言的基础，它包含了支持 Java 应用程序开发和运行的核心类库以及虚拟机等核心组件。Java SE 可以用于构建桌面应用程序或简单的服务器应用程序。
- Java EE（Java Platform, Enterprise Edition ）：Java 平台企业版，建立在 Java SE 的基础上，包含了支持企业级应用程序开发和部署的标准和规范（比如 Servlet、JSP、EJB、JDBC、JPA、JTA、JavaMail、JMS）。 Java EE 可以用于构建分布式、可移植、健壮、可伸缩和安全的服务端 Java 应用程序，例如 Web 应用程序。

简单来说，Java SE 是 Java 的基础版本，Java EE 是 Java 的高级版本。Java SE 更适合开发桌面应用程序或简单的服务器应用程序，Java EE 更适合开发复杂的企业级应用程序或 Web 应用程序。

除了 Java SE 和 Java EE，还有一个 Java ME（Java Platform，Micro Edition）。Java ME 是 Java 的微型版本，主要用于开发嵌入式消费电子设备的应用程序，例如手机、PDA、机顶盒、冰箱、空调等。Java ME 无需重点关注，知道有这个东西就好了，现在已经用不上了

### [JVM vs JDK vs JRE](#jvm-vs-jdk-vs-jre)

### [JVM](#jvm)

Java 虚拟机（JVM）是运行 Java 字节码的虚拟机。JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，它们都会给出相同的结果。字节码和不同系统的 JVM 实现是 Java 语言“一次编译，随处可以运行”的关键所在。

**JVM 并不是只有一种！只要满足 JVM 规范，每个公司、组织或者个人都可以开发自己的专属 JVM。**

### [JDK 和 JRE](#jdk-和-jre)

JRE 是 Java 运行时环境，仅包含 Java 应用程序的运行时环境和必要的类库。

而 JDK 则包含了 JRE，同时还包括了 javac、javadoc、jdb、jconsole、javap 等工具，可以用于 Java 应用程序的开发和调试。

![image-20240425180938843](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425180938843.png)

不过，从 JDK 9 开始，就不需要区分 JDK 和 JRE 的关系了，取而代之的是模块系统（JDK 被重新组织成 94 个模块）+ jlink工具

## [什么是字节码?采用字节码的好处是什么?](#什么是字节码-采用字节码的好处是什么)

JVM可以理解的代码就叫字节码（.class文件），只面向虚拟机

使用字节码：

- 解决了传统解释性语言执行效率低的问题
- 同时保留了解释型语言可移植的特点

![image-20240425181712974](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425181712974.png)

 **JIT（Just in Time Compilation）** 编译器， 属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。（针对热点代码）

> java编译与解释共存说法的来源之一

## [为什么说 Java 语言“编译与解释并存”？]()

因为 Java 程序要经过先编译，后解释两个步骤

- 由 Java 编写的程序需要先经过编译步骤，生成字节码（`.class` 文件）
- 这种字节码必须由 Java 解释器来解释执行。

## [Java 和 C++ 的区别?](#java-和-c-的区别)

- Java 不提供指针来直接访问内存，程序内存更加安全
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
- Java 有自动内存管理垃圾回收机制(GC)，不需要程序员手动释放无用内存。
- C ++同时支持方法重载和操作符重载，但是 Java 只支持方法重载（操作符重载增加了复杂性，这与 Java 最初的设计思想不符）。
- …… 

# [基本数据类型](https://javaguide.cn/java/basis/java-basic-questions-01.html#基本数据类型)

## [Java 中的几种基本数据类型了解么？](https://javaguide.cn/java/basis/java-basic-questions-01.html#java-中的几种基本数据类型了解么)

| 数据类型 | 中文名       | 字节数 | 取值范围                                                     | 缺省默认值 |
| -------- | ------------ | ------ | ------------------------------------------------------------ | ---------- |
| byte     | 字节型       | 1      | -128~127                                                     | 0          |
| short    | 短整型       | 2      | -32768~32767                                                 | 0          |
| int      | 整型         | 4      | -2147483648~2147483647($2.147483647 * 10 ^ 9$)               | 0          |
| long     | 长整型       | 8      | [-2^63^~2^63^-1] (~9,223,372,036,854,775,807) (数量级是$10^{18}$) | 0L         |
| float    | 单精度浮点数 | 4      |                                                              | 0.0f       |
| double   | 双精度浮点数 | 8      |                                                              | 0.0        |
| boolean  | 布尔型       | 1      | true false                                                   | false      |
| char     | 字符型       | 2      | 0~65535                                                      | '\\u0000'  |

> char在java中是两个字节，但字符’a‘在windows操作系统中占用一个字节，’中‘在windows操作系统中占用两个字节

## [基本类型和包装类型的区别？](https://javaguide.cn/java/basis/java-basic-questions-01.html#基本类型和包装类型的区别)

**用途**：

- 除了定义一些常量和局部变量之外，其他很少用基本数据类型。
- 包装类型可用于泛型，而基本类型不可以。

**存储方式**：

- 基本类型：虚拟机栈的局部变量表（局部变量），堆中（未被`static`修饰的成员变量）

> java7及之后，静态变量从方法区的永久代移动到堆中的`Class`对象中

- 包装类型：几乎所有对象实例存在堆中

> JIT会分析，如果对象没有逃逸到方法外，则通过标量替换来实现栈上分配

**占用空间**：基本数据类型占用空间小

**默认值**：

- 成员变量包装类型不赋值就是 `null` 
- 基本类型有默认值且不是 `null`。

**比较方式**：基本数据类型使用`==`，包装数据类型使用`equals()`方法

## [包装类型的缓存机制了解么？](#包装类型的缓存机制了解么)

`Byte`,`Short`,`Integer`,`Long` 这 4 种包装类默认创建了数值 **[-128，127]** 的相应类型的缓存数据，`Character` 创建了数值在 **[0,127]** 范围的缓存数据，`Boolean` 直接返回 `True` or `False`。

![image-20240425184527293](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425184527293.png)

## [自动装箱与拆箱了解吗？原理是什么？](https://javaguide.cn/java/basis/java-basic-questions-01.html#自动装箱与拆箱了解吗-原理是什么)

**什么是自动拆装箱？**

- **装箱**：将基本类型用它们对应的引用类型包装起来，调用`valueOf()`
- **拆箱**：将包装类型转换为基本数据类型，调用`xxxValue()`

## [浮点数精度丢失原因和解决方法]()

计算机表示数字宽度有限，无限循环小数保存的时候会被截断

`BigDecimal` 可以实现对浮点数的运算，不会造成精度丢失。

## [超过long整型的数据如何表示]()

`BigInteger` 内部使用 `int[]` 数组来存储任意大小的整形数据。但运算效率低

# [变量](https://javaguide.cn/java/basis/java-basic-questions-01.html#变量)

## [成员变量与局部变量的区别？](https://javaguide.cn/java/basis/java-basic-questions-01.html#成员变量与局部变量的区别)

语法：

- 成员变量属于类，可以被`public, private, static，final`等修饰符修饰
- 局部变量是在代码块或方法中定义的，或者是参数，不能被访问控制修饰符以及`static`修饰，只能被`final`修饰

存储：

- 静态成员变量属于类对象，其他成员变量属于实例，都存放在堆中
- 局部变量存放于栈内存

生存时间：

- 成员变量随着对象创建和消亡
- 局部变量随着方法调用结束消亡

默认值：

- 成员变量不赋值会有默认值（除了final修饰必须赋值）
- 局部变量不会自动赋值

## [字符型常量和字符串常量的区别?](#字符型常量和字符串常量的区别)

字符常量相当于一个整型值( ASCII 值),可以参加表达式运算; 字符串常量代表一个地址值(该字符串在内存中存放位置)。

⚠️ 注意 `char` 在 Java 中占两个字节。在windows下是一个字节

# [方法]()

## [重载和重写]()

| 区别       | 重载     | 重写                                             |
| ---------- | -------- | ------------------------------------------------ |
| 发生范围   | 同一个类 | 子类                                             |
| 参数       | 必须不同 | 必须相同                                         |
| 返回类型   | 可修改   | 子类比父类小或者相等                             |
| 异常       | 可修改   | 比父类小或者相等                                 |
| 访问修饰符 | 可修改   | 不能更低(public > protected > default > private) |
| 发生阶段   | 编译期   | 运行期                                           |

:star:重写返回类型

- void或基本数据类型，不可修改
- 引用类型，可以返回子类

:star:不能重写``private，final，static`修饰的方法，对`static`修饰的地方进行重写，实际上是对该方法的重新定义

# [面相对象基础]()

## [面向对象和面向过程的区别](https://javaguide.cn/java/basis/java-basic-questions-02.html#面向对象和面向过程的区别)

两者的主要区别在于解决问题的方式不同：

- 面向过程把解决问题的过程拆成一个个方法，通过一个个方法的执行解决问题。
- 面向对象会先抽象出对象，然后用对象执行方法的方式解决问题。

## [创建一个对象用什么运算符?对象实体与对象引用有何不同?](https://javaguide.cn/java/basis/java-basic-questions-02.html#创建一个对象用什么运算符-对象实体与对象引用有何不同)

new 运算符，new 创建对象实例（对象实例在堆内存中），对象引用指向对象实例（对象引用存放在栈内存中）。

## [构造方法有哪些特点？是否可被 override?](#构造方法有哪些特点-是否可被-override)

构造方法特点如下：

- 名字与类名相同。
- 没有返回值，但不能用 void 声明构造函数。
- 生成类的对象时自动执行，无需调用。

构造方法不能被 override（重写）,但是可以 overload（重载）,所以你可以看到一个类中有多个构造函数的情况

## [面向对象三大特征](https://javaguide.cn/java/basis/java-basic-questions-02.html#面向对象三大特征)

### [封装](https://javaguide.cn/java/basis/java-basic-questions-02.html#封装)

将对象信息隐藏在内部，不允许外部直接访问但提供一定的对外接口

### [继承](https://javaguide.cn/java/basis/java-basic-questions-02.html#继承)

继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。

通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。

**关于继承如下 3 点请记住：**

1. 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，**只是拥有**。
2. 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
3. 子类可以用自己的方式实现父类的方法。（以后介绍）。

### [多态](https://javaguide.cn/java/basis/java-basic-questions-02.html#多态)

一个对象具有多种状态，具体表现：父类的引用指向子类的实例

**多态的特点:**

- 对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
- 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
- 多态不能调用“只在子类存在但在父类不存在”的方法；
- 如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法

## [接口和抽象类有什么共同点和区别？](#接口和抽象类有什么共同点和区别)

**共同点**：

- 都不能被实例化。
- 都可以包含抽象方法。
- 都可以有默认实现的方法（Java 8 可以用 `default` 关键字在接口中定义默认方法）。

**区别**：

- 目的
  - 接口主要用于对类的行为进行约束，你实现了某个接口就具有了对应的行为。
  - 抽象类主要用于代码复用，强调的是所属关系。
- 使用限制
  - 一个类只能继承一个类
  - 但是可以实现多个接口。
- 成员变量
  - 接口中的成员变量只能是 `public static final` 类型的，不能被修改且必须有初始值
  - 而抽象类的成员变量默认 default，可在子类中被重新定义，也可被重新赋值

## [深拷贝和浅拷贝区别了解吗？什么是引用拷贝？](#深拷贝和浅拷贝区别了解吗-什么是引用拷贝)

关于深拷贝和浅拷贝区别，我这里先给结论：

- **浅拷贝**：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），不过，如果原对象内部的属性是引用类型的话，浅拷贝会直接复制内部对象的引用地址，也就是说拷贝对象和原对象共用同一个内部对象。
- **深拷贝**：深拷贝会完全复制整个对象，包括这个对象所包含的内部对象。
- **引用拷贝？** 引用拷贝就是两个不同的引用指向同一个对象。

![image-20240428165012064](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240428165012064.png)

# [Object](https://javaguide.cn/java/basis/java-basic-questions-02.html#object)

## [Object 类的常见方法有哪些？](https://javaguide.cn/java/basis/java-basic-questions-02.html#object-类的常见方法有哪些)

```java
/**
 * native 方法，用于返回当前运行时对象的 Class 对象，使用了 final 关键字修饰，故不允许子类重写。
 */
public final native Class<?> getClass()
/**
 * native 方法，用于返回对象的哈希码，主要使用在哈希表中，比如 JDK 中的HashMap。
 */
public native int hashCode()
/**
 * 用于比较 2 个对象的内存地址是否相等，String 类对该方法进行了重写以用于比较字符串的值是否相等。
 */
public boolean equals(Object obj)
/**
 * native 方法，用于创建并返回当前对象的一份拷贝。
 */
protected native Object clone() throws CloneNotSupportedException
/**
 * 返回类的名字实例的哈希码的 16 进制的字符串。建议 Object 所有的子类都重写这个方法。
 */
public String toString()
/**
 * native 方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。
 */
public final native void notify()
/**
 * native 方法，并且不能重写。跟 notify 一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
 */
public final native void notifyAll()
/**
 * native方法，并且不能重写。暂停线程的执行。注意：sleep 方法没有释放锁，而 wait 方法释放了锁 ，timeout 是等待时间。
 */
public final native void wait(long timeout) throws InterruptedException
/**
 * 多了 nanos 参数，这个参数表示额外时间（以纳秒为单位，范围是 0-999999）。 所以超时的时间还需要加上 nanos 纳秒。。
 */
public final void wait(long timeout, int nanos) throws InterruptedException
/**
 * 跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念
 */
public final void wait() throws InterruptedException
/**
 * 实例被垃圾回收器回收的时候触发的操作
 */
protected void finalize() throws Throwable { }
```

## [hashCode() 有什么用？](https://javaguide.cn/java/basis/java-basic-questions-02.html#hashcode-有什么用)

`hashCode()` 的作用是获取哈希码（`int` 整数），也称为散列码。这个哈希码的作用是确定该对象在哈希表中的索引位置。

> :warning:  注意：该方法在 **Oracle OpenJDK8** 中默认是 "使用线程局部状态来实现 Marsaglia's xor-shift 随机数生成", 并不是 "地址" 或者 "地址转换而来", 不同 JDK/VM 可能不同在 **Oracle OpenJDK8** 中有六种生成方式 (其中第五种是返回地址), 通过添加 VM 参数: -XX:hashCode=4 启用第五种

通过hash码初步判断是否相同，如果hash码相等之后再`equals()`判断

## [为什么重写 equals() 时必须重写 hashCode() 方法？](#为什么重写-equals-时必须重写-hashcode-方法)

因为两个相等的对象的 `hashCode` 值必须是相等。也就是说如果 `equals` 方法判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等。

如果重写 `equals()` 时没有重写 `hashCode()` 方法的话就可能会导致 `equals` 方法判断是相等的两个对象，`hashCode` 值却不相等。

**总结**：

- `equals` 方法判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等。
- 两个对象有相同的 `hashCode` 值，他们也不一定是相等的（哈希碰撞）。

# [String](https://javaguide.cn/java/basis/java-basic-questions-02.html#string)

## [String、StringBuffer、StringBuilder 的区别？](https://javaguide.cn/java/basis/java-basic-questions-02.html#string、stringbuffer、stringbuilder-的区别)

**可变性**

- `String` 是不可变的。

- `StringBuilder` 与 `StringBuffer` 可变

  > `StringBuilder` 与 `StringBuffer`都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串，不过没有使用 `final` 和 `private` 关键字修饰，最关键的是这个 `AbstractStringBuilder` 类还提供了很多修改字符串的方法比如 `append` 方法。

**线程安全性**

`String` 中的对象是不可变的，也就可以理解为常量，线程安全。

`AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。

**性能**

每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。

`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**对于三者使用的总结：**

- 操作少量的数据: 适用 `String`
- 单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
- 多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`

## [String 为什么是不可变的?](#string-为什么是不可变的)

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    private final char value[];
  //...
}

```

- 保存字符串的数组被`final`修饰且是私有的，并且`String`类没有暴露修改这个字符串的方法
- `String` 类被 `final` 修饰导致其不能被继承，进而避免了子类破坏 `String` 不可变。

## [为什么JDK9之后，`String`、`StringBuilder` 与 `StringBuffer` 的实现改用 `byte` 数组存储字符串]()

之前是`char`数组

新版的 String 其实支持两个编码方案：Latin-1 和 UTF-16。

- 如果字符串中包含的汉字没有超过 Latin-1 可表示范围内的字符，那就会使用 Latin-1 作为编码方案。Latin-1 编码方案下，`byte` 占一个字节(8 位)，`char` 占用 2 个字节（16），`byte` 相较 `char` 节省一半的内存空间。

JDK 官方就说了绝大部分字符串对象只包含 Latin-1 可表示的字符。

## [字符串拼接用“+” 还是 StringBuilder?](https://javaguide.cn/java/basis/java-basic-questions-02.html#字符串拼接用-还是-stringbuilder)

Java 语言本身并不支持运算符重载，“+”和“+=”是专门为 String 类重载过的运算符，也是 Java 中仅有的两个重载过的运算符

可以看出，字符串对象通过“+”的字符串拼接方式，实际上是通过 `StringBuilder` 调用 `append()` 方法实现的，拼接完成之后调用 `toString()` 得到一个 `String` 对象 。

> 不过，使用 “+” 进行字符串拼接会产生大量的临时对象的问题在 JDK9 中得到了解决。在 JDK9 当中，字符串相加 “+” 改为了用动态方法 `makeConcatWithConstants()` 来实现，而不是大量的 `StringBuilder` 了。这个改进是 JDK9 的 [JEP 280open in new window](https://openjdk.org/jeps/280) 提出的，这也意味着 JDK 9 之后，你可以放心使用“+” 进行字符串拼接了。关于这部分改进的详细介绍，推荐阅读这篇文章：还在无脑用 [StringBuilder？来重温一下字符串拼接吧](https://juejin.cn/post/7182872058743750715)

## [String#equals() 和 Object#equals() 有何区别？](#string-equals-和-object-equals-有何区别)

`String` 中的 `equals` 方法是被重写过的，比较的是 String 字符串的值是否相等。 `Object` 的 `equals` 方法是比较的对象的内存地址。

## [字符串常量池的作用了解吗？](https://javaguide.cn/java/basis/java-basic-questions-02.html#字符串常量池的作用了解吗)

**字符串常量池** 是 JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建

## [String s1 = new String("abc");这句话创建了几个字符串对象？](#string-s1-new-string-abc-这句话创建了几个字符串对象)

会创建 1 或 2 个字符串对象。

1. 如果字符串常量池中不存在字符串对象“abc”的引用，那么它会在堆上创建两个字符串对象，其中一个字符串对象的引用会被保存在字符串常量池中

2. 如果字符串常量池中已存在字符串对象“abc”的引用，则只会在堆中创建 1 个字符串对象“abc”

## [String#intern 方法有什么作用?](#string-intern-方法有什么作用)

`String.intern()` 是一个 native（本地）方法，其作用是将指定的字符串对象的引用保存在字符串常量池中，可以简单分为两种情况：

- 如果字符串常量池中保存了对应的字符串对象的引用，就直接返回该引用。
- 如果字符串常量池中没有保存了对应的字符串对象的引用，那就在常量池中创建一个指向该字符串对象的引用并返回。

## [String 类型的变量和常量做“+”运算时发生了什么？](https://javaguide.cn/java/basis/java-basic-questions-02.html#string-类型的变量和常量做-运算时发生了什么)

**对于编译期可以确定值的字符串，也就是常量字符串 ，jvm 会将其存入字符串常量池。并且，字符串常量拼接得到的字符串常量在编译阶段就已经被存放字符串常量池，这个得益于编译器的优化。**

常量折叠会把常量表达式的值求出来作为常量嵌在最终生成的代码中，这是 Javac 编译器会对源代码做的极少量优化措施之一(代码优化几乎都在即时编译器中进行)。

对于 `String str3 = "str" + "ing";` 编译器会给你优化成 `String str3 = "string";` 

并不是所有的常量都会进行折叠，只有编译器在程序编译期就可以确定值的常量才可以：

- 基本数据类型( `byte`、`boolean`、`short`、`char`、`int`、`float`、`long`、`double`)以及字符串常量。
- `final` 修饰的基本数据类型和字符串变量
- 字符串通过 “+”拼接得到的字符串、基本数据类型之间算数运算（加减乘除）、基本数据类型的位运算（<<、>>、>>> ）

**引用的值在程序编译期是无法确定的，编译器无法对其进行优化。**

> 只有在没有进行运行前就知道值的，才能进行优化

# [异常]()

![image-20240428192159277](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240428192159277.png)

## [Exception 和 Error 有什么区别？](#exception-和-error-有什么区别)

在 Java 中，所有的异常都有一个共同的祖先 `java.lang` 包中的 `Throwable` 类。`Throwable` 类有两个重要的子类:

- **`Exception`** :程序本身可以处理的异常，可以通过 `catch` 来进行捕获。`Exception` 又可以分为 
  - Checked Exception (受检查异常，必须处理) 
  -  Unchecked Exception (不受检查异常，可以不处理)。
- **`Error`**：`Error` 属于程序无法处理的错误 ，我们没办法通过 `catch` 来进行捕获不建议通过`catch`捕获 。
  - 例如 Java 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误(`OutOfMemoryError`)、类定义错误（`NoClassDefFoundError`）等 。
  - 这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止

## [Checked Exception 和 Unchecked Exception 有什么区别？](https://javaguide.cn/java/basis/java-basic-questions-03.html#checked-exception-和-unchecked-exception-有什么区别)

除了`RuntimeException`及其子类以外，其他的`Exception`类及其子类都属于受检查异常，必须`try catch`或者`throw`

`RuntimeException` 及其子类都统称为非受检查异常，常见的有（建议记下来，日常开发中会经常用到）：

- `NullPointerException`(空指针错误)
- `IllegalArgumentException`(参数错误比如方法入参类型错误)
- `NumberFormatException`（字符串转换为数字格式错误，`IllegalArgumentException`的子类）
- `ArrayIndexOutOfBoundsException`（数组越界错误）
- `ClassCastException`（类型转换错误）
- `ArithmeticException`（算术错误）
- `SecurityException` （安全错误比如权限不够）
- `UnsupportedOperationException`(不支持的操作错误比如重复创建同一用户)

## [Throwable 类常用方法有哪些？](#throwable-类常用方法有哪些)

- `String getMessage()`: 返回异常发生时的简要描述
- `String toString()`: 返回异常发生时的详细信息
- `String getLocalizedMessage()`: 返回异常对象的本地化信息。使用 `Throwable` 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage()`返回的结果相同
- `void printStackTrace()`: 在控制台上打印 `Throwable` 对象封装的异常信息

## [try-catch-finally 如何使用？](https://javaguide.cn/java/basis/java-basic-questions-03.html#try-catch-finally-如何使用)

`try`块：用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。

`catch`块：用于处理 try 捕获到的异常。

`finally` 块：无论是否捕获或处理异常，`finally` 块里的语句都会被执行。当在 `try` 块或 `catch` 块中遇到 `return` 语句时，==`finally` 语句块将在方法返回之前被执行==

:warning:**不要在 finally 语句块中使用 return!** 

当 try 语句和 finally 语句中都有 return 语句时，try 语句块中的 return 语句会被忽略。这是因为 try 语句中的 return 返回值会先被暂存在一个本地变量中，当执行到 finally 语句中的 return 之后，这个本地变量的值就变为了 finally 语句中的 return 返回值。

## [finally 中的代码一定会执行吗？](https://javaguide.cn/java/basis/java-basic-questions-03.html#finally-中的代码一定会执行吗)

以下三种不会执行

- 虚拟机寄了
- 所在线程死亡
- 关闭CPU

## [如何使用 `try-with-resources` 代替`try-catch-finally`？](#如何使用-try-with-resources-代替try-catch-finally)

1. **适用范围（资源的定义）：** 任何实现 `java.lang.AutoCloseable`或者 `java.io.Closeable` 的对象
2. **关闭资源和 finally 块的执行顺序：** 在 `try-with-resources` 语句中，任何 catch 或 finally 块在声明的资源关闭后运行

```java
Scanner scanner = null;
try {
    scanner = new Scanner(new File("D://read.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}

//这个代替上边的
try (Scanner scanner = new Scanner(new File("test.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException fnfe) {
    fnfe.printStackTrace();
}

```

## [异常使用有哪些需要注意的地方？](#异常使用有哪些需要注意的地方)

- 不要把异常定义为静态变量，因为这样会导致异常栈信息错乱。每次手动抛出异常，我们都需要手动 new 一个异常对象抛出。
- 抛出的异常信息一定要有意义。
- 建议抛出更加具体的异常比如字符串转换为数字格式错误的时候应该抛出`NumberFormatException`而不是其父类`IllegalArgumentException`。
- 避免重复记录日志：如果在捕获异常的地方已经记录了足够的信息（包括异常类型、错误信息和堆栈跟踪等），那么在业务代码中再次抛出这个异常时，就不应该再次记录相同的错误信息。重复记录日志会使得日志文件膨胀，并且可能会掩盖问题的实际原因，使得问题更难以追踪和解决。
- .....

> 异常别弄成静态变量、要有意义、更具体、不重复记录

# [泛型](https://javaguide.cn/java/basis/java-basic-questions-03.html#泛型)

## [什么是泛型？有什么作用？](https://javaguide.cn/java/basis/java-basic-questions-03.html#什么是泛型-有什么作用)

**Java 泛型（Generics）** 是 JDK 5 中引入的一个新特性。使用泛型参数，可以增强代码的可读性以及稳定性。

编译器可以对泛型参数进行检测，并且通过泛型参数可以指定传入的对象类型。比如 `ArrayList<Person> persons = new ArrayList<Person>()` 这行代码就指明了该 `ArrayList` 对象只能传入 `Person` 对象，如果传入其他类型的对象就会报错。

泛型参数的安全检查发生在编译时

> 在Java中，当我们比较两个对象是否相等时，泛型信息在编译时会被擦除（类型擦除）

# [反射](https://javaguide.cn/java/basis/java-basic-questions-03.html#反射)

## [什么是反射]()

反射赋予了我们在运行时分析类以及执行类中方法的能力。

通过反射你可以获取任意一个类的所有属性和方法，你还可以调用这些方法和属性

## [反射的优缺点？](#反射的优缺点)

优点

- 代码灵活
- 为各种框架提供开箱即用的功能提供了便利

缺点：

- 无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）
- 性能差

## [反射的应用场景？](#反射的应用场景)

像 Spring/Spring Boot、MyBatis 等等框架中都大量使用了反射机制。

**这些框架中也大量使用了动态代理，而动态代理的实现也依赖反射**

# [注解](https://javaguide.cn/java/basis/java-basic-questions-03.html#注解)

## [何谓注解？](https://javaguide.cn/java/basis/java-basic-questions-03.html#何谓注解)

`Annotation` （注解） 是 Java5 开始引入的新特性，可以看作是一种特殊的注释，主要用于修饰类、方法或者变量，==提供某些信息供程序在编译或者运行时使用==。

## [注解的解析方法有哪几种？](#注解的解析方法有哪几种)

注解只有被解析之后才会生效，常见的解析方法有两种：

- **编译期直接扫描**：编译器在编译 Java 代码的时候扫描对应的注解并处理，比如某个方法使用`@Override` 注解，编译器在编译的时候就会检测当前的方法是否重写了父类对应的方法。
- **运行期通过反射处理**：像框架中自带的注解(比如 Spring 框架的 `@Value`、`@Component`)都是通过反射来进行处理的。

# [SPI](https://javaguide.cn/java/basis/java-basic-questions-03.html#spi)

## [何谓 SPI?](https://javaguide.cn/java/basis/java-basic-questions-03.html#何谓-spi)

SPI 即 Service Provider Interface ，字面意思就是：“服务提供者的接口”，我的理解是：专门提供给服务提供者或者扩展框架功能的开发者去使用的一个接口

## [SPI 和 API 有什么区别？](https://javaguide.cn/java/basis/java-basic-questions-03.html#spi-和-api-有什么区别)

![image-20240428194009705](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240428194009705.png)

api是实现方暴露接口并给出实现，调用方只调用即可

spi是实现方只暴露接口，调用方自己实现

# [序列化和反序列化](https://javaguide.cn/java/basis/java-basic-questions-03.html#序列化和反序列化)

- **序列化**：将数据结构或对象转换成二进制字节流的过程
- **反序列化**：将在序列化过程中所生成的二进制字节流转换成数据结构或者对象的过程

**序列化的主要目的是通过网络传输对象或者说是将对象存储到文件系统、数据库、内存中。**

## [序列化协议对应于 TCP/IP 4 层模型的哪一层？]()

![image-20240428194305867](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240428194305867.png)

7层模型中，序列化与反序列化体现在表示层

上三层都是TCP/IP模型中的==应用层==

## [如果有些字段不想进行序列化怎么办？](#如果有些字段不想进行序列化怎么办)

对于不想进行序列化的变量，使用 `transient` 关键字修饰。

`transient` 关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被 `transient` 修饰的变量值不会被持久化和恢复。

关于 `transient` 还有几点注意：

- `transient` 只能修饰变量，不能修饰类和方法。
- `transient` 修饰的变量，在反序列化后变量值将会被置成类型的默认值。例如，如果是修饰 `int` 类型，那么反序列后结果就是 `0`。
- `static` 变量因为不属于任何对象(Object)，所以无论有没有 `transient` 关键字修饰，均不会被序列化。

## [常见序列化协议有哪些？](#常见序列化协议有哪些)

JDK 自带的序列化方式一般不会用 ，因为序列化效率低并且存在安全问题。比较常用的序列化协议有 Hessian、Kryo、Protobuf、ProtoStuff，这些都是基于二进制的序列化协议。

像 JSON 和 XML 这种属于文本类序列化方式。虽然可读性比较好，但是性能较差，一般不会选择

## [为什么不推荐使用 JDK 自带的序列化？](#为什么不推荐使用-jdk-自带的序列化)

我们很少或者说几乎不会直接使用 JDK 自带的序列化方式，主要原因有下面这些原因：

- **不支持跨语言调用** : 如果调用的是其他语言开发的服务的时候就不支持了。
- **性能差**：相比于其他序列化框架性能更低，主要原因是序列化之后的字节数组体积较大，导致传输成本加大。
- **存在安全问题**：相关阅读：[应用安全：JAVA 反序列化漏洞之殇open in new window](https://cryin.github.io/blog/secure-development-java-deserialization-vulnerability/) 。

# [I/O]()

## [Java IO 流了解吗？](https://javaguide.cn/java/basis/java-basic-questions-03.html#java-io-流了解吗)

Java IO 流的 40 多个类都是从如下 4 个抽象类基类中派生出来的。

- `InputStream`/`Reader`: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
- `OutputStream`/`Writer`: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

> - **往内存中**是**输入**Input（读Read）
> - **从内存出**叫**输出**Output（写Write）

## [I/O 流为什么要分为字节流和字符流呢?](#i-o-流为什么要分为字节流和字符流呢)

- 字符流是由 Java 虚拟机将字节转换得到的，这个过程还算是比较耗时；
- 如果我们不知道编码类型的话，使用字节流的过程中很容易出现乱码问题

## [Java IO 中的设计模式有哪些？](https://javaguide.cn/java/basis/java-basic-questions-03.html#java-io-中的设计模式有哪些)

参考答案：[Java IO 设计模式总结](https://javaguide.cn/java/io/io-design-patterns.html)

## [BIO、NIO 和 AIO 的区别？](https://javaguide.cn/java/basis/java-basic-questions-03.html#bio、nio-和-aio-的区别)

参考答案：[Java IO 模型详解](https://javaguide.cn/java/io/io-model.html)

# [语法糖](#语法糖)

## [什么是语法糖？](#什么是语法糖)

**语法糖（Syntactic sugar）** 代指的是编程语言为了方便程序员开发程序而设计的一种特殊语法，这种语法对编程语言的功能并没有影响。实现相同的功能，基于语法糖写出来的代码往往更简单简洁且更易阅读。

JVM不能识别语法糖，由编译器进行解糖

Java 中最常用的语法糖主要有泛型、自动拆装箱、变长参数、枚举、内部类、增强 for 循环、try-with-resources 语法、lambda 表达式等。