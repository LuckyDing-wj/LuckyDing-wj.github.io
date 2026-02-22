---
layout: post
title: "Spring Framework 核心概念"
date: 2025-02-22 22:00:00 +0800
categories: [常用框架]
tags: [Spring, IoC, AOP, 事务]
---

## 一、Spring 是什么

Spring Framework 是 Java 生态最核心的应用框架，提供 IoC 容器、AOP、事务管理等基础能力，是其他 Spring 家族项目的基础。

---

## 二、IoC 与依赖注入

IoC（控制反转）让对象的创建与管理交给容器，常用注入方式：

- 构造器注入（推荐）
- Setter 注入
- 字段注入（不推荐）

```java
@Service
public class OrderService {
    private final UserRepository userRepository;

    public OrderService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

---

## 三、Bean 生命周期与作用域

常见作用域：

- `singleton`：默认，容器唯一实例
- `prototype`：每次获取创建新实例
- `request`/`session`：Web 场景

生命周期流程（简化）：

1. 实例化
2. 属性注入
3. 初始化回调
4. 容器销毁时回调

---

## 四、AOP 基本概念

术语：

- 切面（Aspect）
- 通知（Advice）
- 切点（Pointcut）
- 连接点（JoinPoint）

常见用途：

- 日志
- 事务
- 权限与审计

---

## 五、事务管理

```java
@Service
public class PaymentService {
    @Transactional
    public void pay() {
        // 数据库操作
    }
}
```

要点：

- 默认对 `RuntimeException` 回滚
- 可设置传播行为、隔离级别、超时

---

## 六、常用注解

- `@Component` / `@Service` / `@Repository`
- `@Configuration` / `@Bean`
- `@Autowired` / `@Qualifier`

---

## 七、学习建议

- 先理解 IoC 容器与 Bean 生命周期
- 再学习 AOP 与事务
- 结合 Spring Boot 实战

---

## 八、学习资源

- [Spring Framework 官方文档](https://docs.spring.io/spring-framework/reference/)
- [Spring 官方 Guides](https://spring.io/guides)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

