---
layout: post
title: "Java 反射机制详解"
date: 2025-02-22 21:40:00 +0800
categories: [Java基础]
tags: [Java, 反射, 进阶]
---

## 一、反射是什么

反射（Reflection）是指程序在运行时动态获取类的信息，并能在运行时创建对象、访问字段、调用方法的能力。

常见使用场景：

- 框架底层（Spring、MyBatis 等）
- 动态代理
- 序列化与反序列化
- 插件化与解耦

---

## 二、核心入口：Class 对象

```java
// 方式一：通过类名
Class<?> clazz1 = String.class;

// 方式二：通过对象
String name = "java";
Class<?> clazz2 = name.getClass();

// 方式三：通过全限定名
Class<?> clazz3 = Class.forName("java.lang.String");
```

常用方法：

- `getName()`：获取全限定类名
- `getSimpleName()`：获取简单类名
- `getPackage()`：获取包信息

---

## 三、创建对象

```java
Class<?> userClass = Class.forName("com.demo.User");
Object user = userClass.getDeclaredConstructor().newInstance();
```

说明：

- 推荐使用 `getDeclaredConstructor().newInstance()`，避免 `newInstance()` 的异常问题。
- 如果构造方法是私有的，需要先 `setAccessible(true)`。

---

## 四、访问字段（Field）

```java
Field ageField = userClass.getDeclaredField("age");
ageField.setAccessible(true);
ageField.set(user, 18);

int age = (int) ageField.get(user);
```

常用方法：

- `getDeclaredFields()`：获取所有字段
- `getFields()`：获取 public 字段（含父类）

---

## 五、调用方法（Method）

```java
Method sayHi = userClass.getDeclaredMethod("sayHi", String.class);
sayHi.setAccessible(true);
sayHi.invoke(user, "OpenCode");
```

注意：

- `invoke` 返回值是 `Object`
- 如果方法是 `static`，第一个参数传 `null`

---

## 六、反射与动态代理

反射常配合动态代理使用，用于在运行时生成代理类，实现方法增强。

```java
Object proxy = Proxy.newProxyInstance(
    userClass.getClassLoader(),
    userClass.getInterfaces(),
    (obj, method, args) -> {
        System.out.println("before");
        Object result = method.invoke(user, args);
        System.out.println("after");
        return result;
    }
);
```

---

## 七、常见问题与建议

- 反射会破坏封装性，应慎用 `setAccessible(true)`
- 反射性能低于直接调用，热点路径避免使用
- 不要依赖字段名做强耦合

---

## 八、练习题

1. 通过反射读取类的所有字段名与类型
2. 通过反射调用私有方法并返回结果
3. 自己实现一个简单的 `BeanUtils` 属性复制方法

---

## 九、学习资源

- [Oracle 反射 API 文档](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/package-summary.html)
- [Java 反射教程 - 菜鸟教程](https://www.runoob.com/java/java-reflection.html)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

