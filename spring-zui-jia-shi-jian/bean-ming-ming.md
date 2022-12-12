---
description: bean默认命名规则及别名
---

# bean命名

## bean的默认命名规则

默认小驼峰，对于连续大写的bean，保持原名

```java
// bean: DDQueryService
@Component
public class DDQueryService {
    // services
}

// bean: rubikQueryConfig
@Configuration
public class RubikQueryConfig {
    // config
}
```

@Bean 的命名方式：默认方法名，可制定id和别名

```java
// bean: staticFactoryMethod
@Bean
public static String staticFactoryMethod() {
    return "staticFactoryMethod";
}

// bean: instanceFactoryMethod
@Bean
public String instanceFactoryMethod() {
    return "instanceFactoryMethod";
}

// bean: SpecificNamingBean
@Bean("SpecificNamingBean")
public String namingBean() {
    return "namingBean";
}
```
