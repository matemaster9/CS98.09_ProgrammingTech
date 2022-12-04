---
description: 结合spring官方文档理解Declaring a Pointcut
---

# SpringAOP 切点

## 官方文档

{% embed url="https://docs.spring.io/spring-framework/docs/5.2.22.RELEASE/spring-framework-reference/core.html#aop" %}
SpringAOP
{% endembed %}

## SpringAOP支持的PCD

PCD: Point Cut Designator 切点代号

| PCD（AspectJ）  | 意义                                                       |
| ------------- | -------------------------------------------------------- |
| `execution`   | 匹配方法执行连接点，一般包括类、方法、包                                     |
| `within`      | 匹配类型的连接点，                                                |
| `this`        | 匹配目标对象                                                   |
| `target`      | 匹配代理对象                                                   |
| `args`        |                                                          |
| `@target`     |                                                          |
| `@args`       | 限制匹配到连接点（使用 Spring AOP 时方法的执行），其中传递的实际参数的运行时类型具有给定类型的注释。 |
| `@within`     | 匹配包含指定注解的类                                               |
| `@annotation` | 匹配包含指定注解的方法                                              |

