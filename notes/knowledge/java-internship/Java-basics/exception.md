---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[io]]"
  - "[[oop]]"
---

# 异常体系

## 异常体系结构

```
Throwable（所有异常和错误的根类）
├── Error（严重错误，程序处理不了）
│   ├── OutOfMemoryError      内存溢出
│   ├── StackOverflowError    栈溢出
│   └── NoClassDefFoundError  类找不到
│
└── Exception（程序能处理的异常）
    ├── RuntimeException（运行时异常，不检查）
    │   ├── NullPointerException         空指针
    │   ├── ArrayIndexOutOfBoundsException 数组越界
    │   ├── ClassCastException           类型转换
    │   ├── ArithmeticException          算术异常（除0）
    │   └── IllegalArgumentException     非法参数
    │
    └── Checked Exception（编译时异常，必须处理）
        ├── IOException                  IO 异常
        ├── SQLException                 数据库异常
        ├── ClassNotFoundException       类找不到
        ├── InterruptedException         线程中断
        └── FileNotFoundException        文件找不到
```

## 两种异常的区别

| | RuntimeException | Checked Exception |
| -- | --------------- | ----------------- |
| **检查时机** | 运行时才报错 | 编译时就检查 |
| **必须处理吗** | 可以不处理 | 必须 try-catch 或 throws |
| **常见例子** | 空指针、越界、除0 | IOException、SQLException |
| **原因** | 代码逻辑bug | 外部环境问题 |

## 异常处理方式

### 方式一：try-catch（自己处理）

```java
try {
    int result = 10 / 0;           // 可能出异常的代码
} catch (ArithmeticException e) {
    System.out.println("除0了");
    e.printStackTrace();           // 打印异常信息（最常用）
} finally {
    System.out.println("不管有没有异常都执行");
}
```

> `finally` — 不管有没有异常都会执行，通常用来关资源（数据库连接、文件流）

### 方式二：throws（抛出去让别人处理）

```java
public void readFile() throws IOException {
    FileReader fr = new FileReader("test.txt");
    // 可能抛出 IOException，自己不处理，抛给调用方
}
```

### 方式三：try-with-resources（自动关资源）

```java
// 实现了 AutoCloseable 的资源可以自动关闭
try (FileReader fr = new FileReader("test.txt")) {
    // 用完后自动 close，不用写 finally
} catch (IOException e) {
    e.printStackTrace();
}
```

## 自定义异常

```java
// 继承 RuntimeException（运行时异常）或 Exception（编译时异常）
public class AgeIllegalException extends RuntimeException {
    public AgeIllegalException(String message) {
        super(message);
    }
}

// 使用
public void setAge(int age) {
    if (age < 0 || age > 150) {
        throw new AgeIllegalException("年龄不合法：" + age);
    }
}
```

## 异常关键字总结

| 关键字 | 说明 |
| ------ | ---- |
| `try` | 监视可能出异常的代码 |
| `catch` | 捕获并处理异常 |
| `finally` | 不管怎样都会执行 |
| `throws` | 声明方法可能抛出的异常 |
| `throw` | 手动抛出一个异常 |

---

**相关笔记**: [[../Java-basics/java-basics]] · [[io]] · [[oop]]
