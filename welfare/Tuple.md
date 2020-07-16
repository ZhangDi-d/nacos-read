# **元组**

元组（tuples），可以看成是一个数组或列表，与数组或列表不同的地方在于一个 `tuple` 是一个相互之间不需要任何关联的对象的序列，比如下面的序列就可以看成是一个具有三个元素的 `tuple`：

```java
[120, "Kobe", java.sql.Connection@lf1s1]
```

实际上这个元组也叫做 `Triplet`，即三元组；类似的，我们可以发散思考是不是意味着提供了 `N` 个元素的元组叫做 `N元组` 呢？

</br>

## **元组作用及使用场景**

元组数据实际上就是可以用来存储一组不同元素类型的容器，并且需要注意的是元组一旦定义之后就是不可变的了，下面将二元组的时候会详细剖析。

既然我们知道了元组可以存储的一组不同元素且不可变，那么它的作用实际上就也是围绕其而展开的了：

- `一个函数要同时返回多个类`（一般做法是定义一个对象，然后把其他要返回的对象当做定义的这个对象的属性 or 直接使用 Map 返回。第一种方法可靠但是代码臃肿、第二种方法快捷但又不够安全），实际上使用元组就比较适合
- 因为元组不可变，所以我们可以用来`返回一组不可变的不同元素`
- 此外就是参考元组 `Tuple` 定义的方法，比如：`遍历`、`排序`、`筛选` 等（详见 `javatuples` 包下源码 [Tuple.java](https://github.com/javatuples/javatuples/blob/master/src/main/java/org/javatuples/Tuple.java)）

</br>

## **与数组/列表的对比**

| 类型 | 特点 |
| --- | --- |
| 数组/列表 | 可以包含`任意数量` 且 `元素类型一致` 的元素 |
| 元组 | 只能包含 `固定数量` 并且 `类型不限` 的元素、元组中元素 `类型安全` |

</br>

## **javatuples 包**

前面说了，元组的元素个数是固定的，因此在 `javatuples` 中提供的元组类中，自然也就是提供了不同个数元素组成的元组，感兴趣的可以去看下 `javatuples` 的源码，这里我直接给结论：

```java
// javatuples 包定义了最多可容纳 10 个元素的元组
Unit<A> (1 element)
Pair<A,B> (2 elements)
Triplet<A,B,C> (3 elements)
Quartet<A,B,C,D> (4 elements)
Quintet<A,B,C,D,E> (5 elements)
Sextet<A,B,C,D,E,F> (6 elements)
Septet<A,B,C,D,E,F,G> (7 elements)
Octet<A,B,C,D,E,F,G,H> (8 elements)
Ennead<A,B,C,D,E,F,G,H,I> (9 elements)
Decade<A,B,C,D,E,F,G,H,I,J> (10 elements)
```

使用元组也非常简单，如下：

```java
// 二元组
Pair<A, B> pair = new Pair<A, B>(value1, value2);

// 三元组
Triplet<A, B, C> triplet = new Triplet<A, B, C>(value1, value2, value3);
```

我们先来剖析一下元组（tuple）、二元组（Pair）在 `javatuples` 依赖包中是怎样的

</br>

### **Tuple 与 Pair**

在 `javatuples` 包中，Tuple 抽象类是所有元组类的抽象基类，其结构如下：

```java
// Tuple 抽象类
public abstract class Tuple implements Iterable<Object>, Serializable, Comparable<Tuple> {

    private static final long serialVersionUID = 5431085632328343101L;
    
    private final Object[] valueArray;
    private final List<Object> valueList;

    protected Tuple(final Object... values) {
        super();
        this.valueArray = values;
        this.valueList = Arrays.asList(values);
    }

    // 省略其他方法 ...

}
```

而二元组 `Pair` 则是继承了 `Tuple` 并实现了 `IValue0<A>,IValue1<B>` 这两个元组的标记接口，这两个接口很简单，如下：

> 需要注意的是泛型尖括号里面的类型不能是 Java 的基本类型！！！

```java
// IValue0<A> 接口
public interface IValue0<X> {
    public X getValue0();
}

// IValue0<B> 接口
public interface IValue1<X> {
    public X getValue1();
}
```

而 `Pair` 二元组内部则是维护了两个不可变的变量，如下：

```java
public final class Pair<A,B> extends Tuple implements IValue0<A>, IValue1<B> {

    private static final long serialVersionUID = 2438099850625502138L;
    private static final int SIZE = 2;

    // 不可变变量
    private final A val0;
    private final B val1;

    public Pair(
            final A value0, 
            final B value1) {
        super(value0, value1);
        this.val0 = value0;
        this.val1 = value1;
    }
}
```

二元组 `Pair` 中提供了一个 `with` 静态方法来帮助我们构建一个二元组，如下：

```java
public static <A,B> Pair<A,B> with(final A value0, final B value1) {
    return new Pair<A,B>(value0,value1);
}
```

看到这里，有没有觉得这个二元组 `Pair` 和我们平时使用的 `key-value` 键值对类似，其中 `key相当于 pair.getValue0();`，而 `value相当于 pair.getValue1();`？不同之处在于这个特殊的 `key-value` 键值对只有一个 `key` 和一个 `value` 并且是不可变的。





## **Reference**

- [Java中元组的使用](https://zhuanlan.zhihu.com/p/25572598)


























</br></br>