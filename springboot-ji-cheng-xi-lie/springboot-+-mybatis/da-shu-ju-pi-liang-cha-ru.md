---
description: 推荐foreach拼接与批处理
---

# 大数据批量插入

## 三种方式

* 批处理
* foreach拼接
* for循环单条插入

```java
// 批处理
try (SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH)) {
    StorageManagementMapper mapper = sqlSession.getMapper(StorageManagementMapper.class);
    collect.forEach(mapper::insertStorageDetail);
}

// foreach拼接
storageManagementMapper.insertStorageDetailList(mockData());

// for循环单条插入
mockData().forEach(storageManagementMapper::insertStorageDetail);
```

mapper接口

```java
public interface StorageManagementMapper {

    @Insert({"insert into book_storage_detail " +
            "(book_name, specifications, original_inventory, inbound_count, inbound_price, outbound_count, outbound_price, " +
            " balance_inventory, stock_price, remark) " +
            "values " +
            "(#{item.bookName},#{item.specifications},#{item.originalInventory},#{item.inboundCount},#{item.inboundPrice}," +
            "#{item.outboundCount},#{item.outboundPrice},#{item.balanceInventory},#{item.stockPrice},#{item.remark})"})
    int insertStorageDetail(@Param("item") BookStorageDetailDto bookStorageDetail);

    @Insert({"<script> " +
            "insert into book_storage_detail " +
            "(book_name, specifications, original_inventory, inbound_count, inbound_price, outbound_count, outbound_price, " +
            " balance_inventory, stock_price, remark) " +
            "values " +
            "<foreach collection = 'list' item = 'item' separator = ','> " +
            "(#{item.bookName},#{item.specifications},#{item.originalInventory},#{item.inboundCount},#{item.inboundPrice}," +
            "#{item.outboundCount},#{item.outboundPrice},#{item.balanceInventory},#{item.stockPrice},#{item.remark})" +
            "</foreach>" +
            "</script>"})
    int insertStorageDetailList(@Param("list") List<BookStorageDetailDto> bookStorageDetailList);
}
```

## 应用案例

{% embed url="https://github.com/matemaster9/CS98.00_StandardWebServerConfigCenter" %}
仓库地址
{% endembed %}

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption><p>代码位置</p></figcaption></figure>

## 性能测试对比

* 批处理（100000 1s）

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>100000 1s</p></figcaption></figure>

* sql拼接 （100000 8s）

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>100000 8s</p></figcaption></figure>

* for循环（100000 xxxs，太慢了等不及）

> tips：基于网络环境及个人服务器性能差异，具体性能测试时间可能与实际情况不符。但可以得出结论的是，在大数据量插入情况下：**批处理 > sql拼接 > 循环插入**
