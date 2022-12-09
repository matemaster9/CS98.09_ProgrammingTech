---
description: 针对非配置数据的yml
---

# YAML文件使用

```java
YamlMapFactoryBean yamlMapFactoryBean = new YamlMapFactoryBean();
yamlMapFactoryBean.setResources(new ClassPathResource("configurable.yml"));
Map<String, Object> configurableMap = yamlMapFactoryBean.getObject();
// configurableMap内容：
// {spring={datasource={hikari={connection-timeout=30000, idle-timeout=600000, max-lifetime=1800000, maximum-pool-size=10, minimum-idle=10, pool-name=HikariCP, driver-class-name=com.mysql.cj.jdbc.Driver, username=root, password=matemaster}, url=jdbc:mysql://domain:3306/StandardWebServerConfigCenter}}}
```

{% code title="configurable.yml" %}
```yaml
spring:
  datasource:
    hikari:
      connection-timeout: 30000
      idle-timeout: 600000
      max-lifetime: 1800000
      maximum-pool-size: 10
      minimum-idle: 10
      pool-name: HikariCP
      driver-class-name: com.mysql.cj.jdbc.Driver
      username: root
      password: matemaster
    url: jdbc:mysql://domain:3306/StandardWebServerConfigCenter
```
{% endcode %}

> Map\<String, Object> configurableMap
>
> String: yaml键
>
> Object: LinkedHashMap或字面值

