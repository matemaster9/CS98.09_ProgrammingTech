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
