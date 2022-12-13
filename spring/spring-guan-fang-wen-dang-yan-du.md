# Spring官方文档研读

## Spring IOC容器与beans

1. 什么是IOC与DI （概念）？
2. BeanFactory与ApplicationContext ？
3. Spring bean ？

SpringIOC的主要实现代码：`org.springframework.beans` 、 `org.springframework.context`

* configuration metadata？
* ApplicationContext的主要实现？
* spring容器初始化、装配、管理bean的过程？

## Spring bean概述

* Spring bean在容器中的存在形式？
* BeanDefinition包含的主要信息？

### bean命名

* Spring容器的bean自动命名
* bean的别名，命名规则

### bean的实例化

* 构造器实例化
* 静态工厂方法实例化
* 对象工厂方法实例化
* FactoryBean
* 确定bean的运行时类型

## 依赖处理

### 依赖注入

* 构造器注入
* setter注入
* 方法注入
* 注入注解

### 依赖解析

* 依赖解析过程
* 循环依赖处理

### 其他依赖处理

* depend-on
* 延迟初始化
* 自动装配

## bean范围



## 自定义bean









``
