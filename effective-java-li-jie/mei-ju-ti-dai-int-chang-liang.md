# 枚举替代int常量

* 类型限定：枚举替换int可以实现实例类型的限定！

```java
public enum Planet {
    Mercury(3.302E23, 2439000.0),
    Venus(4.689E24, 6052000.0),
    Earth(5.975E24, 6378000.0),
    Mars(6.419E23, 3393000.0),
    Jupiter(1.899E27, 7.149E7),
    Saturn(5.685E26, 6.027E7),
    Uranus(8.683E25, 2.556E7),
    Neptune(1.024E26, 2.477E7);

    private final double mass;
    private final double radius;
    private final double surfaceGravity;
    private static final double G = 6.673E-11;

    private Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
        this.surfaceGravity = 6.673E-11 * mass / Math.pow(radius, 2.0);
    }

    public double surfaceWeight(double mass) {
        return mass * this.surfaceGravity;
    }

    public double getMass() {
        return this.mass;
    }

    public double getRadius() {
        return this.radius;
    }

    public double getSurfaceGravity() {
        return this.surfaceGravity;
    }
}j
```

* 策略枚举的使用：将额外逻辑交与策略枚举实现，针对工作周和周末加班薪资计算。可以避免switch模式下，添加枚举时，忘记加入新增枚举，导致程序失败！

```java
public enum PayrollDay2 {
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday(PayrollDay2.PayrollStrategy.Weekend),
    Sunday(PayrollDay2.PayrollStrategy.Weekend);

    private final PayrollStrategy payrollStrategy;

    private PayrollDay2() {
        this(PayrollDay2.PayrollStrategy.Weekday);
    }

    private PayrollDay2(PayrollStrategy payrollStrategy) {
        this.payrollStrategy = payrollStrategy;
    }

    public int pay(int workHour, int hourlyWage) {
        return this.payrollStrategy.pay(workHour, hourlyWage);
    }

    private static enum PayrollStrategy {
        Weekday {
            public int pay(int workHour, int hourlyWage) {
                int basePay = workHour * hourlyWage;
                return workHour <= 8 ? basePay : basePay + (workHour - 8) * hourlyWage * 2;
            }
        },
        Weekend {
            public int pay(int workHour, int hourlyWage) {
                return workHour * hourlyWage * 2;
            }
        };

        private static final int BASE_WORK_HOUR = 8;

        private PayrollStrategy() {
        }

        abstract int pay(int var1, int var2);
    }
}
```

* switch应该给外部的枚举类型做特殊行为实现，而不是在枚举内部使用switch，可以使用map替换！

```java
public static Operation inverse(Operation opr) {
    switch (opr) {
        case Plus:
            return Operation.Minus;
        case Minus:
            return Operation.Plus;
        case Times:
            return Operation.Divide;
        case Divide:
            return Operation.Times;
        default:
            throw new AssertionError("unknown operation : " + opr);
    }
}
```

<pre class="language-java"><code class="lang-java"><strong>// 利用stream获取枚举map
</strong><strong>private static final Map&#x3C;String, Operation> stringToEnum = (Map)Stream.of(values()).collect(Collectors.toMap(Operation::toString, (t) -> {
</strong>    return t;
}));
// 提供null安全的工厂实例方法
public static Optional&#x3C;Operation> fromSymbol(String symbol) {
    return Optional.ofNullable(stringToEnum.get(symbol));
}
</code></pre>
