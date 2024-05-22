[toc]
<font size=3>
# API
- 应用程序编程接口（Application Program Interface）
- 整个JDK的类库就是一个javase的API
- 每个API都会配置一套API帮助文档

# Object下的一些方法

## protected Object clone( )
- 负责对象克隆
- 深克隆、浅克隆

## int hashCode( )
- 获取对象哈希值的一个方法
- **默认实现**：<font color=red>public native int hashCode();</font>
    > 带有native，底层调用c++程序
- hashCode（）方法返回的是哈希码，实际上是一个java对象的内存地址，经过哈希算法，得出的一个值。所以hashCode（）方法的执行结果可以等同看做一个java对象的内存地址。 

## boolean equals(Object obj)

- 判断两个对象是否相等
- **默认实现**：
<font color=red><br> public boolean equals(Object obj) {
<br> &emsp; return (this == obj);
<br>}</font>
- **设计目的**：以后编程的过程当中，都需要通过equals（）方法判断两个对象是否相等
> - “==”无法用来判断引用数据类型是否相等，两端是引用的时候判断的是引用指向的对象的**内存地址是否相等**，所以默认的equals（）方法不够用
> - 字符串比较不能用“==”
> - String类已经重写了equals（）方法，可以用equals（）比较两个字符串是否相等
> - String类已经重写了toString（）方法
> - java中基本数据类型判断相等使用“==”，引用数据类型判断相等使用equals（）方法
```java
    String s1="ss";
    String s2="ss";
    String s3=new String("dd");
    String s4=new String("dd");
    System.out.println(s1==s2);//true
    System.out.println(s3.equals(s4));//true
    System.out.println(s3==s4);//flase
```
## String toString( )
- 将对象转换成字符串形式
- **默认实现**：<font color=red><br>public String toString{<br> &emsp; return getClass().getName() + "@" Integer.toHexString(hashCode());<br> }<br></font>
- **设计目的**：通过这个方法，可以将一个java对象转换成字符串表示形式
- **建议所有子类都重写toString（）方法**，toString（）方法应该是简洁的、详实的、易阅读的
- String类已经重写过该方法

## protected void finalize( )（了解）
- 垃圾回收机制负责调用的方法
- **默认实现**：<font color=red> protected void finalize() throws Throwable { }</font>
- 不需要程序员手动调用，JVM的垃圾回收器负责调用这个方法
- **执行时机**：当一个对象即将被垃圾回收器回收的时候
- finalize（）方法和静态代码块一样是SUN为程序员准备的一个时机，这个时机是**垃圾回收时机**
- java中的垃圾回收器不是轻易启动的，垃圾太少，或者时间没到，种种条件下，有可能启动，也有可能不启动
- **建议启动垃圾回收器**：`System.gc() 和 Runtime.getRuntime().gc()`
  
    > 只是建议，也有可能不启动
    
- 垃圾回收器(GC，Garbage Collection)主要针对堆内存

## equals（）方法和toString（）方法都要重写，不用也写

重写示例

```java
public boolean equals(Object obj) {
    if (this == obj) {
     return true;
    }
	//确定比较类型为 person
	//同一类型，才具有可比性
	if (obj instanceof Person) {
		//强制转换，必须实现知道该类型是什么
		Person p = (Person)obj;
		//如果 id 相等就认为相等
		if (this.id == p.id) {
			return true;
        }
	}
	return false;
}
```

<font size=3>

# 内部类

- 在类的内部又定义了一个类。被称作内部类
- 虽然类只能使用public和默认修饰，但内部类可以使用 private 和 protected 修饰

## 内部类的分类

- **静态内部类**：类似于静态变量
- **实例内部类**：类似于实例变量
- **局部内部类**：类似于局部变量

> 使用内部类编写的代码，可读性很差，尽量不使用

## 实例内部类

- 创建实例内部类，外部类的实例必须已经创建
- 实例内部类会持有外部类的引用
- 实例内部不能定义 static 成员，只能定义实例成员

```java
public class InnerClassTest01 {
    private int a;
    private int b;
    InnerClassTest01(int a, int b) {
        this.a = a;
        this.b = b;
    }
    //内部类可以使用 private 和 protected 修饰
    private class Inner1 {
        int i1 = 0;
        int i2 = 1;
        int i3 = a;
        int i4 = b;
        //实例内部类不能采用 static 声明
        //static int i5 = 20;
    }
    public static void main(String[] args) {
        InnerClassTest01.Inner1 inner1 = new InnerClassTest01(100, 200).new Inner1();
        System.out.println(inner1.i1);
        System.out.println(inner1.i2);
        System.out.println(inner1.i3);
        System.out.println(inner1.i4);
    }
}
```

## 静态内部类

- 静态内部类不会持有外部的类的引用，创建时可以不用创建外部类
- 静态内部类可以访问外部的静态变量，如果访问外部类的成员变量必须通过外部类的实例访问

```java
public class InnerClassTest02 {
    static int a = 200;
    int b = 300;
    static class Inner2 {
        //在静态内部类中可以定义实例变量
        int i1 = 10;
        int i2 = 20;
        //可以定义静态变量
        static int i3 = 100;
        //可以直接使用外部类的静态变量
        static int i4 = a;
        //不能直接引用外部类的实例变量
        //int i5 = b;
        //采用外部类的引用可以取得成员变量的值
        int i5 = new InnerClassTest02().b;
    }
    public static void main(String[] args) {
        InnerClassTest02.Inner2 inner = new InnerClassTest02.Inner2();
        System.out.println(inner.i1);
    }
}
```
## 局部内部类

- 局部内部类是在方法中定义的，它只能在当前方法中使用。和局部变量的作用一样
- 局部内部类和实例内部类一致，不能包含静态成员

```java
public class InnerClassTest03 {
    private int a = 100;
    //局部变量，在内部类中使用必须采用 final 修饰
    public void method1(final int temp) {
        class Inner3 {
            int i1 = 10;
            //可以访问外部类的成员变量
            int i2 = a;
            int i3 = temp;
        } 
        //使用内部类
        Inner3 inner3 = new Inner3();
        System.out.println(inner3.i1);
        System.out.println(inner3.i3);
    }
    public static void main(String[] args) {
        InnerClassTest03 innerClassTest03 = new InnerClassTest03();
        innerClassTest03.method1(300);
    }
}
```
## 匿名内部类

- 局部内部类的一种，因为没有名字而得名
- 用法：`new 接口（）{ 该大括号中实现接口中的方法 }`
- 缺点：
  1. 太复杂，太乱，可读性差
  2. 没有名字，无法重复使用

```java
// 	应用例子
public interface compute {
    int sum(int a,int b);
}

public class myMath {
    public int mySum(compute c ,int a,int b){
        int value=c.sum(a,b);
        System.out.println(a+ "+"+ b +"="+value);
        return value;
    }
}

//使用匿名内部类则不需要创建这个类来实现接口
public class computeiml implements compute{
    @Override
    public int sum(int a, int b) {
        return a+b;
    }
}

public class test {
    public static void main(String[] args) {
        myMath mm=new myMath();
        //使用匿名内部类
        mm.mySum(new compute() {
            @Override
            public int sum(int a, int b) {
                return a+b;
            }
        },100,200);

        System.out.println("dont use inner anonymous class ");
        //不使用匿名内部类的写法
        mm.mySum(new computeiml(),122,123);
    }
}

```

</font>

</font>