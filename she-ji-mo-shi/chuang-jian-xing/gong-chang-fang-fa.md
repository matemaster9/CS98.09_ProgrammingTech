---
description: guava提供的集合工厂
---

# 工厂方法

<pre class="language-java"><code class="lang-java"><strong>// 创建初识容量为1024的HashMap
</strong><strong>Maps.newHashMapWithExpectedSize(1024);
</strong>
// 工厂方法
public static &#x3C;K extends @Nullable Object, V extends @Nullable Object>
    HashMap&#x3C;K, V> newHashMapWithExpectedSize(int expectedSize) {
  return new HashMap&#x3C;>(capacity(expectedSize));
}
</code></pre>
