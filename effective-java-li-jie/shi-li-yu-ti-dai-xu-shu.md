# 实例域替代序数

* 应该在非枚举数据结构，禁用`ordinal()`方法,使用实例域 比如int的code

```java
public enum Ensemble2 {
    Solo(1),
    Duet(2),
    Trio(3),
    Quartet(4),
    Quintet(5),
    Sextet(6),
    Septet(7),
    Octet(8),
    Nonet(9),
    Dectet(10),
    TripleQuartet(12);

    private final int numberOfMusicians;

    private Ensemble2(int numberOfMusicians) {
        this.numberOfMusicians = numberOfMusicians;
    }

    public int numberOfMusicians() {
        return this.numberOfMusicians;
    }
}
```
