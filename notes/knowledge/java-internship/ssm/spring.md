---
created: 2026-05-21
tags:
  - java/internship
  - java/ssm
related:
  - "[[../ssm]]"
  - "[[springmvc]]"
---

# Spring

**Spring 核心 = IoC（控制反转）+ DI（依赖注入）**

## IoC（控制反转）

**原来**：自己 new 对象 → `UserService userService = new UserService();`  
**Spring**：把对象交给 Spring 容器管理，用的时候直接拿

```java
// 1. 告诉 Spring 这个类归它管
@Component   // 把当前类交给 Spring 管理（创建 Bean）
public class UserService {
    public void save() {
        System.out.println("保存用户");
    }
}

// 2. 从容器中获取
public class Main {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = ctx.getBean(UserService.class);
        userService.save();
    }
}
```

## 什么是 Bean

**Bean = Spring 容器管理的 Java 对象**

```java
// 没有 Spring 时：程序员自己 new
UserService service = new UserService();

// 有 Spring 后：Spring 创建并管理，我们只管用
// UserService 就是一个 Bean
@Service
public class UserService { ... }

// 从容器获取
@Autowired
private UserService userService;
```

| 特性 | 说明 |
| ---- | ---- |
| **谁创建** | Spring IoC 容器（不是 `new`） |
| **谁管理** | Spring 容器负责创建 → 注入 → 初始化 → 销毁 |
| **默认 scope** | `singleton`（整个容器只有一个实例） |
| **命名** | 默认类名首字母小写（`userService`），可指定 |

> **Spring Boot 为什么不用配 Bean？**  
> Spring Boot 通过 **自动配置**（`@EnableAutoConfiguration`）和 **starter** 机制，根据 classpath 依赖自动注册常用 Bean（如 `DataSource`、`RestTemplate`）。想自定义时加 `@Bean` 覆盖即可。

## Bean 的三种创建方式

### 方式一：组件扫描（最常用）

```java
@Component   // @Service / @Repository / @Controller 都可以
public class UserService {
    public void save() { ... }
}
// 需要包扫描：@ComponentScan("com.example")
```

### 方式二：@Bean 显式声明（适合第三方类）

```java
@Configuration
public class AppConfig {
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();   // RestTemplate 不是自己的类，没法加 @Component
    }
}
```

### 方式三：XML 配置（老项目）

```xml
<bean id="userService" class="com.example.UserService"/>
```

> **三种方式对比**：

| 方式 | 适用场景 | 优点 |
| ---- | -------- | ---- |
| 组件扫描 + 注解 | 自己写的类 | 最简洁，一行注解 |
| `@Bean` + `@Configuration` | 第三方类、复杂初始化 | 显式控制创建逻辑 |
| XML | 遗留项目 | 统一管理，不用重新编译 |

## DI（依赖注入）

```java
@Service
public class UserService {
    @Autowired        // 按类型注入（byType）
    private UserDao userDao;

    @Autowired
    @Qualifier("userDaoImpl")   // 指定名称（配合 @Autowired）
    private UserDao userDao2;

    @Resource(name = "userDaoImpl")     // 按名称注入（javax）
    private UserDao userDao3;
}
```

> `@Autowired` 默认按类型匹配，找到多个同类型 Bean 会报错 → 配合 `@Qualifier` 指定名字  
> `@Resource` 默认按名称匹配，找不到再按类型

## Bean 生命周期

**Spring 容器中 Bean 的完整流程**：

```
容器启动 → 实例化 → 属性注入 → 初始化 → 就绪 → 销毁
                ↓           ↓           ↓
          构造方法    @Autowired   @PostConstruct
                                    ↓
                              init-method
```

```java
@Component
public class UserService {

    public UserService() {
        System.out.println("1. 实例化：构造方法");
    }

    @Autowired
    private UserDao userDao;  // 2. 属性注入

    @PostConstruct
    public void init() {
        System.out.println("3. 初始化完成，执行 init");
    }

    // 4. Bean 就绪，可以正常使用

    @PreDestroy
    public void destroy() {
        System.out.println("5. 容器关闭前销毁");
    }
}
```

> `@PostConstruct` / `@PreDestroy` 属于 Jakarta EE（`jakarta.annotation.*`）

## 作用域

```java
@Component
@Scope("singleton")     // 默认：单例，整个容器一个实例
// @Scope("prototype")  // 原型：每次获取都新创建
// @Scope("request")    // 每个 HTTP 请求一个实例（Web 环境）
// @Scope("session")    // 每个 HTTP Session 一个实例（Web 环境）
```

## 注解开发汇总

| 注解 | 作用 | 说明 |
| ---- | ---- | ---- |
| `@Component` / `@Service` / `@Controller` / `@Repository` | 声明 Bean | 组件扫描方式 |
| `@Bean` | 声明第三方 Bean | 放在 `@Configuration` 类中 |
| `@Autowired` | 按类型注入 | Spring 原生 |
| `@Qualifier` | 按名称注入 | 配合 `@Autowired` |
| `@Resource` | 按名称/类型注入 | Jakarta EE，JDK 自带 |
| `@Value` | 注入普通类型属性 | `${}` 读取配置文件 |
| `@Scope` | 设置作用域 | singleton / prototype |
| `@PostConstruct` / `@PreDestroy` | 生命周期回调 | 初始化 / 销毁 |
| `@Configuration` | 声明配置类 | 替代 XML |
| `@ComponentScan` | 包扫描 | 指定要扫描的包路径 |

---

**相关笔记**: [[../ssm]] · [[springmvc]] · [[maven]]
