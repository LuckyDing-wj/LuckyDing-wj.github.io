---
layout: post
title: "JUnit 与 Mockito 单元测试"
date: 2025-02-22 23:05:00 +0800
categories: [开发工具]
tags: [JUnit, Mockito, 测试, TDD]
---

## 一、单元测试的意义

- 保障核心逻辑稳定
- 促进可测试设计
- 便于重构

---

## 二、JUnit 5 基础

```java
class CalculatorTest {
    @Test
    void add_shouldReturnSum() {
        Calculator cal = new Calculator();
        assertEquals(3, cal.add(1, 2));
    }
}
```

常用注解：

- `@Test`
- `@BeforeEach` / `@AfterEach`
- `@DisplayName`

---

## 三、Mockito 基础

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {
    @Mock
    private OrderRepository orderRepository;

    @InjectMocks
    private OrderService orderService;

    @Test
    void create_shouldSaveOrder() {
        orderService.create("A001");
        verify(orderRepository).save(any());
    }
}
```

常用方法：

- `when(...).thenReturn(...)`
- `verify(...)`
- `any()`

---

## 四、测试最佳实践

- 用例命名清晰（should/when）
- 单个测试只验证一个行为
- 避免依赖真实数据库

---

## 五、学习资源

- [JUnit 5 官方文档](https://junit.org/junit5/docs/current/user-guide/)
- [Mockito 文档](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

