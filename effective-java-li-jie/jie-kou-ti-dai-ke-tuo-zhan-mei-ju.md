# 接口替代可拓展枚举

* 利用`IOperation` 接口，通过不同的枚举类实例实现，这样使用接口接受实现类的方式，凡是`IOperation` 接口出现的地方，都可以使用实现类枚举替换！

```java
public interface IOperation {
    double apply(double var1, double var3);
}
```

```java
public enum Operation3 implements IOperation {
    Plus("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    Minus("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    Times("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    Divide("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };

    private final String symbol;

    private Operation3(String symbol) {
        this.symbol = symbol;
    }

    public String toString() {
        return this.symbol;
    }
}
```

```java
public enum ExtendOperation implements IOperation {
    Exponentiation("^") {
        public double apply(double x, double y) {
            return Math.pow(x, y);
        }
    },
    Remainder("%") {
        public double apply(double x, double y) {
            return x % y;
        }
    };

    private final String symbol;

    private ExtendOperation(String symbol) {
        this.symbol = symbol;
    }

    public String toString() {
        return this.symbol;
    }

    public double apply(double x, double y) {
        return 0.0;
    }
}
```
