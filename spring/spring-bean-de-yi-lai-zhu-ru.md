---
description: 属性注入+方法注入
---

# Spring Bean的依赖注入

## 属性注入

### 构造器注入

构造器注入，在参数名称与Spring管理的bean的 名称一致时，按照名称注入，反之按照类型注入

```java
@RequiredArgsConstructor
public class ConstructorDependencyInjection {
    private final SpringManagedBean springManagedBean;
}
```

构造器注入的优点：

* 得到的对象完全初始化
* 可以检查参数，避免npe
* 对象不可变

### setter注入

setter注入逻辑与构造器相似

```java
@Component
public class SetterDependencyInjection {
    private SpringManagedBean springManagedBean;

    @Bean
    public void setSpringManagedBean(SpringManagedBean springManagedBean) {
        this.springManagedBean = springManagedBean;
    }
}
```

## 方法注入

> Q：方法注入主要解决什么问题？

A：方法注入，主要针对单例bean注入多例bean的场景，因为单例bean只赋值属性一次，普通属性注入会导致，注入的多例bean是单例（多例bean失去了多例的意义）。

### 实现ApplicationContextAware接口



### @lookup 注解注入

