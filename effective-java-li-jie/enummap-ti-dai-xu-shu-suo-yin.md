# EnumMap替代序数索引

* 使用EnumMap替代枚举数组，多维数组可以使用`Map<枚举,Map<枚举,Object>>`嵌套替代

```java
public enum Phase2 {
    Solid,
    Liquid,
    Gas;

    private Phase2() {
    }

    public static enum Transition2 {
        Melt(Phase2.Solid, Phase2.Liquid),
        Freeze(Phase2.Liquid, Phase2.Solid),
        Boil(Phase2.Liquid, Phase2.Gas),
        Condense(Phase2.Gas, Phase2.Liquid),
        Sublime(Phase2.Solid, Phase2.Gas),
        Deposit(Phase2.Gas, Phase2.Solid);

        private final Phase2 from;
        private final Phase2 to;
        // 替代：private static final Transition[][] transitions = new Transition[][]{{null, Melt, Sublime}, {Freeze, null, Boil}, {Deposit, Condense, null}};
        private static final Map<Phase2, Map<Phase2, Transition2>> enumMap = (Map)Stream.of(values()).collect(Collectors.groupingBy(Transition2::getFrom, () -> {
            return new EnumMap(Phase2.class);
        }, Collectors.toMap(Transition2::getTo, (t) -> {
            return t;
        }, (x, y) -> {
            return y;
        }, () -> {
            return new EnumMap(Phase2.class);
        })));

        private Transition2(Phase2 from, Phase2 to) {
            this.from = from;
            this.to = to;
        }

        public static Transition2 from(Phase2 from, Phase2 to) {
            return (Transition2)((Map)enumMap.get(from)).get(to);
        }

        public Phase2 getFrom() {
            return this.from;
        }

        public Phase2 getTo() {
            return this.to;
        }
    }
}
```
