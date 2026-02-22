---
layout: post
title: "Java 泛型入门与进阶"
date: 2025-02-22 21:45:00 +0800
categories: [Java基础]
tags: [Java, 泛型, 进阶]
---

## 一、为什么需要泛型

泛型用于在编译期提供类型检查，提升代码复用性和安全性。

- 避免强制类型转换
- 提高可读性
- 减少运行时错误

---

## 二、泛型类

```java
public class Box<T> {
    private T value;

    public void set(T value) {
        this.value = value;
    }

    public T get() {
        return value;
    }
}

Box<String> box = new Box<>();
box.set("hello");
```

---

## 三、泛型方法

```java
public class Utils {
    public static <T> T first(T[] arr) {
        return arr[0];
    }
}

String first = Utils.first(new String[]{"a", "b"});
```

---

## 四、通配符（?）

### 1. 上界通配符

```java
public void printNumbers(List<? extends Number> list) {
    for (Number n : list) {
        System.out.println(n);
    }
}
```

### 2. 下界通配符

```java
public void addIntegers(List<? super Integer> list) {
    list.add(1);
}
```

记忆规则：**PECS**（Producer Extends, Consumer Super）

---

## 五、类型擦除

Java 的泛型是通过类型擦除实现的：

- 编译后泛型类型信息被擦除
- 运行时无法获取具体泛型参数

```java
List<String> a = new ArrayList<>();
List<Integer> b = new ArrayList<>();
System.out.println(a.getClass() == b.getClass()); // true
```

---

## 六、常见错误与最佳实践

- 不要使用原始类型（raw type）
- 泛型类中不要用 `T.class`
- 多用 `List<?>` 表示只读场景

---

## 七、练习题

1. 定义一个泛型 `Pair<K, V>` 类
2. 编写泛型方法：找出数组中最大值
3. 将 `List<Integer>` 安全地传给只读方法

---

## 八、学习资源

- [Oracle 泛型教程](https://docs.oracle.com/javase/tutorial/java/generics/)
- [泛型 FAQ - Stack Overflow](https://stackoverflow.com/questions/45199/what-is-a-raw-type-and-why-shouldnt-we-use-it)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

