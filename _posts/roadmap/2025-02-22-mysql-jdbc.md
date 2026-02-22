---
layout: post
title: "阶段四：MySQL 与 JDBC 基础"
date: 2025-02-22 23:10:00 +0800
categories: [学习路线]
tags: [MySQL, JDBC, 数据库]
---

## 一、阶段目标

- 掌握关系型数据库基础概念
- 能够使用 JDBC 进行增删改查
- 了解 SQL 优化与索引基础

---

## 二、MySQL 基础

核心概念：

- 数据库、表、行、列
- 主键与外键
- 约束与索引

常用 SQL：

```sql
select * from users where id = 1;
insert into users(name, age) values('Tom', 18);
update users set age = 19 where id = 1;
delete from users where id = 1;
```

---

## 三、JDBC 基本流程

```java
Connection conn = DriverManager.getConnection(url, user, password);
PreparedStatement ps = conn.prepareStatement("select * from users where id = ?");
ps.setLong(1, 1L);
ResultSet rs = ps.executeQuery();
```

关键步骤：

1. 获取连接
2. 预编译 SQL
3. 执行与处理结果
4. 关闭资源

---

## 四、资源关闭最佳实践

```java
try (Connection conn = dataSource.getConnection();
     PreparedStatement ps = conn.prepareStatement(sql)) {
    ...
}
```

---

## 五、学习建议

- 学会解释执行计划
- 理解索引与查询性能的关系
- 练习 JDBC 批量操作

---

## 六、学习资源

- [MySQL 官方文档](https://dev.mysql.com/doc/)
- [JDBC 教程 - Oracle](https://docs.oracle.com/javase/tutorial/jdbc/)

---

## 七、相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*
