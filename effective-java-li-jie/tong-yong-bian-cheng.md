# 通用编程

## 最小化局部变量

```java
for(int i = 0, limit = 100; i < limit; i ++) {
    System.out.print("");
}
```

## for-each优先

针对集合数组，以及实现iterable接口，遍历时，只关心元素内容的情况下，for-each方式优先

使用for循环的情况：

1. 进行元素删除
2. 取代元素
3. 并行集合操作

