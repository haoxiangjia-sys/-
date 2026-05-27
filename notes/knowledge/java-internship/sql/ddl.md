---
created: 2026-05-21
tags:
  - java/internship
  - database/mysql
related:
  - "[[sql/mysql]]"
  - "[[sql/dml]]"
---

# DDL — 数据库/表操作

> **DDL = Data Definition Language，定义数据库结构**

## 数据库操作

```sql
-- 查所有数据库
SHOW DATABASES;

-- 创建
CREATE DATABASE db_name;

-- 创建（指定字符集）
CREATE DATABASE db_name [default charset 字符集][collate 排序规则]

-- 使用
USE db_name;

-- 查看当前使用的数据库
SELECT DATABASE();

-- 删除
DROP DATABASE db_name;
```

## 表操作

```sql
-- 查所有表
SHOW TABLES;

-- 创建表
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,  -- 主键、自增
    name     VARCHAR(20) NOT NULL,             -- 非空
    age      INT DEFAULT 0,                     -- 默认值
    email    VARCHAR(50) UNIQUE,                -- 唯一约束
    dept_id  INT,
    FOREIGN KEY (dept_id) REFERENCES dept(id)  -- 外键
);

-- 查看表结构
DESC user;

-- 删除表
DROP TABLE user;
```

---

**相关笔记**: [[sql/mysql]] · [[sql/dml]]
