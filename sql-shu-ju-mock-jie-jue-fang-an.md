# SQL数据mock解决方案

## 项目背景

鉴于目前在创建数据库表格及仿真数据库数据，依赖手工输入的方式，有开发效率低下以及容易出错的问题。现在需要系统提供下列功能：

* 客户端输入需要创建的数据库表的基本信息，服务端基于基本信息，生成（创建、插入）数据库表的SQL语句，并生成相应的mock数据，提供Java数据库PO对象，JSON数据
* 提供数据表持久化功能，并提供查询，以及获取建表相关SQL

## 影响分析

暂无（全新功能不存在业务影响）

## 功能设计

### sql-mock

客户端输入表单数据(这里基于datagrip数据生成格式)

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

| 字段             | 含义    |
| -------------- | ----- |
| datebase\_name | 数据库名称 |
| table\_name    | 表名    |
| columns        | 行信息   |
| keys           | 键     |
| indexes        | 索引    |

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption><p>业务流程</p></figcaption></figure>





## 功能验收
