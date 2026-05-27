---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[oop]]"
---

# Java 基础语法

## Java 概述

| 概念 | 全称 | 说明 |
| ---- | ---- | ---- |
| **Java SE** | 标准版 | 桌面开发 |
| **Java ME** | 微型版 | 嵌入式 |
| **Java EE** | 企业版 | Web 网站开发 |

> 应用方向：微服务、鸿蒙、Android、大数据开发

### JVM / JDK / JRE

| 术语 | 说明 |
| ---- | ---- |
| **JVM** (Java Virtual Machine) | 真正运行 Java 的地方 |
| **JDK** (Java Development Kit) | JVM + 库 + 开发工具 |
| **JRE** (Java Runtime Environment) | Java 运行时环境 |

---

## 基础语法

### 关键字 & class

```java
public    // 关键字
class     // 创建 / 定义一个类（class 是最小单位）
```

### 字面量

整数、小数、字符串、字符、布尔、空（null）

![[Pasted image 20260521231842.png]]

### 数据类型

#### 基本数据类型

| 类型 | 关键字 | 大小 | 取值范围 |
| ---- | ------ | ---- | -------- |
| 字节型 | `byte` | 1 字节 | -128 ~ 127 |
| 短整型 | `short` | 2 字节 | -32768 ~ 32767 |
| 整型 | `int` | 4 字节 | -21 亿 ~ 21 亿 |
| 长整型 | `long` | 8 字节 | -922 亿亿 ~ 922 亿亿 |
| 单精度浮点 | `float` | 4 字节 | ±3.4E-38 ~ ±3.4E+38 |
| 双精度浮点 | `double` | 8 字节 | ±1.7E-308 ~ ±1.7E+308 |
| 字符型 | `char` | 2 字节 | 0 ~ 65535 |
| 布尔型 | `boolean` | 未明确定义 | `true` / `false` |

> 整型默认是 `int`，浮点默认是 `double`  
> `long` 赋值要加 L：`long l = 100L;`  
> `float` 赋值要加 F：`float f = 1.5F;`

#### 引用数据类型

| 类型 | 说明 | 示例 |
| ---- | ---- | ---- |
| **类（class）** | 自定义或 JDK 提供的类 | `String`、`Student` |
| **接口（interface）** | 定义行为规范 | `List`、`Map` |
| **数组（array）** | 存储多个同类型数据 | `int[]`、`String[]` |

> 引用数据类型变量存的是**地址值**，指向堆内存中的对象  
> 基本数据类型变量存的是**具体的值**

### 转义符 `\t`

制表符，将长度补到 8

```java
System.out.println("name" + '\t' + "age");   // 输出：name    age
```

### 定义变量

```java
数据类型 变量名 = 数据值；
```

1. 变量中只保留一个值
2. 变量名不允许重复定义
3. 一条语句可以定义多个变量
4. 变量使用前要进行赋值
5. 变量有作用域

### 进制转换

二进制 → 十进制：**系数 × 基数的权次幂 相加**

```
101 = 1 × 2² + 0 × 2¹ + 1 × 2⁰ = 5
```

### 标识符规则

1. 由数字、字母、下划线和 `$` 组成
2. 不能用数字开头
3. 不能是关键字
4. 区分大小写

### 键盘录入

```java
import java.util.Scanner;              // 导包
Scanner sc = new Scanner(System.in);   // 创建对象
int i = sc.nextInt();                  // 接受输入
```

---

## 运算符

### 算术运算符

```java
+   -   *   /   %
```

### 类型转换

| 转换方式 | 说明 | 示例 |
| -------- | ---- | ---- |
| **隐式转换** | 小范围 → 大范围 | `byte → short → int → long → float → double` |
| | byte、short、char 运算时先转为 int | |
| **强制转换** | 大范围 → 小范围 | `byte b = (byte) a;` |

### 自增自减

```java
int x = 10;
int y = x++;   // y = 10，先赋值后自增
int z = ++x;   // z = 12，先自增后赋值
// 最终：x=12, y=10, z=12
```

### 赋值运算符

| 符号 | 等价于 | 示例 |
| ---- | ------ | ---- |
| `=` | — | `int a = 10` |
| `+=` | `a + b` | `a += b` |
| `-=` | `a - b` | `a -= b` |
| `*=` | `a * b` | `a *= b` |
| `/=` | `a / b` | `a /= b` |
| `%=` | `a % b` | `a %= b` |

> **注意**：`a += b` 自带隐式类型转换，比 `a = a + b` 更安全  
> 例：`short s = 1; s += 1;` ✅ 编译通过；`s = s + 1;` ❌ 类型提升报错

### 关系运算符

| 符号 | 示例 | 结果 |
| ---- | ---- | ---- |
| `==` | `2 == 3` | `false` |
| `!=` | `2 != 3` | `true` |
| `>` | `3 > 2` | `true` |
| `>=` | `3 >= 3` | `true` |
| `<` | `2 < 3` | `true` |
| `<=` | `3 <= 2` | `false` |

> 结果都是 `boolean` 类型，只有 `true` 或 `false`

### 逻辑运算符

| 符号 | 作用 | 说明 |
| ---- | ---- | ---- |
| `&` | 逻辑与（且） | 并且，两边都为真，结果才是真 |
| `\|` | 逻辑或 | 或者，两边都为假，结果才是假 |
| `^` | 逻辑异或 | 相同为 false，不同为 true |
| `!` | 逻辑非 | 取反 |

> `&&` 和 `||` 是**短路运算符**：左边能决定结果时，右边就不执行  
> `&` 和 `|` 两边都会执行

### 三元运算符

```java
关系表达式 ? 表达式1 : 表达式2;
// true → 返回表达式1，false → 返回表达式2
```

---

## 流程控制

### switch

```java
switch (表达式) {
    case value1:
        语句体1;
        break;
    case value2:
        语句体2;
        break;
    default:
        语句体3;
        break;
}
```

---

## 循环结构

### for 循环

```java
for (初始化; 条件判断; 条件控制) {
    // 循环体
}
```

### while 循环

```java
while (条件判断) {
    // 循环体
    // 条件控制
}
```

---

## 数组

### 一维数组

```java
数据类型[] 数组名;
```

数组存在**内存**里，通过**索引（index）** 对元素访问

#### 静态初始化

```java
int[] arr = new int[]{11, 22, 33};
int[] arr = {11, 22, 33};   // 简化写法
```

#### 动态初始化

```java
int[] arr = new int[3];     // 指定长度，默认值填充
```

### 二维数组

#### 静态初始化

```java
int[][] arr = {{11, 22}, {33, 44}};
```

#### 动态初始化

```java
int[][] arr = new int[2][3];
```

---

## 方法（Method）

方法：程序的**最小执行单元**，提高代码复用性、可维护性

### 基本方法

```java
public static void 方法名() {
    // 方法体
}

// 调用
方法名();
```

### 带参数的方法

```java
public static void method(int num1, int num2) {   // num1, num2 是形参
    int result = num1 + num2;
    System.out.println(result);
}
```

### 带返回值的方法

需要结果去编写另一段代码时，用返回值

```java
public static int 方法名(参数) {
    // 方法体
    return value;
}
```

### 方法重载（Overload）

同一个类中，定义了**同名**的方法，功能相似，但**参数类型或个数不同**

```java
public static int sum(int a, int b) { return a + b; }
public static double sum(double a, double b) { return a + b; }
public static int sum(int a, int b, int c) { return a + b + c; }
```

---

## Java 内存分配

| 区域 | 说明 |
| ---- | ---- |
| **栈（Stack）** | 方法运行时用，方法进栈运行，运行完毕出栈 |
| **堆（Heap）** | `new` 出来的东西存在这里，在堆内存开了个空间 |
| **方法区（Method Area）** | 存储 class 信息 |

> 引用数据类型：变量储存的是**地址值**，引用其他空间的数据

---

## 包（Package）

**包 = 文件夹，用来管理类，防止类名冲突**

```java
package com.quiz.model;   // 声明包，必须写在文件第一行

import java.util.Scanner; // 导入其他包下的类
import com.quiz.model.Student;
```

| 概念 | 说明 |
| ---- | ---- |
| **package** | 声明类所在的包 |
| **import** | 导入其他包的类，省去写全类名 |
| **全类名** | 包名 + 类名，例如 `java.util.Scanner` |

> 域名倒写：`com.公司名.项目名.模块名` — 包命名规范

---

**相关笔记**: [[../Java-basics/java-basics]] · [[oop]] · [[exception]]
