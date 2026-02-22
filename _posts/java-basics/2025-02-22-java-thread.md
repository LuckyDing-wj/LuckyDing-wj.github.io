---
layout: post
title: "Java 多线程详解"
date: 2025-02-22 23:50:00 +0800
categories: [Java基础]
tags: [Java, 多线程, 并发, 线程池]
---

## 一、线程基础

### 线程创建方式

```java
// 方式1：继承 Thread 类
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }

    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start();  // 启动线程
    }
}

// 方式2：实现 Runnable 接口（推荐）
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }

    public static void main(String[] args) {
        MyRunnable runnable = new MyRunnable();
        Thread thread = new Thread(runnable, "我的线程");
        thread.start();
    }
}

// 方式3：Lambda 表达式（Java 8+）
public static void main(String[] args) {
    Thread thread = new Thread(() -> {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
        }
    }, "Lambda线程");
    thread.start();
}

// 方式4：实现 Callable 接口（可返回结果）
public class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            sum += i;
        }
        return sum;
    }

    public static void main(String[] args) throws Exception {
        MyCallable callable = new MyCallable();
        FutureTask<Integer> task = new FutureTask<>(callable);
        Thread thread = new Thread(task);
        thread.start();

        // 获取返回结果
        Integer result = task.get();  // 阻塞直到线程完成
        System.out.println("结果: " + result);
    }
}
```

---

## 二、线程生命周期

```
新建状态 → 就绪状态 → 运行状态 → 阻塞状态 → 终止状态
   ↑                                      ↓
   └──────────────────────────────────────┘
```

```java
public class ThreadState {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        System.out.println("新建状态: " + thread.getState());  // NEW

        thread.start();
        System.out.println("就绪/运行: " + thread.getState());  // RUNNABLE

        Thread.sleep(500);
        System.out.println("等待状态: " + thread.getState());  // TIMED_WAITING

        thread.join();
        System.out.println("终止状态: " + thread.getState());  // TERMINATED
    }
}
```

---

## 三、synchronized 关键字

### 同步方法

```java
public class SynchronizedDemo {
    private int count = 0;

    // 同步实例方法
    public synchronized void increment() {
        count++;
    }

    // 同步静态方法
    public static synchronized void staticMethod() {
        // ...
    }

    // 等价于
    public void increment2() {
        synchronized (this) {
            count++;
        }
    }
}
```

### 同步代码块

```java
public class BankAccount {
    private double balance;

    public void deposit(double amount) {
        // 同步代码块，指定锁对象
        synchronized (this) {
            double newBalance = balance + amount;
            try {
                Thread.sleep(10);  // 模拟耗时操作
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            balance = newBalance;
        }
    }

    // 静态方法同步
    private static final Object LOCK = new Object();

    public static void staticMethod() {
        synchronized (LOCK) {
            // ...
        }
    }
}
```

### 死锁示例

```java
public class DeadlockDemo {
    private static final Object LOCK_A = new Object();
    private static final Object LOCK_B = new Object();

    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> {
            synchronized (LOCK_A) {
                System.out.println("线程1：获取 A 锁");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                synchronized (LOCK_B) {
                    System.out.println("线程1：获取 B 锁");
                }
            }
        });

        Thread thread2 = new Thread(() -> {
            synchronized (LOCK_B) {
                System.out.println("线程2：获取 B 锁");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                synchronized (LOCK_A) {
                    System.out.println("线程2：获取 A 锁");
                }
            }
        });

        thread1.start();
        thread2.start();
        // 可能发生死锁！
    }
}
```

---

## 四、Lock 接口

### ReentrantLock

```java
import java.util.concurrent.locks.*;

public class LockDemo {
    private final ReentrantLock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        lock.lock();  // 获取锁
        try {
            count++;
        } finally {
            lock.unlock();  // 必须在 finally 中释放锁
        }
    }

    // 尝试获取锁
    public void tryIncrement() {
        if (lock.tryLock()) {
            try {
                count++;
            } finally {
                lock.unlock();
            }
        } else {
            System.out.println("获取锁失败，执行其他操作");
        }
    }

    // 可中断锁
    public void interruptibleIncrement() throws InterruptedException {
        lock.lockInterruptibly();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

### ReadWriteLock（读写锁）

```java
import java.util.concurrent.locks.*;

public class ReadWriteLockDemo {
    private final ReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();
    private String data = "初始数据";

    // 读操作（可以并发）
    public String read() {
        readLock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " 读取数据");
            return data;
        } finally {
            readLock.unlock();
        }
    }

    // 写操作（互斥）
    public void write(String newData) {
        writeLock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " 写入数据");
            data = newData;
        } finally {
            writeLock.unlock();
        }
    }
}
```

---

## 五、线程通信

### wait/notify

```java
public class WaitNotifyDemo {
    private static final Object LOCK = new Object();
    private static boolean flag = false;

    public static void main(String[] args) {
        // 等待线程
        Thread waitThread = new Thread(() -> {
            synchronized (LOCK) {
                while (!flag) {
                    try {
                        System.out.println("等待中...");
                        LOCK.wait();  // 释放锁并等待
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println("被唤醒，继续执行");
            }
        });

        // 通知线程
        Thread notifyThread = new Thread(() -> {
            synchronized (LOCK) {
                flag = true;
                LOCK.notify();  // 唤醒等待线程
                System.out.println("发送通知");
            }
        });

        waitThread.start();
        notifyThread.start();
    }
}
```

### Condition

```java
import java.util.concurrent.locks.*;

public class ConditionDemo {
    private final ReentrantLock lock = new ReentrantLock();
    private final Condition condition = lock.newCondition();
    private boolean flag = false;

    public void await() throws InterruptedException {
        lock.lock();
        try {
            while (!flag) {
                System.out.println("等待中...");
                condition.await();
            }
            System.out.println("被唤醒");
        } finally {
            lock.unlock();
        }
    }

    public void signal() {
        lock.lock();
        try {
            flag = true;
            condition.signal();
            System.out.println("发送信号");
        } finally {
            lock.unlock();
        }
    }
}
```

---

## 六、线程池

### ThreadPoolExecutor

```java
import java.util.concurrent.*;

public class ThreadPoolDemo {
    public static void main(String[] args) {
        // 核心线程数
        int corePoolSize = 5;
        // 最大线程数
        int maximumPoolSize = 10;
        // 空闲线程存活时间
        long keepAliveTime = 60;
        // 时间单位
        TimeUnit unit = TimeUnit.SECONDS;
        // 工作队列
        BlockingQueue<Runnable> workQueue = new LinkedBlockingQueue<>(100);
        // 线程工厂
        ThreadFactory threadFactory = Executors.defaultThreadFactory();
        // 拒绝策略
        RejectedExecutionHandler handler = new ThreadPoolExecutor.AbortPolicy();

        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            corePoolSize,
            maximumPoolSize,
            keepAliveTime,
            unit,
            workQueue,
            threadFactory,
            handler
        );

        // 提交任务
        for (int i = 0; i < 15; i++) {
            final int taskId = i;
            executor.execute(() -> {
                System.out.println(Thread.currentThread().getName() + " 执行任务 " + taskId);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }

        // 关闭线程池
        executor.shutdown();
        try {
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
        }
    }
}
```

### Executors 工具类

```java
public class ExecutorsDemo {
    public static void main(String[] args) {
        // 固定大小线程池
        ExecutorService fixedPool = Executors.newFixedThreadPool(5);

        // 单线程池
        ExecutorService singlePool = Executors.newSingleThreadExecutor();

        // 缓存线程池（按需创建）
        ExecutorService cachedPool = Executors.newCachedThreadPool();

        // 定时任务线程池
        ScheduledExecutorService scheduledPool = Executors.newScheduledThreadPool(3);

        // 定时执行
        scheduledPool.schedule(() -> {
            System.out.println("延迟3秒执行");
        }, 3, TimeUnit.SECONDS);

        // 周期执行
        scheduledPool.scheduleAtFixedRate(() -> {
            System.out.println("每2秒执行一次");
        }, 0, 2, TimeUnit.SECONDS);
    }
}
```

---

## 七、并发工具类

### CountDownLatch（倒计时门栓）

```java
import java.util.concurrent.*;

public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        // 计数器为3
        CountDownLatch latch = new CountDownLatch(3);

        for (int i = 1; i <= 3; i++) {
            final int num = i;
            new Thread(() -> {
                try {
                    System.out.println("工人 " + num + " 开始工作");
                    Thread.sleep((long) (Math.random() * 3000));
                    System.out.println("工人 " + num + " 完成工作");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    latch.countDown();  // 计数减1
                }
            }).start();
        }

        System.out.println("等待所有工人完成...");
        latch.await();  // 等待计数器归零
        System.out.println("所有工作完成！");
    }
}
```

### CyclicBarrier（循环栅栏）

```java
import java.util.concurrent.*;

public class CyclicBarrierDemo {
    public static void main(String[] args) {
        // 3个线程到达栅栏后执行回调
        CyclicBarrier barrier = new CyclicBarrier(3, () -> {
            System.out.println("所有线程已到达，继续前进！");
        });

        for (int i = 1; i <= 3; i++) {
            final int num = i;
            new Thread(() -> {
                try {
                    System.out.println("运动员 " + num + " 到达起点");
                    Thread.sleep((long) (Math.random() * 2000));
                    System.out.println("运动员 " + num + " 到达栅栏");
                    barrier.await();  // 等待其他线程
                    System.out.println("运动员 " + num + " 继续跑步");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

### Semaphore（信号量）

```java
import java.util.concurrent.*;

public class SemaphoreDemo {
    public static void main(String[] args) {
        // 3个许可
        Semaphore semaphore = new Semaphore(3);

        for (int i = 1; i <= 10; i++) {
            final int num = i;
            new Thread(() -> {
                try {
                    semaphore.acquire();  // 获取许可
                    System.out.println("车辆 " + num + " 进入停车位");
                    Thread.sleep(2000);
                    System.out.println("车辆 " + num + " 离开停车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();  // 释放许可
                }
            }).start();
        }
    }
}
```

---

## 八、原子类

```java
import java.util.concurrent.atomic.*;

public class AtomicDemo {
    public static void main(String[] args) throws InterruptedException {
        // 原子整数
        AtomicInteger atomicInt = new AtomicInteger(0);

        // 增加并返回
        int andIncrement = atomicInt.getAndIncrement();
        int incrementAndGet = atomicInt.incrementAndGet();

        // CAS 操作
        boolean success = atomicInt.compareAndSet(5, 10);
        int value = atomicInt.get();

        // 原子引用
        AtomicReference<String> atomicRef = new AtomicReference<>("初始值");
        atomicRef.set("新值");
        String oldValue = atomicRef.getAndSet("更新值");

        // 原子数组
        AtomicIntegerArray atomicArray = new AtomicIntegerArray(10);
        atomicArray.getAndAdd(0, 5);

        // 多线程安全计数
        ExecutorService executor = Executors.newFixedThreadPool(10);
        CountDownLatch latch = new CountDownLatch(10000);

        for (int i = 0; i < 10000; i++) {
            executor.submit(() -> {
                atomicInt.incrementAndGet();
                latch.countDown();
            });
        }

        latch.await();
        System.out.println("最终值: " + atomicInt.get());  // 10000
        executor.shutdown();
    }
}
```

---

## 九、ThreadLocal

```java
public class ThreadLocalDemo {
    // 每个线程独立的变量
    private static final ThreadLocal<String> threadLocal = new ThreadLocal<>();
    private static final ThreadLocal<Integer> threadLocalInt = ThreadLocal.withInitial(() -> 0);

    public static void main(String[] args) {
        // 主线程设置值
        threadLocal.set("主线程的值");
        System.out.println(Thread.currentThread().getName() + ": " + threadLocal.get());

        // 子线程
        new Thread(() -> {
            // 子线程获取不到主线程设置的值
            System.out.println(Thread.currentThread().getName() + ": " + threadLocal.get());

            threadLocal.set("子线程的值");
            System.out.println(Thread.currentThread().getName() + ": " + threadLocal.get());

            threadLocal.remove();  // 清除，防止内存泄漏
        }).start();

        // 主线程的值不受影响
        System.out.println(Thread.currentThread().getName() + ": " + threadLocal.get());
    }

    // 使用示例：SimpleDateFormat 线程安全
    private static final ThreadLocal<SimpleDateFormat> dateFormat =
        ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd"));

    public static String format(Date date) {
        return dateFormat.get().format(date);
    }
}
```

---

## 十、volatile 关键字

```java
public class VolatileDemo {
    // volatile 保证可见性和有序性，但不保证原子性
    private volatile boolean running = true;
    private volatile int count = 0;

    // 可见性：一个线程修改后，其他线程立即可见
    public void stop() {
        running = false;  // 修改后立即刷新到主内存
    }

    public void run() {
        while (running) {
            // 每次读取都从主内存获取最新值
        }
        System.out.println("线程停止");
    }

    // ❌ volatile 不保证原子性
    // count++ 实际是 读取-修改-写入 三步操作
    public void increment() {
        count++;  // 非原子操作，多线程下不安全
    }

    // ✅ 需要原子性时使用 AtomicInteger
    private AtomicInteger atomicCount = new AtomicInteger(0);
    public void safeIncrement() {
        atomicCount.incrementAndGet();
    }
}
```

### volatile vs synchronized

| 特性 | volatile | synchronized |
|------|----------|-------------|
| 可见性 | ✅ | ✅ |
| 原子性 | ❌ | ✅ |
| 有序性 | ✅ | ✅ |
| 阻塞 | 不会阻塞 | 会阻塞 |
| 适用场景 | 状态标志、双重检查锁 | 复合操作 |

### 双重检查锁单例

```java
public class Singleton {
    // volatile 防止指令重排序
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {                // 第一次检查
            synchronized (Singleton.class) {
                if (instance == null) {        // 第二次检查
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

---

## 十一、CompletableFuture 详解

### 创建异步任务

```java
import java.util.concurrent.*;

public class CompletableFutureDemo {
    public static void main(String[] args) throws Exception {
        // 无返回值
        CompletableFuture<Void> future1 = CompletableFuture.runAsync(() -> {
            System.out.println("异步任务执行中...");
        });

        // 有返回值
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
            return "Hello CompletableFuture";
        });

        System.out.println(future2.get());  // 阻塞获取结果
    }
}
```

### 链式调用

```java
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApply(s -> s + " World")           // 转换结果
    .thenApply(String::toUpperCase)
    .thenAccept(System.out::println)        // 消费结果
    .thenRun(() -> System.out.println("完成"));  // 执行后续操作
```

### 组合多个任务

```java
// 两个任务都完成后合并结果
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");

CompletableFuture<String> combined = future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2);
System.out.println(combined.get());  // Hello World

// 任意一个完成即可
CompletableFuture<Object> any = CompletableFuture.anyOf(future1, future2);

// 所有任务完成
CompletableFuture<Void> all = CompletableFuture.allOf(future1, future2);
all.join();
```

### 异常处理

```java
CompletableFuture.supplyAsync(() -> {
    if (true) throw new RuntimeException("出错了");
    return "OK";
})
.exceptionally(ex -> {
    System.out.println("异常: " + ex.getMessage());
    return "默认值";
})
.thenAccept(System.out::println);  // 默认值

// handle 同时处理正常和异常
CompletableFuture.supplyAsync(() -> "数据")
    .handle((result, ex) -> {
        if (ex != null) {
            return "异常恢复值";
        }
        return result + " 处理完成";
    });
```

### 实际应用：并行查询

```java
public class ParallelQuery {
    public static void main(String[] args) {
        long start = System.currentTimeMillis();

        CompletableFuture<String> userFuture = CompletableFuture.supplyAsync(() -> {
            sleep(1000);
            return "用户信息";
        });

        CompletableFuture<String> orderFuture = CompletableFuture.supplyAsync(() -> {
            sleep(1500);
            return "订单信息";
        });

        CompletableFuture<String> scoreFuture = CompletableFuture.supplyAsync(() -> {
            sleep(800);
            return "积分信息";
        });

        // 等待所有查询完成
        CompletableFuture.allOf(userFuture, orderFuture, scoreFuture).join();

        System.out.println(userFuture.join());
        System.out.println(orderFuture.join());
        System.out.println(scoreFuture.join());

        // 总耗时约 1500ms，而非 3300ms
        System.out.println("耗时: " + (System.currentTimeMillis() - start) + "ms");
    }

    private static void sleep(long ms) {
        try { Thread.sleep(ms); } catch (InterruptedException e) {}
    }
}
```

---

## 十二、最佳实践

```java
// 1. 优先使用线程池而不是直接创建线程
ExecutorService pool = Executors.newFixedThreadPool(10);
pool.execute(task);

// 2. 使用并发集合
List<String> list = new CopyOnWriteArrayList<>();
Map<String, String> map = new ConcurrentHashMap<>();
BlockingQueue<String> queue = new LinkedBlockingQueue<>();

// 3. 使用 CountDownLatch 等待多个线程完成
CountDownLatch latch = new CountDownLatch(threadCount);
// ... 线程任务中 latch.countDown()
latch.await();

// 4. 避免在 synchronized 块中执行耗时操作
synchronized (lock) {
    // 只保护必要的代码
}
// 耗时操作放在外面

// 5. 使用 volatile 保证可见性
private volatile boolean flag = false;

// 6. 使用 CompletableFuture（Java 8+）
CompletableFuture.supplyAsync(() -> "Hello")
    .thenApplyAsync(String::toUpperCase)
    .thenAcceptAsync(System.out::println);
```

---

## 练习题

1. 实现一个生产者-消费者模型
2. 使用线程池实现并发下载
3. 手写一个线程安全的单例模式
4. 实现 3 个线程依次打印 ABCABC...

---

## 相关链接

- [学习资源汇总](/2025/02/22/learning-resources.html)
- [返回学习路线](/#roadmap)

---

*持续更新中...*

