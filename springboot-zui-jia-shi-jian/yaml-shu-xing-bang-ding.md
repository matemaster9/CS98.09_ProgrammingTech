---
description: 基于spring-boot读取yaml文件的三种方式value、setter、constructor、env
---

# yaml属性绑定

## value注解

`@Configuration + @Value`

```java
@Setter
@Getter
@ToString
@Configuration
public class RandomValueProperties {
    @Value("${random-value.secret}")
    private String secret;

    @Value("${random-value.number}")
    private Integer number;

    @Value("${random-value.big-number}")
    private Long bigNumber;

    @Value("${random-value.uuid}")
    private String uuid;

    @Value("${random-value.less-ten}")
    private Integer lessTen;

    @Value("${random-value.range}")
    private Integer range;
}
```

yaml配置

```yaml
random-value:
  secret: ${random.value}
  number: ${random.int}
  big-number: ${random.long(1,9999999999)}
  uuid: ${random.uuid}
  less-ten: ${random.int(10)}
  range: ${random.int[1,100]}
```

## ConfigurationProperties注解

`@Configuration + @CondigurationProperties`

```java
@Setter
@Getter
@ToString
@Configuration
@ConfigurationProperties(prefix = "web.server")
public class WebServerProperties {
    private Map<String, String> application;
    private Map<String, List<String>> config;
    private Map<String, Credential> users;

    @Getter
    @Setter
    public static class Credential {
        private String username;
        private String password;
    }
}
```

yaml配置

```yaml
web.server:
  application:
    name: InjectMapFromYAML
    url: http://injectmapfromyaml.dev
    description: How To Inject a map from a YAML File in Spring Boot
  config:
    ip-list:
      - 10.10.10.10
      - 10.10.10.11
      - 10.10.10.12
      - 10.10.10.13
    file-system:
      - /dev/root
      - /dev/md2
      - /dev/md4
  users:
    root:
      username: root
      password: Ti3Ka3x8T8y0cPrfuYIj
    guest:
      username: guest
      password: m8CW0LO3GGIsclO207DE
```

## ConstructorBinding注解

`@ConstructorBinding + @ConfigurationProperties + @ConfigurationPropertiesScan`

```java
@Getter
@ToString
@ConfigurationProperties(prefix = "app.system")
public class AppSystemProperties {
    private final Map<String, String> monthMap;
    private final Map<String, SystemUser> systemUsers;

    public AppSystemProperties() {
        monthMap = Collections.emptyMap();
        systemUsers = Collections.emptyMap();
    }

    @ConstructorBinding
    public AppSystemProperties(Map<String, String> monthMap, @DefaultValue Map<String, SystemUser> systemUsers) {
        this.monthMap = monthMap;
        this.systemUsers = systemUsers;
    }
}

@SpringBootApplication
@ConfigurationPropertiesScan(basePackageClasses = {AppSystemProperties.class})
public class ConfigApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext configurableApplicationContext = SpringApplication.run(ConfigApplication.class, args);
    }
}
```

yaml配置

```yaml
app.system:
  month-map:
    1: [ Jan. ]
    2: [ Feb. ]
    3: [ Mar. ]
    4: [ Apr. ]
    5: [ May ]
    6: [ Jun. ]
    7: [ Jul. ]
    8: [ Aug. ]
    9: [ Sep. ]
    10: [ Oct. ]
    11: [ Nov. ]
    12: [ Dec. ]
  system-users:
    root: { "username": "mate-master","password": "gLObQK0kZZVMVsOCIhaK" }
    guest: { "username": "jackson","password": "gWf6Tz5WiOgsUqSUFxam" }
```

## StandardEnv

