---
layout: post
title: "Java 集合框架详解"
date: 2025-02-22 23:00:00 +0800
categories: [Java基础]
tags: [Java, 集合, List, Set, Map, 源码]
---

## 一、集合框架体系

```
Collection (接口)
├── List (接口，有序可重复)
│   ├── ArrayList
│   ├── LinkedList
│   └── Vector
├── Set (接口，无序不可重复)
│   ├── HashSet
│   ├── LinkedHashSet
│   └── TreeSet
└── Queue (接口，队列)
    ├── LinkedList
    └── PriorityQueue

Map (接口，键值对)
├── HashMap
├── LinkedHashMap
├── TreeMap
└── Hashtable
```

---

## 二、List 集合

### ArrayList

```java
import java.util.*;

public class ArrayListDemo {
    public static void main(String[] args) {
        // 创建 ArrayList
        List<String> list = new ArrayList<>();

        // 添加元素
        list.add("Java");
        list.add("Python");
        list.add("Go");
        list.add(1, "C++");  // 在指定位置插入

        // 访问元素
        System.out.println(list.get(0));  // Java
        System.out.println(list.size());   // 4

        // 遍历
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }

        for (String item : list) {
            System.out.println(item);
        }

        list.forEach(System.out::println);

        // 删除元素
        list.remove("Go");
        list.remove(0);

        // 其他操作
        list.set(0, "Rust");  // 修改
        list.contains("Java"); // true
        list.isEmpty();       // false
        list.clear();         // 清空
    }
}
```

### LinkedList（双向链表）

```java
public class LinkedListDemo {
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>();

        // 添加
        list.add(1);
        list.addFirst(0);    // 头部添加
        list.addLast(2);     // 尾部添加

        // 获取
        list.getFirst();
        list.getLast();

        // 删除
        list.removeFirst();
        list.removeLast();

        // 队列操作
        list.offer(3);       // 添加到尾部
        list.poll();         // 移除并返回头部
        list.peek();         // 返回头部但不删除
    }
}
```

### ArrayList vs LinkedList

| 特性 | ArrayList | LinkedList |
|------|-----------|------------|
| 底层结构 | 动态数组 | 双向链表 |
| 随机访问 | O(1) | O(n) |
| 头尾插入 | O(n) | O(1) |
| 中间插入 | O(n) | O(n) |
| 空间占用 | 较小 | 较大（额外存储指针） |

---

## 三、Set 集合

### HashSet

```java
public class HashSetDemo {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();

        set.add("Apple");
        set.add("Banana");
        set.add("Apple");  // 重复元素不会被添加

        System.out.println(set.size());  // 2
        System.out.println(set.contains("Apple"));  // true

        // 遍历（顺序不保证）
        for (String item : set) {
            System.out.println(item);
        }
    }
}
```

### HashSet 去重原理

```java
public class Person {
    private String name;
    private int age;

    // 正确重写 equals 和 hashCode 才能去重
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}

// 使用
Set<Person> people = new HashSet<>();
people.add(new Person("张三", 20));
people.add(new Person("张三", 20));  // 重复，不会被添加
```

### LinkedHashSet（有序去重）

```java
Set<Integer> set = new LinkedHashSet<>();
set.add(3);
set.add(1);
set.add(2);

// 遍历顺序 = 插入顺序
for (Integer num : set) {
    System.out.println(num);  // 3, 1, 2
}
```

### TreeSet（排序）

```java
// 自然排序
Set<Integer> set = new TreeSet<>();
set.add(3);
set.add(1);
set.add(2);

// 自动排序
for (Integer num : set) {
    System.out.println(num);  // 1, 2, 3
}

// 自定义排序
Set<Person> people = new TreeSet<>((a, b) -> a.getAge() - b.getAge());
people.add(new Person("张三", 25));
people.add(new Person("李四", 20));
```

---

## 四、Map 集合

### HashMap

```java
public class HashMapDemo {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();

        // 添加
        map.put("Apple", 10);
        map.put("Banana", 20);
        map.put("Orange", 15);

        // 获取
        map.get("Apple");           // 10
        map.getOrDefault("Pear", 0); // 0（不存在返回默认值）

        // 判断
        map.containsKey("Apple");   // true
        map.containsValue(10);       // true

        // 遍历
        // 方式1：entrySet
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " = " + entry.getValue());
        }

        // 方式2：keySet
        for (String key : map.keySet()) {
            System.out.println(key + " = " + map.get(key));
        }

        // 方式3：forEach
        map.forEach((k, v) -> System.out.println(k + " = " + v));

        // 删除
        map.remove("Apple");
        map.clear();
    }
}
```

### LinkedHashMap（有序 Map）

```java
// 保持插入顺序
Map<String, Integer> map = new LinkedHashMap<>();
map.put("A", 1);
map.put("C", 3);
map.put("B", 2);

// 遍历顺序 = 插入顺序：A, C, B
```

### TreeMap（排序 Map）

```java
// 按键排序
Map<String, Integer> map = new TreeMap<>();
map.put("C", 3);
map.put("A", 1);
map.put("B", 2);

// 遍历顺序：A, B, C（按字母排序）
```

### HashMap 源码分析

#### 数据结构

```java
// JDK 1.8+：数组 + 链表 + 红黑树
static class Node<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
}

// 转换阈值
static final int TREEIFY_THRESHOLD = 8;    // 链表转红黑树
static final int UNTREEIFY_THRESHOLD = 6;  // 红黑树转链表
```

#### put 过程

```java
1. 计算哈希值：hash = (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16)
2. 计算索引：index = hash & (n - 1)
3. 如果位置为空，直接插入
4. 如果位置有元素，遍历链表/红黑树
5. 如果 key 已存在，覆盖 value
6. 如果链表长度 > 8，转为红黑树
7. size > threshold 时，扩容为 2 倍
```

---

## 五、Collections 工具类

```java
import java.util.*;

public class CollectionsDemo {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(3);
        list.add(1);
        list.add(4);
        list.add(1);
        list.add(5);

        // 排序
        Collections.sort(list);  // [1, 1, 3, 4, 5]

        // 二分查找（需要先排序）
        Collections.binarySearch(list, 3);  // 2

        // 反转
        Collections.reverse(list);

        // 打乱
        Collections.shuffle(list);

        // 交换
        Collections.swap(list, 0, 1);

        // 最大/最小值
        Collections.max(list);
        Collections.min(list);

        // 填充
        Collections.fill(list, 0);

        // 不可修改集合
        List<Integer> unmodifiable = Collections.unmodifiableList(list);

        // 空集合
        List<String> empty = Collections.emptyList();

        // 单元素集合
        Set<String> singleton = Collections.singleton("Java");
    }
}
```

---

## 六、集合与数组互转

```java
// 数组 -> 集合
String[] arr = {"A", "B", "C"};
List<String> list = Arrays.asList(arr);          // 返回固定大小列表
List<String> list2 = new ArrayList<>(Arrays.asList(arr));  // 可修改列表

// 集合 -> 数组
String[] arr2 = list.toArray(new String[0]);
```

---

## 七、遍历方式总结

```java
List<String> list = Arrays.asList("A", "B", "C");

// 1. 普通 for 循环（有索引）
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}

// 2. 增强 for 循环
for (String item : list) {
    System.out.println(item);
}

// 3. Iterator
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}

// 4. ListIterator（可双向遍历）
ListIterator<String> lit = list.listIterator();
while (lit.hasNext()) {
    System.out.println(lit.next());
}

// 5. forEach + Lambda
list.forEach(System.out::println);
list.forEach(item -> System.out.println("Item: " + item));

// 6. Stream
list.stream().forEach(System.out::println);
```

---

## 八、常见问题

### 1. Array vs ArrayList

```java
// 数组
int[] arr = new int[5];      // 固定大小
arr[0] = 1;

// ArrayList
List<Integer> list = new ArrayList<>();  // 动态大小
list.add(1);
```

### 2. ArrayList 源码扩容机制

```java
// 默认容量
private static final int DEFAULT_CAPACITY = 10;

// 扩容公式
int newCapacity = oldCapacity + (oldCapacity >> 1);  // 1.5 倍

// 扩容时机
if (minCapacity > elementData.length)
    grow(minCapacity);
```

### 3. ConcurrentHashMap

```java
// 线程安全的 Map
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

// JDK 1.7：分段锁（Segment）
// JDK 1.8：CAS + synchronized（锁粒度更细）
```

---

## 九、Stream API（Java 8+）

### 创建 Stream

```java
import java.util.stream.*;

// 从集合创建
List<String> list = Arrays.asList("Java", "Python", "Go");
Stream<String> stream1 = list.stream();           // 顺序流
Stream<String> stream2 = list.parallelStream();    // 并行流

// 从数组创建
int[] arr = {1, 2, 3, 4, 5};
IntStream stream3 = Arrays.stream(arr);

// 直接创建
Stream<String> stream4 = Stream.of("A", "B", "C");

// 无限流
Stream<Integer> stream5 = Stream.iterate(0, n -> n + 2);  // 0, 2, 4, 6...
Stream<Double> stream6 = Stream.generate(Math::random);
```

### 中间操作

```java
List<String> names = Arrays.asList("张三", "李四", "王五", "张伟", "李明", "张三");

// filter 过滤
names.stream()
    .filter(name -> name.startsWith("张"))
    .forEach(System.out::println);  // 张三, 张伟, 张三

// map 映射转换
names.stream()
    .map(String::length)
    .forEach(System.out::println);  // 2, 2, 2, 2, 2, 2

// distinct 去重
names.stream()
    .distinct()
    .forEach(System.out::println);  // 张三, 李四, 王五, 张伟, 李明

// sorted 排序
names.stream()
    .sorted()
    .forEach(System.out::println);

// limit / skip
names.stream()
    .skip(2)     // 跳过前2个
    .limit(3)    // 取3个
    .forEach(System.out::println);

// flatMap 扁平化
List<List<Integer>> nested = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4),
    Arrays.asList(5, 6)
);
nested.stream()
    .flatMap(Collection::stream)
    .forEach(System.out::println);  // 1, 2, 3, 4, 5, 6
```

### 终端操作

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// collect 收集
List<Integer> evenList = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());  // [2, 4, 6, 8, 10]

Set<Integer> evenSet = numbers.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toSet());

// joining 拼接字符串
String joined = names.stream()
    .collect(Collectors.joining(", "));  // 张三, 李四, 王五...

// reduce 归约
int sum = numbers.stream()
    .reduce(0, Integer::sum);  // 55

Optional<Integer> max = numbers.stream()
    .reduce(Integer::max);

// count / min / max
long count = numbers.stream().filter(n -> n > 5).count();  // 5

// anyMatch / allMatch / noneMatch
boolean hasEven = numbers.stream().anyMatch(n -> n % 2 == 0);  // true
boolean allPositive = numbers.stream().allMatch(n -> n > 0);    // true

// findFirst / findAny
Optional<Integer> first = numbers.stream()
    .filter(n -> n > 5)
    .findFirst();  // Optional[6]

// toMap
List<String> langs = Arrays.asList("Java", "Python", "Go");
Map<String, Integer> lengthMap = langs.stream()
    .collect(Collectors.toMap(s -> s, String::length));
// {Java=4, Python=6, Go=2}
```

### 分组与分区

```java
List<Person> people = Arrays.asList(
    new Person("张三", 20),
    new Person("李四", 30),
    new Person("王五", 20),
    new Person("赵六", 30)
);

// 按年龄分组
Map<Integer, List<Person>> byAge = people.stream()
    .collect(Collectors.groupingBy(Person::getAge));
// {20=[张三, 王五], 30=[李四, 赵六]}

// 分组并计数
Map<Integer, Long> countByAge = people.stream()
    .collect(Collectors.groupingBy(Person::getAge, Collectors.counting()));
// {20=2, 30=2}

// 分区（按条件分成两组）
Map<Boolean, List<Person>> partition = people.stream()
    .collect(Collectors.partitioningBy(p -> p.getAge() >= 25));
// {false=[张三, 王五], true=[李四, 赵六]}
```

### 数值流

```java
// 避免自动装箱
IntStream intStream = IntStream.rangeClosed(1, 100);

int sum = intStream.sum();                    // 5050
OptionalInt max = IntStream.of(3, 1, 4).max();
OptionalDouble avg = IntStream.of(3, 1, 4).average();

// 对象流转数值流
double totalAge = people.stream()
    .mapToInt(Person::getAge)
    .sum();
```

### 实际应用

```java
// 统计单词频率
String text = "hello world hello java world hello";
Map<String, Long> wordCount = Arrays.stream(text.split(" "))
    .collect(Collectors.groupingBy(w -> w, Collectors.counting()));
// {hello=3, world=2, java=1}

// 找出最贵的3个商品名称
List<String> topProducts = products.stream()
    .sorted(Comparator.comparingDouble(Product::getPrice).reversed())
    .limit(3)
    .map(Product::getName)
    .collect(Collectors.toList());

// 按部门统计平均工资
Map<String, Double> avgSalary = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.averagingDouble(Employee::getSalary)
    ));
```

---

## 练习题

1. 统计字符串中每个字符出现的次数
2. List 去重（多种方式实现）
3. 实现 HashMap 的 put 方法
4. 用集合实现简单的购物车功能

---

*持续更新中...*
