---
layout: post
title: "Spring Cloud 微服务基础"
date: 2025-02-22 22:25:00 +0800
categories: [常用框架]
tags: [Spring Cloud, 微服务, 分布式]
---

## 一、Spring Cloud 是什么

Spring Cloud 提供一套微服务常用组件，帮助快速构建分布式系统。

常见能力：

- 服务发现
- 配置中心
- API 网关
- 调用治理与熔断

---

## 二、核心组件概览

- 服务注册与发现：Eureka / Consul / Nacos
- 负载均衡：Spring Cloud LoadBalancer
- 配置中心：Config / Nacos
- 网关：Spring Cloud Gateway
- 监控与追踪：Sleuth / Zipkin

---

## 三、基础示例（服务注册）

```yaml
spring:
  application:
    name: order-service
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

---

## 四、网关示例

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/api/users/**
```

---

## 五、学习建议

- 先掌握 Spring Boot 与服务拆分
- 再学习注册中心、网关、配置中心
- 结合实际业务做链路追踪与限流

---

## 六、学习资源

- [Spring Cloud 官方文档](https://spring.io/projects/spring-cloud)
- [Spring Cloud Gateway 文档](https://docs.spring.io/spring-cloud-gateway/reference/)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

