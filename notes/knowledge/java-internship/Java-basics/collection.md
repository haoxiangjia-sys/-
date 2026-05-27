---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[oop]]"
  - "[[stream]]"
---

# 集合与泛型

集合：可变的容器，用来存储多个对象，比数组更灵活

## 集合框架图

```
Collection（单列集合）
├── List（有序、可重复）
│   ├── ArrayList    ← 数组结构，查询快，增删慢
│   └── LinkedList   ← 链表结构，增删快，查询慢
├── Set（无序、不可重复）
│   ├── HashSet      ← 哈希表，存取最快
│   └── TreeSet      ← 红黑树，自动排序
│
Map（双列集合，键值对）
├── HashMap          ← 哈希表，最常用
├── LinkedHashMap    ← 哈希表 + 链表，有序
└── TreeMap          ← 红黑树，按键排序
```

## List

**List 特有方法（有索引，操作位置）**

| 方法 | 说明 |
| ---- | ---- |
| `add(index, element)` | 在指定位置插入 |
| `get(index)` | 获取指定位置的元素 |
| `set(index, element)` | 修改指定位置的元素 |
| `remove(index)` | 删除指定位置的元素 |
| `indexOf(element)` | 查找元素第一次出现的位置 |
| `lastIndexOf(element)` | 查找元素最后一次出现的位置 |
| `subList(from, to)` | 截取子列表（含头不含尾） |
| `sort(Comparator)` | 排序 |

```java
List<String> list = new ArrayList<>();
list.add("Java");                // 尾部添加
list.add(0, "Python");           // 在索引 0 插入
list.get(0);                     // 获取索引 0 的元素
list.set(0, "C++");              // 修改索引 0 的元素
list.remove(0);                  // 删除索引 0 的元素
list.indexOf("Java");            // 查找位置
list.sort(null);                 // 默认升序排序
```

### ArrayList

```java
ArrayList<String> list = new ArrayList<>();
list.add("Java");
list.get(0);
list.set(0, "Python");
list.remove(0);
list.size();
```

> **特点**：底层数组，查询快（`get/set` O(1)），中间增删慢

### LinkedList

```java
LinkedList<String> list = new LinkedList<>();
list.addFirst("A");        // 头部添加
list.addLast("B");         // 尾部添加
list.removeFirst();        // 移除头部
list.getFirst();           // 获取头部
list.getLast();            // 获取尾部
```

> **特点**：底层链表，头尾操作快（`addFirst/addLast` O(1)），中间查询慢

## Set

### HashSet

```java
HashSet<String> set = new HashSet<>();
set.add("Java");
set.add("Python");
set.add("Java");           // 重复的不会被添加
// 输出：无顺序，不重复
```

### TreeSet

```java
TreeSet<Integer> set = new TreeSet<>();
set.add(5);
set.add(1);
set.add(3);
// 输出：1, 3, 5（自动排序）
```

## Map

### HashMap

```java
HashMap<String, Integer> map = new HashMap<>();
map.put("Java", 1);        // 添加键值对
map.get("Java");           // 根据键取值 → 1
map.remove("Java");        // 根据键删除
map.containsKey("Java");   // 判断键是否存在
map.keySet();              // 获取所有键
```

> **无序**：不保证顺序

### LinkedHashMap

**有序的 HashMap，底层多了一个链表维护顺序**

```java
LinkedHashMap<String, Integer> map = new LinkedHashMap<>();
map.put("a", 1);
map.put("b", 2);
map.put("c", 3);
System.out.println(map);   // {a=1, b=2, c=3} 有序！
```

| | HashMap | LinkedHashMap |
| -- | ------- | ------------- |
| **底层** | 哈希表 | 哈希表 + 双向链表 |
| **顺序** | 无序 | 有序（默认插入顺序） |
| **性能** | 快 | 稍慢（维护链表开销） |
| **适用** | 不关心顺序 | 需要保持顺序 |

> **两种顺序模式**：
> 1. **插入顺序**（默认） — 按 put 的顺序
> 2. **访问顺序** — `new LinkedHashMap<>(16, 0.75f, true)`，最近访问的放最后，**可用于 LRU 缓存**

### TreeMap

**按键自动排序（红黑树），遍历出来就是有序的**

```java
TreeMap<String, Integer> map = new TreeMap<>();
map.put("c", 3);
map.put("a", 1);
map.put("b", 2);
System.out.println(map);   // {a=1, b=2, c=3} 按键排序了！
```

| | HashMap | TreeMap |
| ------ | ------- | ---------- |
| **底层** | 哈希表 | 红黑树 |
| **顺序** | 无序 | 按键排序（默认升序） |
| **性能** | O(1) | O(log n) |
| **适用** | 不关心顺序 | 需要排序、范围查找 |

```java
// 自定义排序规则
TreeMap<String, Integer> map2 = new TreeMap<>((a, b) -> b.compareTo(a));
map2.put("a", 1);
map2.put("b", 2);
map2.put("c", 3);
System.out.println(map2);   // {c=3, b=2, a=1} 降序
```

## Iterator（迭代器）

**用来遍历集合，不依赖索引**

```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

// 获取迭代器
Iterator<String> it = list.iterator();

// hasNext() 判断还有没有下一个
// next() 获取下一个元素
while (it.hasNext()) {
    String s = it.next();
    System.out.println(s);
}
```

### 迭代过程中删除

```java
// ❌ 错误：for-each 遍历时直接删会报错 ConcurrentModificationException
for (String s : list) {
    if (s.equals("B")) list.remove(s);   // 报错！
}

// ✅ 正确：用 iterator 的 remove
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String s = it.next();
    if (s.equals("B")) it.remove();      // 安全删除
}
```

## 五种遍历方式

```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");
```

### ① 普通 for（有索引才能用）

```java
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}
```

> 适用：List（有索引），Set/Map 不能用

### ② 增强 for（for-each）

**底层就是 Iterator，代码更简洁**

```java
for (String s : list) {
    System.out.println(s);
}
```

> 适用：所有 Collection，不能修改/删除元素

### ③ Iterator（迭代器）

```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    String s = it.next();
    System.out.println(s);
}
```

> 适用：所有 Collection，可以安全删除元素（`it.remove()`）

### ④ ListIterator（列表迭代器）

**List 特有，可以双向遍历**

```java
ListIterator<String> lit = list.listIterator();
while (lit.hasNext()) {
    System.out.println(lit.next());
}

// 从后往前遍历
while (lit.hasPrevious()) {
    System.out.println(lit.previous());
}
```

> 适用：只有 List 能用，支持前进/后退、添加、修改

### ⑤ Lambda + forEach

```java
list.forEach(s -> System.out.println(s));
// 或
list.forEach(System.out::println);
```

> 适用：所有 Collection + Map，代码最简洁

### Map 的遍历

```java
HashMap<String, Integer> map = new HashMap<>();
map.put("a", 1);
map.put("b", 2);
map.put("c", 3);
```

**① keySet + get（遍历键，再取值）**

```java
for (String key : map.keySet()) {
    System.out.println(key + "=" + map.get(key));
}
```

> 最简单，但每次 `get(key)` 都要查一次，效率略低

**② entrySet（遍历键值对）**

```java
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + "=" + entry.getValue());
}
```

> 一次拿到键和值，性能最好，推荐

**③ Lambda（forEach）**

```java
map.forEach((k, v) -> System.out.println(k + "=" + v));
```

> 代码最简洁，Java 8+ 推荐写法

**④ Iterator + entrySet**

```java
Iterator<Map.Entry<String, Integer>> it = map.entrySet().iterator();
while (it.hasNext()) {
    Map.Entry<String, Integer> entry = it.next();
    System.out.println(entry.getKey() + "=" + entry.getValue());
}
```

> 遍历过程中可以安全删除元素（`it.remove()`）

**⑤ values（只遍历值）**

```java
for (Integer v : map.values()) {
    System.out.println(v);   // 1, 2, 3
}
```

> 只需要值、不需要键的时候用

## Collections 工具类

**`java.util.Collections` — 操作集合的工具类，全是静态方法**

| 方法 | 说明 |
| ---- | ---- |
| `sort(list)` | 排序（升序） |
| `sort(list, Comparator)` | 按自定义规则排序 |
| `reverse(list)` | 反转顺序 |
| `shuffle(list)` | 打乱顺序 |
| `binarySearch(list, key)` | 二分查找 |
| `max(list)` / `min(list)` | 找最大/最小值 |
| `fill(list, obj)` | 用指定对象填充 |
| `copy(dest, src)` | 复制集合 |
| `swap(list, i, j)` | 交换两个位置的元素 |
| `addAll(collection, elements...)` | 批量添加元素 |

```java
List<Integer> list = new ArrayList<>();
list.add(3);
list.add(1);
list.add(2);

Collections.sort(list);                  // [1, 2, 3]
Collections.reverse(list);               // [3, 2, 1]
Collections.shuffle(list);               // 打乱
Collections.addAll(list, 4, 5, 6);       // 批量加
int max = Collections.max(list);         // 最大值
int index = Collections.binarySearch(list, 3);  // 二分查找（必须先排序）
```

### 不可变集合

**创建后不能修改的集合（增/删/改都会报错）**

**方式一：Collections 工具类**

```java
// 空集合
List<String> emptyList = Collections.emptyList();
Set<String> emptySet = Collections.emptySet();
Map<String, String> emptyMap = Collections.emptyMap();

// 单元素集合
List<String> singleList = Collections.singletonList("A");

// 把可变集合变成不可变
List<String> unmodifiableList = Collections.unmodifiableList(list);
// unmodifiableList.add("X");   // ❌ UnsupportedOperationException
```

**方式二：Java 9+ `List.of()` / `Set.of()` / `Map.of()`（推荐）**

```java
// 直接创建不可变集合
List<String> list = List.of("a", "b", "c");
Set<String> set = Set.of("a", "b", "c");
Map<String, Integer> map = Map.of("a", 1, "b", 2);

// list.add("d");   // ❌ 报错！不可变
```

| 方式 | 旧版写法 | Java 9+ 推荐 |
| ---- | -------- | ------------ |
| 空列表 | `Collections.emptyList()` | `List.of()` |
| 单元素 | `Collections.singletonList("a")` | `List.of("a")` |
| 多元素 | `Collections.unmodifiableList(list)` | `List.of("a", "b", "c")` |

> **不可变集合的好处**：安全（不会被意外修改）、省内存、线程安全

> `Collections` 和 `Collection` 的区别：
> - **Collection** — 接口，集合框架的根
> - **Collections** — 工具类，全是静态方法

---

## 数据结构

### 栈（Stack）

**先进后出（FILO）** — 像叠盘子，先放的在最底下，后放的先拿

```
入栈：push → [ 底 | 1 | 2 | 3 ] ← 出栈：pop
```

> Java 对应：`Stack` 类、`Deque`（双端队列）

### 队列（Queue）

**先进先出（FIFO）** — 像排队，先排的先出

```
入队：add → [ 1 | 2 | 3 ] → 出队：poll
```

> Java 对应：`Queue` 接口、`LinkedList` 实现

### 数组（Array）

**连续内存，通过索引直接访问**

```
索引： [0] [1] [2] [3]
值：  [10][20][30][40]
```

| 操作 | 时间复杂度 |
| ---- | ---------- |
| 根据索引查询 | O(1) ✅ |
| 根据值查找 | O(n) |
| 中间插入/删除 | O(n) |
| 尾部添加 | O(1) ✅ |

### 链表（LinkedList）

**节点 + 指针，不连续内存**

```
单向链表：  head → [A|●] → [B|●] → [C|null]
双向链表：  null ⇄ [A|●] ⇄ [B|●] ⇄ [C] ⇄ null
```

| 操作 | 时间复杂度 |
| ---- | ---------- |
| 查询 | O(n) |
| 头尾插入/删除 | O(1) ✅ |
| 中间插入/删除 | O(n) |

### 二叉树

**每个节点最多有两个子节点（左子、右子）**

```
         root
        /    \
     left    right
     /  \    /   \
   LL   LR  RL   RR
```

> 遍历方式：前序（根左右）、中序（左根右）、后序（左右根）

### 二叉查找树（BST）

**二叉树的升级版，左小右大**

```
        10
       /  \
      5    15
     / \   /
    3   7 12
```

| 特性 | 说明 |
| ---- | ---- |
| **左子树** | 所有节点值 < 根节点 |
| **右子树** | 所有节点值 > 根节点 |
| **查找效率** | 理想 O(log n)，但可能退化成链表 O(n) |

### 平衡二叉树（AVL）

**解决 BST 退化成链表的问题，自动保持平衡**

```
        10
       /  \
      5    15     ← 左右高度差 ≤ 1
     / \
    3   7
```

> 任意节点的左右子树高度差不超过 1，通过**旋转**维持平衡

### 红黑树（Red-Black Tree）

**近似平衡的二叉树，HashMap 8+ 和 TreeMap 的底层实现**

| 规则 | 说明 |
| ---- | ---- |
| **1.** | 每个节点是红色或黑色 |
| **2.** | 根节点是黑色 |
| **3.** | 叶子节点（null）是黑色 |
| **4.** | 红色节点的子节点必须是黑色（不能有连续红色） |
| **5.** | 任意节点到叶子节点的路径上黑色节点数相同 |

> **效率**：查找 / 插入 / 删除 都是 O(log n)  
> **应用**：`HashMap`（链表 ≥8 转红黑树）、`TreeMap`、`TreeSet`

---

## 泛型（Generic）

**泛型 = 参数化类型，把类型当参数传进去，编译时检查类型安全**

### 为什么用泛型

```java
// ❌ 不用泛型：可以存任何类型，取出来要强转，容易报错
List list = new ArrayList();
list.add("Hello");
list.add(123);
String s = (String) list.get(0);   // ✅ 但 list.get(1) 转 String 就炸了

// ✅ 用泛型：限制类型，编译时就检查
List<String> list2 = new ArrayList<>();
list2.add("Hello");
// list2.add(123);     // ❌ 编译报错，提前发现问题
String s2 = list2.get(0);          // ✅ 不需要强转
```

### 泛型类

```java
// 定义：<T> 是类型占位符
public class Box<T> {
    private T data;

    public void set(T data) {
        this.data = data;
    }

    public T get() {
        return data;
    }
}

// 使用
Box<String> box1 = new Box<>();      // T = String
box1.set("Hello");

Box<Integer> box2 = new Box<>();      // T = Integer
box2.set(123);
```

### 泛型方法

```java
public <T> void printArray(T[] arr) {
    for (T t : arr) {
        System.out.println(t);
    }
}
```

### 泛型接口

```java
public interface List<T> {
    void add(T element);
    T get(int index);
}

// 实现时指定类型
public class MyList implements List<String> {
    @Override
    public void add(String element) { ... }

    @Override
    public String get(int index) { ... }
}
```

### 泛型通配符

| 通配符 | 说明 | 示例 |
| ------ | ---- | ---- |
| `?` | 任意类型 | `List<?>` |
| `? extends T` | T 或 T 的子类（上限） | `List<? extends Number>` |
| `? super T` | T 或 T 的父类（下限） | `List<? super Integer>` |

```java
// ? extends T：可以传 Number 或其子类（Integer、Double...）
public void printNum(List<? extends Number> list) {
    for (Number n : list) {
        System.out.println(n);
    }
}

// ? super T：可以传 Integer 或其父类（Number、Object...）
public void addInt(List<? super Integer> list) {
    list.add(123);
}
```

> **常见泛型标识**：`T`（Type）、`E`（Element）、`K`（Key）、`V`（Value）

---

**相关笔记**: [[../Java-basics/java-basics]] · [[oop]] · [[stream]] · [[io]]
