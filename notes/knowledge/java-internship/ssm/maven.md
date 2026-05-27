---
created: 2026-05-21
tags:
  - java/internship
  - java/ssm
related:
  - "[[../ssm]]"
  - "[[spring]]"
---

# Maven

**Maven = 项目管理工具（依赖管理 + 项目构建）**

## Maven 坐标（GAV）

每个依赖/项目都有唯一坐标：

```xml
<groupId>com.example</groupId>      <!-- 公司/组织名（反向域名） -->
<artifactId>my-app</artifactId>      <!-- 项目名 -->
<version>1.0.0</version>             <!-- 版本号 -->
```

## 依赖管理

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.30</version>
        <!-- <scope>compile</scope> -->   <!-- 默认 compile -->
    </dependency>
</dependencies>
```

### 依赖范围（scope）

| scope | 编译 | 测试 | 运行 | 示例 |
| ----- | ---- | ---- | ---- | ---- |
| **compile**（默认） | ✅ | ✅ | ✅ | spring-core |
| **provided** | ✅ | ✅ | ❌ | servlet-api（Tomcat 自带） |
| **runtime** | ❌ | ✅ | ✅ | MySQL 驱动 |
| **test** | ❌ | ✅ | ❌ | JUnit |
| **system** | ✅ | ✅ | ❌ | 本地 jar（不推荐） |

## 生命周期

```
clean → validate → compile → test → package → verify → install → deploy
```

| 常用命令 | 说明 |
| -------- | ---- |
| `mvn clean` | 删除 target 目录 |
| `mvn compile` | 编译源代码 |
| `mvn test` | 运行测试 |
| `mvn package` | 打包（jar / war） |
| `mvn install` | 安装到本地仓库 |
| `mvn clean install` | 最常用：清空 + 重新构建 |

> **依赖传递**：A 依赖 B，B 依赖 C → A 自动拿到 C  
> 排除依赖：
> ```xml
> <exclusions>
>     <exclusion>
>         <groupId> unwanted </groupId>
>         <artifactId> unwanted </artifactId>
>     </exclusion>
> </exclusions>
> ```

---

**相关笔记**: [[../ssm]] · [[spring]] · [[springboot]]
