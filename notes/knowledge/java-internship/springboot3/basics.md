---
created: 2026-05-27
tags:
  - java/internship
  - springboot3
related:
  - "[[../_index]]"
  - "[[../ssm/springboot]]"
  - "[[../ssm/spring]]"
---

# Spring Boot 3

> Spring Boot 3 = **Java 17+** + **Jakarta EE** + **GraalVM**

## 环境要求

| 依赖 | 版本 |
| ---- | ---- |
| Java | **17+**（最低要求） |
| Spring Boot | 3.x |
| Spring Framework | 6.x |
| Jakarta EE | **`javax` → `jakarta`** |

## 从 Spring Boot 2 迁移

### 包名变更

| Spring Boot 2 | Spring Boot 3 |
| ------------- | ------------- |
| `javax.servlet.*` | `jakarta.servlet.*` |
| `javax.persistence.*` | `jakarta.persistence.*` |
| `javax.validation.*` | `jakarta.validation.*` |
| `javax.annotation.*` | `jakarta.annotation.*` |

```gradle
// build.gradle
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.4.0'
    id 'io.spring.dependency-management' version '1.1.6'
}

java {
    sourceCompatibility = JavaVersion.VERSION_17
    targetCompatibility = JavaVersion.VERSION_17
}
```

```java
// Spring Boot 3：javax 换成 jakarta
import jakarta.persistence.*;
import jakarta.validation.constraints.*;
import jakarta.annotation.Resource;
```

### 主要变化

| 变化 | 说明 |
| ---- | ---- |
| **Java 17 起步** | 不再支持 Java 8 / 11 |
| **Jakarta EE 9+** | `javax.*` → `jakarta.*` |
| **Spring Security** | 废弃 `WebSecurityConfigurerAdapter`，改用组件化配置 |
| **Spring MVC** | 默认 **PathPatternParser** 替代 AntPathMatcher |
| **HttpMethod 枚举** | 从 String 改为 `HttpMethod` 枚举 |
| **原生编译** | 支持 GraalVM native-image |
| **虚拟线程** | 支持 JDK 21 Virtual Threads |

## 快速开始

```java
// 启动类
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

```yaml
# application.yml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: 123456
```

## 虚拟线程（JDK 21+）

**极大简化高并发编程，一个请求一个虚拟线程**

```yaml
# application.yml
spring:
  threads:
    virtual:
      enabled: true
```

```java
// 不启用虚拟线程时，需要手动配置线程池
// 启用后，Tomcat 每个请求自动使用虚拟线程
@Bean
public TomcatProtocolHandlerCustomizer<?> protocolHandlerVirtualThreadExecutor() {
    return protocolHandler -> protocolHandler.setExecutor(Executors.newVirtualThreadPerTaskExecutor());
}
```

## 运行时信息（Actuator）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
  endpoint:
    health:
      show-details: always
```

## 常见依赖

```xml
<!-- Web -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<!-- MyBatis -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>

<!-- 数据库 -->
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- Lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

> 完整笔记详见 [[../ssm/springboot]]

---

**相关笔记**: [[../ssm/springboot]] · [[../ssm/spring]] · [[../vue3/basics]]
