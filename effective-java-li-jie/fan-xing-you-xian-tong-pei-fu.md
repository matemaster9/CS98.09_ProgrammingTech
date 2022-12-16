# 泛型-有限通配符

> tips：有限通配符的设计，遵循PECS（producer => extends，consumer => super）

```java
public interface IStack<E> {

    void push(E e);

    E pop();

    boolean isEmpty();

    void pushAll(Iterable<? extends E> src);

    void popAll(Collection<? super E> dst);
}
```

这里src提供元素，属于生产者，dst消费元素，属于消费者。所以各自对应的类型限定\<? extends E> ｜\<? super E>
