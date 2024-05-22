[toc]
<font size=3>
## java的特点
### 可移植性/跨平台
- java程序编写一次，就能到处运行
- 通过JVM（java虚拟机）实现
    - 优点：一次编写，各平台通用
    - 缺点：麻烦。运行java程序必须要有JVM
>所有的java程序都是运行在JVM当中的
### 健壮性
- 自动垃圾回收机制（GC机制）
## 一些术语
### JKD,JRE,JVM
- JDK：Java开发工具箱
- JRE：java运行环境
- JVM：java虚拟机
>JDK包括JRE,JRE包括JVM<br>只有JDK和JRE可以独立安装
### JavaSE,JavaEE,JavaME
- JavaSE：标准版
- JavaEE：企业版
- JavaME：微型版
### 注释
- //单行注释
- /* <br>&emsp; 多行注释<br> */
- /**<br>
	*javadoc注释：这里的注释信息可以自动被javadoc.exe命令解析提取并生成到帮助文档当中。<br>
	*/
## java程序的执行过程<br>
- 由**javac.exe**进行编译生成以.**class**结尾的字节码文件
- 执行**java 类名**的操作后
    - 第一步启动JVM
    - JVM启动**类加载器**（calssloader）
    >类加载器的作用：加载类。本质上是去硬盘上找对应类的**字节码**文件<br>

    - 找到对应的**字节码**文件后，类加载器会将该字节码装载到JVM中，JVM启动解释器将字节码解释成0 1二进制代码，操作系统执行二进制代码和硬件交互
</font>