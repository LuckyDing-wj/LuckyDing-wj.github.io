---
layout: post
title: "Java Lambda 表达式与函数式编程"
date: 2025-02-22 21:50:00 +0800
categories: [Java基础]
tags: [Java, Lambda, 函数式编程]
---

## 一、Lambda 是什么

Lambda 表达式是 Java 8 引入的语法，用来简化匿名内部类写法，常用于函数式接口。

---

## 二、函数式接口

只有一个抽象方法的接口称为函数式接口，常用的有：

- `Runnable`
- `Callable`
- `Comparator`
- `Function<T, R>`
- `Predicate<T>`
- `Consumer<T>`
- `Supplier<T>`

```java
@FunctionalInterface
public interface Converter<F, T> {
    T convert(F from);
}
```

---

## 三、Lambda 基本语法

```java
(参数) -> { 方法体 }
```

示例：

```java
Runnable r = () -> System.out.println("running");

Comparator<Integer> c = (a, b) -> Integer.compare(a, b);

Function<String, Integer> f = s -> s.length();
```

---

## 四、方法引用

方法引用是 Lambda 的简写形式：

```java
List<String> list = Arrays.asList("a", "bb", "ccc");
list.forEach(System.out::println);
```

常见类型：

- `类名::静态方法`
- `对象::实例方法`
- `类名::实例方法`
- `类名::new`

---

## 五、Stream API 简介

```java
List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);
int sum = nums.stream()
    .filter(n -> n % 2 == 0)
    .mapToInt(n -> n * 2)
    .sum();
```

常用操作：

- 中间操作：`filter`、`map`、`sorted`
- 终止操作：`forEach`、`collect`、`count`

---

## 六、Optional 简介

```java
Optional<String> opt = Optional.ofNullable(user.getName());
String name = opt.orElse("unknown");
```

用处：

- 避免空指针
- 使空值处理更明确

---

## 七、最佳实践

- Lambda 只做小逻辑，复杂逻辑单独抽方法
- 避免捕获可变外部变量
- 流式操作注意性能与可读性

---

## 八、练习题

1. 用 Lambda 实现一个排序器
2. 使用 Stream 统计字符串列表中长度大于 3 的数量
3. 用 Optional 改造一次空值判断

---

## 九、学习资源

- [Oracle Lambda 教程](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
- [Stream API 指南](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

