---
description: 利用FactoryBean创建bean
---

# FactoryBean接口使用

## 实现FactoryBean接口

> tips：实现了FactoryBean接口的RubikQueryFactory不仅会向容器注册一个rubikQueryFactory， 还会自动生成一个RubikQuery类型的bean

```java
@Component
public class RubikQueryFactory implements FactoryBean<RubikQuery> {

    @Override
    public RubikQuery getObject() throws Exception {
        return new RubikQuery();
    }

    @Override
    public Class<?> getObjectType() {
        return RubikQuery.class;
    }
}
```

```java
public class RubikQuery {
    // query-services
}
```

获取RubikQueryFactory生成的bean：RubikQuery

```java
RubikQueryFactory bean = (RubikQueryFactory) applicationContext.getBean("&rubikQueryFactory");
```

## mybatis-spring应用案例

在mybatis应用中，核心类 SqlSession，有SqlSessionFactory创建。在集成spring中，bean：SqlSessionFactory的创建，是有SqlSessionFactoryBean实现FactoryBean接口，达到在spring容器创建注入SqlSessionFactory的目的

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption><p>SqlSessionFactoryBean</p></figcaption></figure>

```java
@Override
public SqlSessionFactory getObject() throws Exception {
  if (this.sqlSessionFactory == null) {
    afterPropertiesSet();
  }

  return this.sqlSessionFactory;
}
```
