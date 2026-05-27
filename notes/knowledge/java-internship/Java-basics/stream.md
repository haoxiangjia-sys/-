---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[collection]]"
---

# Stream 流与方法引用

## Stream 流

**Stream = 对集合进行函数式操作，链式调用，一行搞定过滤/转换/收集**

### 创建 Stream

```java
// 从集合创建
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream1 = list.stream();

// 从数组创建
Stream<String> stream2 = Arrays.stream(new String[]{"a", "b", "c"});

// 直接创建
Stream<String> stream3 = Stream.of("a", "b", "c");
```

### 中间操作（返回 Stream，可以链式调用）

| 方法 | 说明 | 示例 |
| ---- | ---- | ---- |
| `filter` | 过滤 | `filter(s -> s.startsWith("a"))` |
| `map` | 转换 | `map(s -> s.toUpperCase())` |
| `distinct` | 去重 | `distinct()` |
| `sorted` | 排序 | `sorted()` |
| `limit` | 截取前 n 个 | `limit(3)` |
| `skip` | 跳过前 n 个 | `skip(2)` |

### 终结操作（触发执行，一个 Stream 只能用一次）

| 方法 | 说明 |
| ---- | ---- |
| `collect` | 收集到集合 |
| `forEach` | 遍历 |
| `count` | 计数 |
| `anyMatch / allMatch / noneMatch` | 匹配判断 |
| `findFirst / findAny` | 查找 |
| `reduce` | 归约 |

### 实战示例

```java
List<String> list = Arrays.asList("张三", "李四", "王五", "张三丰", "张飞");

// 需求：找出姓张的，名字3个字的，转大写，排序，取前2个
List<String> result = list.stream()
    .filter(s -> s.startsWith("张"))       // 过滤：姓张
    .filter(s -> s.length() == 3)          // 过滤：3个字
    .map(String::toUpperCase)              // 转大写
    .sorted()                              // 排序
    .limit(2)                              // 取前2个
    .collect(Collectors.toList());         // 收集成 List

System.out.println(result);  // [张三丰, 张飞]
```

### collect 收集器

```java
// 收集到 List
List<String> list = stream.collect(Collectors.toList());

// 收集到 Set
Set<String> set = stream.collect(Collectors.toSet());

// 收集到 Map
Map<Integer, String> map = stream.collect(
    Collectors.toMap(String::length, s -> s)
);

// 分组
Map<Integer, List<String>> group = list.stream()
    .collect(Collectors.groupingBy(String::length));
// {1=[a], 2=[ab, cd], 3=[abc]}
```

> **Stream 特点**：
> - 不修改原数据，产生新流
> - 延迟执行，没有终结操作就不干活
> - 一个 Stream 只能用一次

---

## 方法引用（::）

**方法引用 = Lambda 的简化写法，用 `::` 替代 `s -> ...`**

| 类型 | 语法 | Lambda 写法 | 方法引用 |
| ---- | ---- | ----------- | -------- |
| **静态方法** | `类名::静态方法` | `s -> Integer.parseInt(s)` | `Integer::parseInt` |
| **实例方法** | `对象::实例方法` | `s -> System.out.println(s)` | `System.out::println` |
| **特定类型方法** | `类名::实例方法` | `s -> s.compareTo(s2)` | `String::compareTo` |
| **构造方法** | `类名::new` | `() -> new Student()` | `Student::new` |

### 四种类型详解

#### ① 静态方法引用 `类名::static方法`

**条件**：Lambda 的参数 → 静态方法的参数

```java
// Lambda：参数 s 传给 parseInt
Function<String, Integer> f1 = s -> Integer.parseInt(s);
// 方法引用：参数自动传给静态方法
Function<String, Integer> f2 = Integer::parseInt;
```

#### ② 实例方法引用 `对象::实例方法`

**条件**：Lambda 的参数 → 实例方法的参数

```java
// Lambda：参数 s 传给 println
Consumer<String> c1 = s -> System.out.println(s);
// 方法引用：参数自动传给实例方法
Consumer<String> c2 = System.out::println;
```

#### ③ 特定类型方法引用 `类名::实例方法`

**条件**：Lambda 的第一个参数作为调用者，其余参数传给方法

```java
// Lambda：a 是调用者，b 是参数
Comparator<String> cmp1 = (a, b) -> a.compareTo(b);
// 方法引用：第一个参数当调用者
Comparator<String> cmp2 = String::compareTo;

// 再看一个
Function<String, Integer> g1 = s -> s.length();
Function<String, Integer> g2 = String::length;
```

#### ④ 构造方法引用 `类名::new`

**条件**：Lambda 的参数 → 构造方法的参数

```java
// Lambda：参数 s 传给构造方法
Function<String, Student> f1 = s -> new Student(s);
// 方法引用：参数自动传给构造
Function<String, Student> f2 = Student::new;

// 无参构造
Supplier<Student> s1 = () -> new Student();
Supplier<Student> s2 = Student::new;
```

### 使用规则总结

**能用方法引用的条件**：Lambda 体只有一行，且这一行正好在调用一个现有的方法

| 类型 | Lambda 参数 | 方法接收 | 示例 |
| ---- | ----------- | -------- | ---- |
| **静态方法引用** | 全部参数 | 作为静态方法参数 | `s -> Integer.parseInt(s)` → `Integer::parseInt` |
| **实例方法引用** | 全部参数 | 作为实例方法参数 | `s -> System.out.println(s)` → `System.out::println` |
| **特定类型方法引用** | 第一个参数当调用者，其余当参数 | 调用者.方法(其余参数) | `(a, b) -> a.compareTo(b)` → `String::compareTo` |
| **构造方法引用** | 全部参数 | 作为构造方法参数 | `s -> new Student(s)` → `Student::new` |

> **一句话规则**：如果你的 Lambda 只是把参数转发给另一个方法，就能换成 `::`

---

**相关笔记**: [[../Java-basics/java-basics]] · [[collection]] · [[oop]]
