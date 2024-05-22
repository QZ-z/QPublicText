# Array.stream(a)和list.stream()

`Array.stream(a)`对象获得是一个`IntStream`（如果a是int类型数组的话）对象，其中有`sum()`方法

![image-20240425155531669](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20240425155531669.png)

`list.stream()`获得的是`Stream<Integer>`，其中没有`sum()`方法，继续使用`mapToInt`则返回的是`IntStream`对象

# **子类重写的方法使用的访问权限不能小于父类被重写的方法的访问权限的原因**

因为在使用多态的时候经常把子类向上转型为父类，进而`父类.方法(形参列表)`

如果子类缩小了允许访问的范围，那么多态的时候就不能运行子类重写的方法了

允许扩大，能够允许子类方法完成更多父类方法不能完成的事

# ArrayDeque和LinkedList的插入

ArrayDeque中禁止插入null元素
LinkedList中可以插入null元素

以LinkedList中的offerLast方法为例子

```java
public boolean offerLast(E e) {
        addLast(e);
        return true;
    }
public void addLast(E e) {
        linkLast(e);
    }
void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }

```

实际上，LinkedList在内部申请一个Node节点来存放加入元素的值，而实际上插入的Node节点还是非空的

再来看ArrayDeque中的offerLast方法

```java
public boolean offerLast(E e) {
        addLast(e);
        return true;
    }
public void addLast(E e) {
        if (e == null)
            throw new NullPointerException();
        final Object[] es = elements;
        es[tail] = e;
        if (head == (tail = inc(tail, es.length)))
            grow(1);
    }
```

只要插入的是null，则返回空指针异常

![image-20231214161645574](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20231214161645574.png)

![image-20231214161656146](http://pig-test-qz.oss-cn-beijing.aliyuncs.com/img/image-20231214161656146.png)
