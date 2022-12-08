---
description: guava在工作中的使用记录
---

# guava开发应用记录

1. 集合的拆分
2. 集合的交集、并集、差集
3. 工厂方法创建集合



## 集合工具🔨

### Lists

* 工厂方法创建ArrayList（List旗下容器）

```java
Lists.newArrayList();
Lists.newArrayList("mate", "master");
```

* 创建asList只读视图

```java
Lists.asList("OnePlusArrayList<>", new String[0]);
Lists.asList("first", "second", new String[]{"TwoPlusArrayList<>"});
```

* 切分list

```java
@Test
void partition() {
    List<Integer> numbers = new ArrayList<>();
    for (int i = 0; i < 100; i++) {
        numbers.add(i);
    }
    Lists.partition(numbers, 10);
}
```

* charactersOf字符串转char list

```java
ImmutableList<Character> characters = Lists.charactersOf("matemaster");
log.debug(characters.toString());
```

* 求集合的笛卡尔积

```java
List<List<Object>> cartesianProduct = Lists.cartesianProduct(alphabet, numbers);
```

_<mark style="background-color:orange;">**Lists的使用建议**</mark>_：

`lists.newArrayList` 利用工厂方法方法创建List接口容器，语意明确

`Lists.asList` 创建列表视图，底层使用OnePlusArrayList与TwoPlusArrayList继承AbstractList实现不可变的只读列表

`Lists.partition` 底层利用继承实现AbstractList\<List\<?>>，并利用get方法实现伪装切分，实际存储容器为List\<List\<?>>形式，只在获取子列表时做处理，与原有集合共享视图！一句话：子列表只读，总列表与源列表共享视图

### Sets

* 工厂方法创建set（set旗下的容器）

```java
Sets.newHashSet();
Sets.newHashSetWithExpectedSize(1024);
```

* 集合的交、并、差、对称差

```java
Set<Integer> set1 = Sets.newHashSet(0, 1, 2, 3, 4, 5, 6);
Set<Integer> set2 = Sets.newHashSet(5, 6, 7, 8, 9, 10, 11);
Sets.SetView<Integer> intersection = Sets.intersection(set1, set2);
Sets.SetView<Integer> union = Sets.union(set1, set2);
Sets.SetView<Integer> difference = Sets.difference(set1, set2);
Sets.SetView<Integer> symmetricDifference = Sets.symmetricDifference(set1, set2);
```

* 求制定size的子集(beta版本未来可能删除，31.1-jre)

```java
Set<Integer> set1 = Sets.newHashSet(0, 1, 2, 3, 4, 5, 6);
Set<Set<Integer>> combinations = Sets.combinations(set1, 2);
```

* 求集合幂集（最多支持31不同元素集合，size = 2^31）

```java
Set<Integer> set1 = Sets.newHashSet(0, 1, 2);
Sets.powerSet(set1);
```

### Maps

```java
Map<String, Object> mapping = Maps.newHashMap();
Map<String, Object> mapping2 = Maps.newHashMapWithExpectedSize(64);
```



### Collections2

关于几个静态方法的使用建议：

* filter与transform可以使用stream api替代
* permutation排列属于beta操作

## Immutable系列

这里以list不可变为例：

* 创建不可变集合

```java
ImmutableList<Integer> immutableList1 = ImmutableList.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
ImmutableList<Integer> immutableList2 = ImmutableList.copyOf(new Integer[]{0, 1, 2, 3, 4, 5, 6, 7, 8, 9});
ImmutableList<Integer> immutableList3 = ImmutableList.<Integer>builder().add(0, 1, 2, 3, 4, 5, 6, 7, 8, 9).build();
```

存在三种形式的创建：

1. of
2. copyOf
3. builder

* 提供不可变列表视图，不可变集合都提供asList方法提供不可变列表视图



## Hash函数支持

[JDK标准库及开源库](https://app.gitbook.com/s/svcZMNqpQ2QiYAlzW99P/jdk-biao-zhun-ku-ji-kai-yuan-ku "mention") - Hash函数



## ListenableFuture

回调支持

```java
ListeningExecutorService listeningExecutorService = MoreExecutors.listeningDecorator(pool);
ListenableFuture<String> listenableFuture = listeningExecutorService.submit(() -> {
    String name = Thread.currentThread().getName();
    System.out.println(name);
    return name;
});

Futures.addCallback(listenableFuture, new FutureCallback<String>() {
    @Override
    public void onSuccess(String result) {
        System.out.println(result + "成功执行");
    }

    @Override
    public void onFailure(Throwable t) {
        System.out.println(t);
    }
}, pool);
```

链式异步操作

