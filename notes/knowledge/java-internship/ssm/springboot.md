---
created: 2026-05-21
tags:
  - java/internship
  - java/ssm
related:
  - "[[../ssm]]"
  - "[[spring]]"
  - "[[mybatis]]"
---

# SpringBoot

**SpringBoot = 简化 Spring 配置（开箱即用）**

```java
@SpringBootApplication   // 启动类
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

## 核心概念

| 概念 | 说明 |
| ---- | ---- |
| **起步依赖（Starter）** | 封装好的一组依赖，引入一个 = 引入一堆 |
| **自动配置** | 根据依赖自动配置 Bean（不用写 XML） |
| **application.yml** | 统一配置文件（替代 XML） |

## 常用 Starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- 自动包含：Spring + SpringMVC + Tomcat + Jackson -->
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.3.0</version>
</dependency>
```

## 配置文件

```yaml
# application.yml（推荐，比 properties 更简洁）
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.example.entity
  configuration:
    map-underscore-to-camel-case: true
```

## 自动配置原理

```
@SpringBootApplication
  ├── @SpringBootConfiguration     // 标记为配置类
  ├── @ComponentScan               // 扫描当前包及其子包
  └── @EnableAutoConfiguration      // 核心：自动配置
       └── 读取 META-INF/spring.factories（里面有 XXXAutoConfiguration）
           └── 条件装配（有相关依赖才生效）
```

**条件装配注解**：

| 注解 | 说明 |
| ---- | ---- |
| `@ConditionalOnClass` | 类路径有指定类才生效 |
| `@ConditionalOnMissingBean` | 容器中没有指定 Bean 才生效 |
| `@ConditionalOnProperty` | 配置中有指定属性才生效 |

## 配置绑定

```java
@Component
@ConfigurationProperties(prefix = "aliyun")   // 绑定 yml 中的 aliyun.*
@Data
public class AliyunProperties {
    private String accessKey;
    private String secretKey;
    private String bucket;
}
```

```yaml
# application.yml
aliyun:
  access-key: xxx
  secret-key: xxx
  bucket: my-bucket
```

## @SpringBootApplication 常用注解补充

| 注解 | 说明 |
| ---- | ---- |
| `@SpringBootApplication` | 启动类注解（组合注解） |
| `@EnableConfigurationProperties` | 开启配置绑定 |
| `@ConfigurationProperties` | 将 yml 属性绑定到类 |
| `@ConditionalOnXxx` | 条件装配系列 |
| `@Import` | 导入配置类 |
| `@SpringBootTest` | 测试类注解 |

---

**相关笔记**: [[../ssm]] · [[spring]] · [[mybatis]] · [[maven]]
