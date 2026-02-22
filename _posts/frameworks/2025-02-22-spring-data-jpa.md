---
layout: post
title: "Spring Data JPA 入门"
date: 2025-02-22 22:20:00 +0800
categories: [常用框架]
tags: [Spring Data JPA, ORM, JPA]
---

## 一、Spring Data JPA 是什么

Spring Data JPA 基于 JPA 规范，提供 Repository 抽象，减少 DAO 层样板代码。

---

## 二、实体与映射

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
}
```

---

## 三、Repository 接口

```java
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByUsername(String username);
}
```

常见能力：

- 通用 CRUD
- 方法名解析生成查询
- 分页与排序

---

## 四、分页与排序

```java
Page<User> page = userRepository.findAll(PageRequest.of(0, 10));
List<User> users = page.getContent();
```

---

## 五、常用注解

- `@Entity` / `@Table`
- `@Id` / `@GeneratedValue`
- `@OneToMany` / `@ManyToOne`
- `@JoinColumn`

---

## 六、注意事项

- 懒加载注意 N+1 问题
- 建议明确使用 `fetch = FetchType.LAZY`
- 复杂查询可结合 `@Query`

---

## 七、学习资源

- [Spring Data JPA 文档](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
- [Hibernate ORM 文档](https://hibernate.org/orm/documentation/)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

