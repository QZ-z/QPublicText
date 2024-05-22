> 实时解释主要是为了跨平台

p9hsdb工具的使用

hsbd的用途：查看java虚拟机的内存信息

![image.png](https://cdn.nlark.com/yuque/0/2024/png/29496705/1713232672918-f53bc41f-6826-46c3-86ea-c9726c52997e.png?x-oss-process=image%2Fformat%2Cwebp)



java8之前使用hsdb ，java9之后使用jhsdb，在jdk的bin目录下

jps展示当前运行的java进程及其对应的uid（因为hsdb需要进程的uid）



线程启动之后默认的线程上下文加载器是应用程序类加载器



周志明《深入理解java虚拟机》



阿尔萨斯实现热部署只是将修改后的字节码加载到了内存中，所以重启会失效



jdk9之后的类加载器，应用程序加载器变化了么



redis中的slot



![image.png](https://cdn.nlark.com/yuque/0/2024/png/29496705/1713236479202-0e8631cb-7016-477e-a38e-63c84bf5cdca.png?x-oss-process=image%2Fformat%2Cwebp)



操作系统中的c代码加载顺序和这个有关系么？



new string出的字符串对象存储结构是怎么样的

字符串部分着重关注

NIO具体是什么



JMM



应该学会使用的工具，阿尔萨斯，jclass，MAT，HSDB