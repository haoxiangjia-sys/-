---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[exception]]"
  - "[[collection]]"
---

# 多线程

**多线程 = 让程序同时干多件事**

## 进程 vs 线程

| 概念 | 说明 |
| ---- | ---- |
| **进程 (Process)** | 正在运行的程序，有独立的内存空间 |
| **线程 (Thread)** | 进程内的执行单元，共享进程的内存（堆和方法区） |

> 一个进程至少有一个线程（main 主线程），可以有多个线程并行执行

## 并发 vs 并行

| 概念 | 英文 | 说明 | 比喻 |
| ---- | ---- | ---- | ---- |
| **并发** | Concurrency | 同一时间段内交替执行（单核也能做） | 一个人同时烧水+扫地，来回切换 |
| **并行** | Parallelism | 同一时刻真正同时执行（必须多核） | 两个人，一个烧水一个扫地 |

```java
// 并发：单核 CPU 上，两个线程快速交替
// 看起来是"同时"跑，实际是 CPU 时间片轮转

// 并行：多核 CPU 上，两个线程各跑在一个核心上
// 真正的同一时刻都在执行
```

> **关键区别**：并发是**逻辑上的同时**（交替做），并行是**物理上的同时**（真的同时做）  
> 单核 CPU 只能并发，多核 CPU 才能并行

## 创建线程的四种方式

| 方式 | 返回值 | 抛异常 | 适用场景 |
| ---- | ----- | ------ | -------- |
| 继承 `Thread` | ❌ 无 | ❌ 不能抛 | 简单任务，但受单继承限制 |
| 实现 `Runnable` | ❌ 无 | ❌ 不能抛 | 常用，可继续继承其他类 |
| 实现 `Callable` | ✅ 有 | ✅ 可以抛 | 需要返回结果 |
| **线程池** | ✅ 推荐 | — | 生产环境首选，资源可控 |

### ① 继承 Thread

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("线程执行了");
    }
}

// 启动线程
MyThread t = new MyThread();
t.start();    // start() 会开新线程去跑 run()
// t.run() 是普通方法调用，不会开新线程！
```

### ② 实现 Runnable（推荐）

```java
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("线程执行了");
    }
}

Thread t = new Thread(new MyRunnable());
t.start();

// Lambda 简化
Thread t = new Thread(() -> System.out.println("线程执行了"));
t.start();
```

### ③ 实现 Callable（有返回值）

```java
class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        return "线程执行完毕";
    }
}

FutureTask<String> task = new FutureTask<>(new MyCallable());
Thread t = new Thread(task);
t.start();

String result = task.get();   // 获取返回值（会阻塞等待线程结束）
```

### ④ 线程池（生产推荐）

```java
// 创建线程池（3 个固定线程）
ExecutorService pool = Executors.newFixedThreadPool(3);

pool.submit(() -> {
    System.out.println("线程池执行任务");
});

pool.shutdown();   // 关闭线程池
```

> 其他常见线程池：`newCachedThreadPool()`（动态扩容）、`newSingleThreadExecutor()`（单线程）

## 线程生命周期（六种状态）

```
NEW ──→ RUNNABLE ──→ TERMINATED
           │
           ↓
    BLOCKED / WAITING / TIMED_WAITING
```

| 状态 | 说明 |
| ---- | ---- |
| **NEW** | 创建了 Thread 对象，还没调用 `start()` |
| **RUNNABLE** | 调用了 `start()`，正在运行或等待 CPU 时间片 |
| **BLOCKED** | 等待锁（synchronized 没抢到锁） |
| **WAITING** | 调用了 `wait()` 或 `join()`，等待被唤醒 |
| **TIMED_WAITING** | 带超时的等待，如 `sleep(1000)`、`wait(1000)` |
| **TERMINATED** | 线程执行完毕 |

## 线程安全

**线程不安全的原因**：多个线程同时操作共享数据，导致数据不一致

```java
// ❌ 线程不安全：两个线程同时 count++ 会丢数据
int count = 0;
count++;  // 实际拆成三步：读 → 加 → 写，可能被插队
```

### synchronized 同步锁

```java
// 方式一：同步代码块（锁对象）
synchronized (lockObj) {
    // 一次只允许一个线程进来
    count++;
}

// 方式二：同步方法（锁的是 this）
public synchronized void increment() {
    count++;
}

// 方式三：静态同步方法（锁的是 类.class）
public static synchronized void staticIncrement() {
    count++;
}
```

### Lock 显式锁

```java
Lock lock = new ReentrantLock();

lock.lock();     // 加锁
try {
    count++;
} finally {
    lock.unlock(); // 一定要释放锁！
}
```

> **synchronized vs Lock**：`synchronized` 自动加解锁（简单）；`Lock` 更灵活（可超时、可中断、可尝试获取）

### volatile 关键字

```java
// volatile 保证可见性：一个线程改了，其他线程立马看到
private volatile boolean flag = false;
```

> `volatile` 不能保证原子性（`count++` 仍然不安全），只是让变量在多个线程间**可见**

## 线程通信：wait / notify

```java
synchronized (lock) {
    while (条件不满足) {
        lock.wait();      // 释放锁，进入等待
    }
    // 条件满足，继续执行
    lock.notify();        // 唤醒一个等待线程
    lock.notifyAll();     // 唤醒所有等待线程
}
```

> `wait()` / `notify()` 必须在 `synchronized` 代码块内使用！

## 常用方法

| 方法 | 说明 |
| ---- | ---- |
| `start()` | 启动线程（开新线程执行 run） |
| `run()` | 线程要执行的任务（直接调不会开新线程） |
| `sleep(ms)` | 当前线程休眠，不释放锁 |
| `join()` | 等待这个线程执行完 |
| `yield()` | 让出 CPU 时间片（不一定生效） |
| `setDaemon(true)` | 设为守护线程（主线程结束它自动结束） |
| `interrupt()` | 中断线程（设置中断标志位） |

## 线程池参数（面试重点）

```java
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    2,                    // corePoolSize：核心线程数
    5,                    // maximumPoolSize：最大线程数
    60L,                  // keepAliveTime：空闲线程存活时间
    TimeUnit.SECONDS,     // 时间单位
    new ArrayBlockingQueue<>(10),  // workQueue：任务队列
    new ThreadPoolExecutor.AbortPolicy()  // 拒绝策略
);
```

**线程池流程**：
1. 任务来了 → 核心线程没满 → 创建核心线程执行
2. 核心线程满了 → 放入任务队列等待
3. 队列满了 → 创建临时线程（直到最大线程数）
4. 都满了 → 执行拒绝策略

**四种拒绝策略**：

| 策略 | 行为 |
| ---- | ---- |
| `AbortPolicy`（默认） | 抛异常 `RejectedExecutionException` |
| `CallerRunsPolicy` | 谁提交的谁自己跑 |
| `DiscardPolicy` | 悄悄丢掉，不报错 |
| `DiscardOldestPolicy` | 丢掉最老的任务，再尝试提交 |

---

**相关笔记**: [[../Java-basics/java-basics]] · [[exception]] · [[collection]]
