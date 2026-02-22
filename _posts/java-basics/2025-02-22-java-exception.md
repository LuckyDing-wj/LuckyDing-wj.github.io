---
layout: post
title: "Java 异常处理详解"
date: 2025-02-22 23:30:00 +0800
categories: [Java基础]
tags: [Java, 异常, 最佳实践]
---

## 一、异常体系结构

```
Throwable
├── Error（错误，程序无法处理）
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
└── Exception（异常，程序可以处理）
    ├── RuntimeException（运行时异常， unchecked）
    │   ├── NullPointerException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── ArithmeticException
    │   ├── ClassCastException
    │   └── NumberFormatException
    └── 非运行时异常（编译时异常， checked）
        ├── IOException
        ├── SQLException
        ├── FileNotFoundException
        └── ClassNotFoundException
```

---

## 二、try-catch-finally

### 基本语法

```java
public class ExceptionDemo {
    public static void main(String[] args) {
        try {
            // 可能出现异常的代码
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            // 处理异常
            System.out.println("除数不能为0");
        } finally {
            // 无论是否异常都会执行
            System.out.println("finally 块执行");
        }
    }
}
```

### 多异常捕获

```java
try {
    // 代码
} catch (NullPointerException e) {
    System.out.println("空指针异常");
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("数组越界");
} catch (Exception e) {
    // 兜底处理，子类异常要写在前面
    System.out.println("其他异常: " + e.getMessage());
}
```

### Java 7+ 多异常合并

```java
try {
    // 代码
} catch (NullPointerException | ArrayIndexOutOfBoundsException e) {
    System.out.println("发生异常: " + e.getMessage());
}
```

### try-with-resources（自动关闭资源）

```java
// 传统方式
try {
    FileInputStream fis = new FileInputStream("test.txt");
    // 使用资源
} catch (IOException e) {
    e.printStackTrace();
} finally {
    // 手动关闭
    if (fis != null) {
        try {
            fis.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

// try-with-resources（推荐）
try (FileInputStream fis = new FileInputStream("test.txt");
     BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
    // 自动关闭资源，实现 AutoCloseable 接口
} catch (IOException e) {
    e.printStackTrace();
}
```

---

## 三、throw 与 throws

### throws 声明异常

```java
public class FileDemo {
    // 声明可能抛出的异常
    public static void readFile(String path) throws IOException, FileNotFoundException {
        FileReader reader = new FileReader(path);
        // 读取操作
        reader.close();
    }

    public static void main(String[] args) {
        try {
            readFile("test.txt");
        } catch (IOException e) {
            System.out.println("文件读取失败: " + e.getMessage());
        }
    }
}
```

### throw 抛出异常

```java
public class AgeValidator {
    // 主动抛出异常
    public static void setAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("年龄必须在 0-150 之间");
        }
        // 正常逻辑
    }

    public static void main(String[] args) {
        try {
            setAge(-5);
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());  // 年龄必须在 0-150 之间
        }
    }
}
```

---

## 四、自定义异常

### 定义自定义异常

```java
// 自定义检查时异常
public class InsufficientFundsException extends Exception {
    private double amount;
    private double balance;

    public InsufficientFundsException(double amount, double balance) {
        super("余额不足：需要 " + amount + "，当前余额 " + balance);
        this.amount = amount;
        this.balance = balance;
    }

    public double getAmount() {
        return amount;
    }

    public double getBalance() {
        return balance;
    }
}

// 自定义运行时异常
public class InvalidInputException extends RuntimeException {
    public InvalidInputException(String message) {
        super(message);
    }
}
```

### 使用自定义异常

```java
public class BankAccount {
    private double balance;

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException(amount, balance);
        }
        balance -= amount;
    }

    public static void main(String[] args) {
        BankAccount account = new BankAccount(100);
        try {
            account.withdraw(150);
        } catch (InsufficientFundsException e) {
            System.out.println(e.getMessage());
            System.out.println("还差: " + (e.getAmount() - e.getBalance()));
        }
    }
}
```

---

## 五、常见异常类

| 异常类型 | 说明 | 示例场景 |
|---------|------|---------|
| NullPointerException | 空指针异常 | 调用 null 对象的方法 |
| ArrayIndexOutOfBoundsException | 数组越界 | 访问不存在的数组索引 |
| ClassCastException | 类型转换异常 | 错误的类型强制转换 |
| NumberFormatException | 数字格式异常 | "abc" 转数字 |
| ArithmeticException | 算术异常 | 除以 0 |
| IllegalArgumentException | 非法参数 | 传入无效参数 |
| IndexOutOfBoundsException | 索引越界 | List 访问越界 |
| NoSuchElementException | 元素不存在 | 空集合获取元素 |
| ConcurrentModificationException | 并发修改 | 迭代时修改集合 |

### 异常示例

```java
// NullPointerException
String str = null;
str.length();  // NPE

// ArrayIndexOutOfBoundsException
int[] arr = new int[5];
arr[10] = 1;  // 越界

// ClassCastException
Object obj = "Hello";
Integer num = (Integer) obj;  // 类型不匹配

// NumberFormatException
int num = Integer.parseInt("abc");

// ArithmeticException
int result = 10 / 0;

// ConcurrentModificationException
List<String> list = new ArrayList<>(Arrays.asList("A", "B", "C"));
for (String item : list) {
    if ("B".equals(item)) {
        list.remove(item);  // 迭代时修改会抛异常
    }
}
// 解决方案：使用 Iterator.remove() 或 removeIf()
```

---

## 六、异常最佳实践

### 1. 异常处理原则

```java
// ❌ 不要捕获所有异常
try {
    // 代码
} catch (Exception e) {
    // 吞掉异常
}

// ✅ 针对性处理
try {
    // 代码
} catch (SpecificException e) {
    // 具体处理
}

// ❌ 不要用异常处理流程控制
try {
    if (list.size() > 0) {
        // ...
    }
} catch (IndexOutOfBoundsException e) {
    // ...
}

// ✅ 正确的做法
if (list.size() > 0) {
    // ...
}
```

### 2. 异常链（保留原始异常）

```java
public class ExceptionChain {
    public static void main(String[] args) {
        try {
            method1();
        } catch (Exception e) {
            // 打印完整异常链
            e.printStackTrace();
        }
    }

    static void method1() throws Exception {
        try {
            method2();
        } catch (IOException e) {
            // 包装原始异常，保留异常链
            throw new Exception("业务处理失败", e);
        }
    }

    static void method2() throws IOException {
        throw new IOException("文件读取失败");
    }
}
```

### 3. 日志记录

```java
import java.util.logging.*;

public class LoggingException {
    private static final Logger logger = Logger.getLogger(LoggingException.class.getName());

    public void processData(String data) {
        try {
            // 处理逻辑
        } catch (Exception e) {
            // 记录异常
            logger.log(Level.SEVERE, "处理数据时发生异常: " + data, e);
            // 或者抛出给上层处理
            throw new RuntimeException("处理失败", e);
        }
    }
}
```

### 4. 清理资源

```java
public class ResourceCleanup {
    public void readFile(String path) {
        FileInputStream fis = null;
        try {
            fis = new FileInputStream(path);
            // 读取操作
        } catch (IOException e) {
            logger.log(Level.WARNING, "读取文件失败", e);
        } finally {
            // 确保资源释放
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException e) {
                    logger.log(Level.WARNING, "关闭流失败", e);
                }
            }
        }
    }

    // 推荐使用 try-with-resources
    public void readFileAutoClose(String path) {
        try (FileInputStream fis = new FileInputStream(path)) {
            // 读取操作
        } catch (IOException e) {
            logger.log(Level.WARNING, "读取文件失败", e);
        }
        // 自动关闭
    }
}
```

---

## 七、异常与性能

### 1. 避免不必要的异常

```java
// ❌ 使用异常控制流程
public boolean isValid(String input) {
    try {
        Integer.parseInt(input);
        return true;
    } catch (NumberFormatException e) {
        return false;
    }
}

// ✅ 使用条件判断
public boolean isValid(String input) {
    if (input == null || input.isEmpty()) {
        return false;
    }
    for (char c : input.toCharArray()) {
        if (!Character.isDigit(c)) {
            return false;
        }
    }
    return true;
}
```

### 2. 异常创建开销

```java
// 填充堆栈信息开销大
new Exception().fillInStackTrace();

// 如果不需要堆栈信息（如预期异常）
public class FastException extends RuntimeException {
    @Override
    public Throwable fillInStackTrace() {
        return this;  // 跳过堆栈填充
    }
}
```

---

## 八、Optional 避免空指针

### 创建 Optional

```java
import java.util.Optional;

// 创建方式
Optional<String> empty = Optional.empty();
Optional<String> of = Optional.of("Hello");        // 值不能为 null
Optional<String> nullable = Optional.ofNullable(null); // 值可以为 null
```

### 常用方法

```java
Optional<String> opt = Optional.ofNullable(getUserName());

// isPresent / isEmpty（Java 11+）
if (opt.isPresent()) {
    System.out.println(opt.get());
}

// ifPresent 有值时执行
opt.ifPresent(name -> System.out.println("用户名: " + name));

// orElse 默认值
String name = opt.orElse("匿名用户");

// orElseGet 延迟计算默认值
String name2 = opt.orElseGet(() -> generateDefaultName());

// orElseThrow 无值时抛异常
String name3 = opt.orElseThrow(() -> new RuntimeException("用户名不存在"));

// map 转换
Optional<Integer> length = opt.map(String::length);

// flatMap 避免嵌套 Optional
Optional<String> city = getUser()
    .flatMap(User::getAddress)
    .flatMap(Address::getCity);

// filter 过滤
Optional<String> longName = opt.filter(n -> n.length() > 3);
```

### 实际应用

```java
// ❌ 传统空指针检查
public String getCityName(User user) {
    if (user != null) {
        Address address = user.getAddress();
        if (address != null) {
            City city = address.getCity();
            if (city != null) {
                return city.getName();
            }
        }
    }
    return "未知";
}

// ✅ 使用 Optional
public String getCityName(User user) {
    return Optional.ofNullable(user)
        .map(User::getAddress)
        .map(Address::getCity)
        .map(City::getName)
        .orElse("未知");
}
```

---

## 练习题

1. 编写一个程序，从文件中读取数字并计算平均值，处理所有可能的异常
2. 实现一个带重试机制的方法
3. 自定义一个异常类，用于表示配置错误
4. 使用 try-with-resources 处理多个资源

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

