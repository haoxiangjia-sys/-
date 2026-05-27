---
created: 2026-05-21
tags:
  - java/internship
  - java/basics
related:
  - "[[../Java-basics/java-basics]]"
  - "[[collection]]"
  - "[[exception]]"
---

# 面向对象（OOP）

```java
public class Student {
    // 属性（成员变量）
    String name;
    double height;

    // 方法
    public void study() {}
    public void sleep() {}
}
```

## 封装

**把数据藏起来，只给外界暴露方法去操作它**

- **private** 把属性锁起来
- **public getter/setter** 提供受控的访问通道

## 权限修饰符

| 修饰符 | 同一个类 | 同包 | 子类（不同包） | 任何地方 |
| :----: | :------: | :--: | :------------: | :------: |
| `private` | ✅ | ❌ | ❌ | ❌ |
| `默认（default）` | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

```java
public class Student {
    private   int a;   // 只有本类能访问
              int b;   // 默认：本类 + 同包
    protected int c;   // 本类 + 同包 + 子类
    public    int d;   // 所有人都能访问
}
```

> 权限大小：`private` < `默认` < `protected` < `public`

## 继承

**子类继承父类的属性和方法，减少重复代码**

### 继承的特点

| 特点 | 说明 |
| ---- | ---- |
| **单继承** | 一个子类只能继承一个父类 |
| **多层继承** | 爷 → 父 → 子，可以一直往下传 |
| **所有类继承 Object** | 所有类都隐式继承 `Object`（老祖宗） |
| **子类拥有父类的所有** | 除了 private 和构造方法 |
| **子类可以扩展** | 在父类基础上加自己的方法和属性 |
| **方法重写（Override）** | 子类可以重写父类的方法 |

### 继承体系

```
Object（根类）
 └── Animal
      ├── Dog
      │    └── Husky（多层继承）
      └── Cat
```

```java
// 顶层：父类
public class Animal {
    public void eat() {
        System.out.println("吃东西");
    }
}

// 中间层：继承了 Animal
public class Dog extends Animal {
    public void bark() {
        System.out.println("汪汪");
    }
}

// 底层：继承了 Dog（多层继承）
public class Husky extends Dog {
    public void pullSled() {
        System.out.println("拉雪橇");
    }
}

// 使用
Husky h = new Husky();
h.eat();       // Animal 的
h.bark();      // Dog 的
h.pullSled();  // 自己的
```

### 继承相关关键字

| 关键字 | 说明 |
| ------ | ---- |
| `extends` | 表示继承关系 |
| `super` | 指向父类对象的引用 |
| `@Override` | 重写父类方法（方法名、参数相同） |
| `final` | 类不能被继承，方法不能被重写 |

### super 关键字

**`super` = 父类对象**

| 用法 | 说明 |
| ---- | ---- |
| `super.成员变量` | 访问父类的成员变量 |
| `super.方法名()` | 调用父类的方法 |
| `super()` | 调用父类的构造方法，必须写在第一行 |

```java
public class Dog extends Animal {
    public Dog() {
        super();           // 调用父类构造，必须写在第一行
    }

    @Override
    public void eat() {
        super.eat();       // 调用父类的 eat
        System.out.println("狗在吃骨头");
    }
}
```

## 多态

**同一个行为有多种不同的表现形式**

前提：继承 + 方法重写 + 父类引用指向子类对象

```java
// 父类
public class Animal {
    public void shout() {
        System.out.println("动物叫");
    }
}

// 子类1
public class Dog extends Animal {
    @Override
    public void shout() {
        System.out.println("汪汪");
    }
}

// 子类2
public class Cat extends Animal {
    @Override
    public void shout() {
        System.out.println("喵喵");
    }
}

// 测试多态
public class Test {
    public static void main(String[] args) {
        Animal a1 = new Dog();   // 父类引用指向子类对象
        Animal a2 = new Cat();

        a1.shout();   // 汪汪（Dog 的）
        a2.shout();   // 喵喵（Cat 的）
    }
}
```

### 多态的好处

**解耦**：写代码时面向父类编程，要换子类时不用改调用方的代码

```java
// 没有多态：每加一种动物就要写一个新方法
public void makeSound(Dog d) { d.shout(); }
public void makeSound(Cat c) { c.shout(); }

// 有多态：一个方法搞定所有动物
public void makeSound(Animal a) {   // 参数写父类
    a.shout();                       // 不管传什么子类进来都能调
}
```

### 类型转换

```java
// 向上转型（自动）—— 把子类转成父类
Animal a = new Dog();

// 向下转型（强制）—— 把父类转回子类
Dog d = (Dog) a;

// 用 instanceof 判断类型，避免转型失败
if (a instanceof Dog) {
    Dog d2 = (Dog) a;
}
```

> **多态的核心**：编译看左边（父类），运行看右边（子类）

### 多态的弊端

父类引用**不能调用子类特有的方法或属性**

```java
Animal a = new Dog();
a.shout();       // ✅ 父类有 shout()，调得到
a.bark();        // ❌ 编译报错！bark() 是 Dog 特有的，父类没有
```

解决办法：向下转型

```java
Animal a = new Dog();
if (a instanceof Dog) {
    Dog d = (Dog) a;   // 转回子类
    d.bark();          // ✅ 可以调了
}
```

> **总结**：多态让代码更灵活（解耦），但代价是丢失了子类特有的东西，要用向下转型拿回来

## 抽象类和抽象方法

**抽象类** — 用 `abstract` 修饰的类，不能创建对象，专门给子类继承

**抽象方法** — 只有声明没有方法体，强制子类重写

```java
// 抽象类
public abstract class Animal {
    public void eat() {
        System.out.println("吃东西");
    }

    // 抽象方法：没有方法体，子类必须重写
    public abstract void shout();
}

// 子类必须重写所有抽象方法，否则也得是抽象类
public class Dog extends Animal {
    @Override
    public void shout() {    // 不重写就编译报错
        System.out.println("汪汪");
    }
}
```

| 概念 | 规则 |
| ---- | ---- |
| **抽象类** | 用 `abstract class` 修饰，不能 `new` |
| **抽象方法** | 用 `abstract` 修饰，没有方法体，必须被子类重写 |
| **抽象类可以有构造方法** | 但不能直接 `new`，给子类 `super()` 调用的 |
| **抽象类可以有普通方法** | 也可以有成员变量，和普通类一样 |
| **有抽象方法的类必须是抽象类** | 但抽象类可以没有抽象方法 |

> 抽象类存在的意义：**强制子类按统一规范去实现**

## 接口（interface）

**接口 = 完全抽象的"规范"，只定义方法签名，不写实现**

```java
// 定义接口
public interface Flyable {
    void fly();   // 抽象方法，public abstract 省略了
}

// 实现接口（implements）
public class Bird implements Flyable {
    @Override
    public void fly() {
        System.out.println("鸟在飞");
    }
}

// 使用
Bird b = new Bird();
b.fly();
```

| 接口 vs 抽象类 | 接口 | 抽象类 |
| -------------- | ---- | ------ |
| 关键字 | `interface` | `abstract class` |
| 方法 | 全是抽象方法（Java 8+ 可以有 default/static） | 可以有普通方法 |
| 变量 | 只能 `public static final`（常量） | 可以有成员变量 |
| 构造方法 | 没有构造方法 | 可以有构造方法 |
| 多继承 | 类可以**实现多个接口** | 类只能继承一个抽象类 |

### 接口的特性

```java
// 1. 一个类可以实现多个接口（弥补单继承的不足）
public class Plane implements Flyable, Moveable {
    @Override
    public void fly() { ... }

    @Override
    public void move() { ... }
}

// 2. 接口可以继承接口
public interface SuperFlyable extends Flyable {
    void superFly();
}

// 3. 接口中的变量都是常量
interface Config {
    int MAX_SIZE = 100;   // 相当于 public static final int MAX_SIZE = 100;
}
```

> **接口的意义**：定义规范，让不同的类遵循同样的行为标准  
> 常用接口：`List`、`Map`、`Runnable`、`Comparable`

## final 关键字

**`final` = 不可变的，最终状态**

| 使用位置 | 效果 | 示例 |
| -------- | ---- | ---- |
| **修饰变量** | 变量变成常量，值不能改 | `final int MAX = 100;` |
| **修饰方法** | 方法不能被重写 | `public final void run() {}` |
| **修饰类** | 类不能被继承 | `public final class Math {}` |

```java
public class Parent {
    public final void show() {
        System.out.println("不能重写我");
    }
}

public class Child extends Parent {
    // ❌ 编译报错！final 方法不能重写
    // @Override
    // public void show() {}
}

public final class MyString {
    // final 类不能被继承
}
```

> 常见 final 类：`String`、`Math`、`Integer`（包装类都是 final 的）

## this 关键字

**`this` = 当前对象自己**

| 用法 | 说明 | 示例 |
| ---- | ---- | ---- |
| `this.成员变量` | 区分成员变量和参数 | `this.name = name;` |
| `this()` | 本构造方法中调用另一个构造方法 | `this(name, age);` |

```java
public class Student {
    private String name;

    public void setName(String name) {
        this.name = name;  // this.name 是对象的name，= name 是参数
    }
}
```

## 构造方法

创建对象时自动调用，用来初始化对象

| 特点 | 说明 |
| ---- | ---- |
| 方法名 = 类名 | 必须和类名相同 |
| 无返回值 | 连 `void` 都不用写 |
| 默认赠送 | 不写任何构造时，Java 自动给一个无参构造 |

```java
public class Student {
    private String name;
    private int age;

    // 无参构造
    public Student() {
    }

    // 有参构造
    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

// 使用
Student s1 = new Student();                  // 调无参构造
Student s2 = new Student("张三", 20);        // 调有参构造
```

> 注意：写了有参构造后，无参构造**不会自动生成**，要两个都用就都得手动写上

## JavaBean

一种 Java 类的编写规范（约定），框架依赖这套规范来操作你的类

| 规范 | 说明 |
| ---- | ---- |
| **类用 public 修饰** | 对外可见 |
| **提供无参构造** | 让框架能通过反射创建对象 |
| **属性用 private** | 封装，不让外界直接访问 |
| **提供 getter/setter** | 通过公共方法读写属性 |

```java
public class Student {
    private String name;
    private int age;

    public Student() {}

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

> MyBatis、Spring 等框架都依赖 JavaBean 规范来创建对象和读写属性

## 内部类

**在一个类的内部再定义一个类**

### 成员内部类

```java
public class Outer {
    private int num = 10;

    public class Inner {
        public void show() {
            System.out.println(num);   // 可以直接访问外部类的成员
        }
    }
}

// 使用
Outer o = new Outer();
Outer.Inner i = o.new Inner();
i.show();
```

### 静态内部类

```java
public class Outer {
    public static class Inner {
        public void show() {
            System.out.println("静态内部类");
        }
    }
}

// 使用
Outer.Inner i = new Outer.Inner();
```

### 匿名内部类（最常用）

**简化代码，不用单独写一个类，直接 new 接口或抽象类**

```java
// 不用匿名内部类：得单独写一个实现类
public class Dog implements Animal {
    @Override
    public void shout() { ... }
}

// 用匿名内部类：当场实现
Animal a = new Animal() {
    @Override
    public void shout() {
        System.out.println("汪汪");
    }
};
a.shout();
```

> 匿名内部类常用于：**事件监听**、**线程**、**集合排序（Comparator）**

## Lambda 表达式

**简化匿名内部类的写法，只保留核心参数和逻辑**

前提：接口必须是**函数式接口**（只有一个抽象方法）

```java
// 匿名内部类的写法
Comparator<Integer> c1 = new Comparator<>() {
    @Override
    public int compare(Integer a, Integer b) {
        return a - b;
    }
};

// Lambda 写法
Comparator<Integer> c2 = (a, b) -> a - b;
```

### Lambda 语法

```java
(参数) -> { 方法体 }

// 示例
Runnable r1 = () -> System.out.println("run");           // 无参
Consumer<String> r2 = (s) -> System.out.println(s);      // 单参
BinaryOperator<Integer> r3 = (a, b) -> a + b;             // 多参
```

| 场景 | 匿名内部类 | Lambda |
| ---- | ---------- | ------ |
| 排序 | `new Comparator<>() { compare(a,b) { ... } }` | `(a, b) -> a - b` |
| 线程 | `new Runnable() { run() { ... } }` | `() -> { ... }` |
| 集合遍历 | `new Consumer<>() { accept(s) { ... } }` | `s -> System.out.println(s)` |

> **Lambda 本质**：函数式接口的快捷写法，代码更简洁  
> 常用函数式接口：`Runnable`、`Comparator`、`Consumer`、`Function`、`Predicate`

---

**相关笔记**: [[../Java-basics/java-basics]] · [[syntax]] · [[collection]] · [[exception]]
