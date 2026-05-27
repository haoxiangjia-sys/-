---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[oop]]"
  - "[[exception]]"
---

# StringBuilder & StringJoiner

## StringBuilder

**StringBuilder** — 可变的字符串，用来解决 String 拼接效率低的问题

| 特点 | 说明 |
| ---- | ---- |
| **可变** | 内容可以修改，不会创建新对象 |
| **效率高** | 拼接字符串比 String 快很多 |
| **线程不安全** | 但性能比 StringBuffer 好 |

### 常用方法

| 方法 | 说明 |
| ---- | ---- |
| `append(xxx)` | 追加内容 |
| `insert(index, xxx)` | 在指定位置插入 |
| `delete(start, end)` | 删除指定范围 |
| `reverse()` | 反转字符串 |
| `toString()` | 转成 String |

```java
StringBuilder sb = new StringBuilder();

sb.append("Hello");
sb.append(" World");
System.out.println(sb);          // Hello World

sb.insert(5, ",");
System.out.println(sb);          // Hello, World

sb.reverse();
System.out.println(sb);          // dlroW ,olleH

String result = sb.toString();   // 转成 String
```

> String 拼接每次都会创建新对象，大量拼接时用 StringBuilder

## StringJoiner

用指定分隔符拼接字符串，可以加前缀和后缀

| 方法 | 说明 |
| ---- | ---- |
| `add(xxx)` | 添加元素 |
| `toString()` | 转成拼接后的字符串 |

```java
// 普通拼接，逗号分隔
StringJoiner sj = new StringJoiner(", ");
sj.add("Java");
sj.add("Python");
sj.add("C++");
System.out.println(sj);            // Java, Python, C++

// 带前缀和后缀
StringJoiner sj2 = new StringJoiner(", ", "[", "]");
sj2.add("A");
sj2.add("B");
sj2.add("C");
System.out.println(sj2);           // [A, B, C]
```

> 相比于 StringBuilder 手动处理分隔符，StringJoiner 更简单

---

**相关笔记**: [[../Java-basics/java-basics]] · [[oop]] · [[exception]]
