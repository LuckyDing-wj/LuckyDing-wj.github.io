---
layout: post
title: "Spring Boot 快速上手"
date: 2025-02-22 22:10:00 +0800
categories: [常用框架]
tags: [Spring Boot, 自动配置, 微服务]
---

## 一、Spring Boot 是什么

Spring Boot 旨在简化 Spring 应用的搭建与部署，提供自动配置、Starter 依赖、生产就绪特性。

---

## 二、快速创建项目

常用方式：

- Spring Initializr（网页）
- IDE 集成（IntelliJ IDEA）

关键依赖：

- `spring-boot-starter-web`
- `spring-boot-starter-test`
- `spring-boot-starter-data-jpa`

---

## 三、最小示例

```java
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

---

## 四、自动配置原理

- `@SpringBootApplication` 组合注解
- `spring.factories` / `AutoConfiguration`
- 条件注解：`@ConditionalOnClass` 等

---

## 五、配置与环境

```yaml
server:
  port: 8080
spring:
  profiles:
    active: dev
```

常见文件：

- `application.yml`
- `application-dev.yml`

---

## 六、Actuator 生产就绪

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
```

常用端点：

- `/actuator/health`
- `/actuator/info`
- `/actuator/metrics`

---

## 七、学习建议

- 先理解 Spring，再学习 Boot 自动配置
- 熟悉常用 Starter 与配置
- 配合常见组件（JPA、MyBatis、Redis）

---

## 八、学习资源

- [Spring Boot 官方文档](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Spring Initializr](https://start.spring.io/)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

