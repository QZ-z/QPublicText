[toc]
<font size=3>
# package
- java中的包机制，作用：方便程序管理，不同功能的类分别存放在不同的包下（不同的软件包具有不同的功能）

## package定义
- package是一个关键字，后边加包名，例如:<br>

<font color=red>**package com.bjpowernode.javase.chapter17;**</font>
> package语句只允许出现在java源代码的**第一行**

## 包名命名规范
- 公司域名倒叙 + 项目名 + 功能名
## 带有package程序的编译和运行
- 以HelloWorld为例
- 类名实际为：com.bjpowernode.javase.chapter17.HelloWorld
- **编译**：javac -d . HelloWorld.java（java -d . xxx.java）
> - -d：带包编译
> - . 代表编译之后生成的东西放到当前目录下（点代表当前目录）
- **运行**：java com.bjpowernode.javase.chapter17.HelloWorld（java 完整包名）

# import
## 何时使用
- A类中使用B类，在同一个包下边，不用使用，不在同一个包下，使用
- import只能出现在**package之下，class声明之上**
- java.lang包下的类不需要使用import导入



</font>