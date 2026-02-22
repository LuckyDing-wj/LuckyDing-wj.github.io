---
layout: post
title: "Java 基础语法入门"
date: 2025-02-22 21:00:00 +0800
categories: [Java基础]
tags: [Java, 基础语法, 入门]
---

## 一、Java 简介

Java 是一门面向对象的编程语言，具有简单、面向对象、分布式、健壮、安全、平台无关、可移植、多线程等特点。

### Java 的特点

- **跨平台性**：一次编写，到处运行（Write Once, Run Anywhere）
- **面向对象**：封装、继承、多态
- **自动内存管理**：垃圾回收机制
- **丰富的类库**：强大的标准库和第三方库

---

## 二、Hello World

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

### 代码解析

- `public class HelloWorld`：定义一个公开的类
- `public static void main(String[] args)`：程序入口
- `System.out.println()`：输出内容到控制台

---

## 三、变量与数据类型

### 基本数据类型

| 类型 | 大小 | 默认值 | 说明 |
|------|------|--------|------|
| byte | 8位 | 0 | 字节型 |
| short | 16位 | 0 | 短整型 |
| int | 32位 | 0 | 整型（默认） |
| long | 64位 | 0L | 长整型 |
| float | 32位 | 0.0f | 单精度浮点 |
| double | 64位 | 0.0d | 双精度浮点（默认） |
| char | 16位 | '\u0000' | 字符型 |
| boolean | - | false | 布尔型 |

### 变量声明示例

```java
// 整数类型
int age = 25;
long population = 7800000000L;

// 浮点类型
float price = 19.99f;
double pi = 3.141592653;

// 字符类型
char grade = 'A';

// 布尔类型
boolean isStudent = true;

// 字符串类型（引用类型）
String name = "张三";
```

---

## 四、运算符

### 算术运算符

```java
int a = 10, b = 3;

System.out.println(a + b);  // 13 加法
System.out.println(a - b);  // 7  减法
System.out.println(a * b);  // 30 乘法
System.out.println(a / b);  // 3  除法（整除）
System.out.println(a % b);  // 1  取余
```

### 关系运算符

```java
int x = 5, y = 10;

System.out.println(x == y);  // false 等于
System.out.println(x != y);  // true  不等于
System.out.println(x < y);   // true  小于
System.out.println(x > y);   // false 大于
System.out.println(x <= y);  // true  小于等于
System.out.println(x >= y);  // false 大于等于
```

### 逻辑运算符

```java
boolean a = true, b = false;

System.out.println(a && b);  // false 与（两者都为true）
System.out.println(a || b);  // true  或（至少一个为true）
System.out.println(!a);      // false 非
```

---

## 五、流程控制

### if-else 条件语句

```java
int score = 85;

if (score >= 90) {
    System.out.println("优秀");
} else if (score >= 60) {
    System.out.println("及格");
} else {
    System.out.println("不及格");
}
```

### switch 语句

```java
int dayOfWeek = 3;
String dayName;

switch (dayOfWeek) {
    case 1:
        dayName = "星期一";
        break;
    case 2:
        dayName = "星期二";
        break;
    case 3:
        dayName = "星期三";
        break;
    // ... 其他 case
    default:
        dayName = "未知";
}
System.out.println(dayName);  // 星期三
```

### for 循环

```java
// 标准 for 循环
for (int i = 0; i < 5; i++) {
    System.out.println("第 " + (i + 1) + " 次循环");
}

// 增强 for 循环（遍历数组/集合）
int[] numbers = {1, 2, 3, 4, 5};
for (int num : numbers) {
    System.out.println(num);
}
```

### while 循环

```java
// while 循环
int i = 0;
while (i < 5) {
    System.out.println("i = " + i);
    i++;
}

// do-while 循环（至少执行一次）
int j = 0;
do {
    System.out.println("j = " + j);
    j++;
} while (j < 5);
```

---

## 六、数组

### 数组声明与初始化

```java
// 声明并初始化
int[] numbers = {1, 2, 3, 4, 5};

// 先声明后初始化
String[] names = new String[3];
names[0] = "张三";
names[1] = "李四";
names[2] = "王五";

// 获取数组长度
System.out.println(numbers.length);  // 5

// 遍历数组
for (int num : numbers) {
    System.out.println(num);
}
```

---

## 七、练习题

1. 编写程序，判断一个数是否为偶数
2. 打印 1-100 之间所有的质数
3. 计算斐波那契数列前 20 项
4. 实现一个简单的计算器（加减乘除）

---

## 八、学习资源

- [Oracle Java 官方文档](https://docs.oracle.com/javase/tutorial/)
- [菜鸟教程 Java 基础](https://www.runoob.com/java/)

---

## 九、相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

