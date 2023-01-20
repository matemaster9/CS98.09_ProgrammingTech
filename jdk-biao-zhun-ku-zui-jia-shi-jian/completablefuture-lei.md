# CompletableFuture类

## 异步计算

`static CompletableFuture<Void> runAsync(Runnable runnable)`

`static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier)`



## 多任务返回策略

`static CompletableFuture<Void> allOf(CompletableFuture<?>... cfs)`

`static CompletableFuture<Object> anyOf(CompletableFuture<?>... cfs)`



## 异步计算完成后操作

