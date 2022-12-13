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
