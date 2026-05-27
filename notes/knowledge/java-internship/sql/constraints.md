---
created: 2026-05-27
tags:
  - java/internship
  - database/mysql
related:
  - "[[mysql]]"
  - "[[ddl]]"
  - "[[dql]]"
---

# MySQL 约束

> **约束 = 对表中数据的限制规则，保证数据的完整性和正确性**

## 约束类型

| 约束 | 关键字 | 说明 |
| ---- | ------ | ---- |
| **主键约束** | `PRIMARY KEY` | 唯一标识一条记录（唯一 + 非空） |
| **非空约束** | `NOT NULL` | 列值不能为 `NULL` |
| **唯一约束** | `UNIQUE` | 列值不能重复（但可为 `NULL`） |
| **默认约束** | `DEFAULT` | 未指定值时使用默认值 |
| **检查约束** | `CHECK` | 自定义规则（MySQL 8.0.16+ 生效） |
| **外键约束** | `FOREIGN KEY` | 引用其他表的记录 |
| **自增** | `AUTO_INCREMENT` | 自动生成递增编号 |

## 创建约束

### 建表时指定

```sql
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,   -- 主键 + 自增
    name     VARCHAR(20) NOT NULL,              -- 非空
    age      INT DEFAULT 0,                     -- 默认值 0
    email    VARCHAR(50) UNIQUE,                -- 唯一
    gender   CHAR(1) CHECK (gender IN ('男', '女')),  -- 检查约束
    dept_id  INT,
    FOREIGN KEY (dept_id) REFERENCES dept(id)   -- 外键
);
```

### 表后追加约束

```sql
CREATE TABLE user (
    id       INT,
    name     VARCHAR(20) NOT NULL,
    age      INT DEFAULT 0,
    email    VARCHAR(50),
    dept_id  INT
);

-- 添加主键
ALTER TABLE user ADD PRIMARY KEY (id);

-- 添加自增
ALTER TABLE user MODIFY id INT AUTO_INCREMENT;

-- 添加唯一约束
ALTER TABLE user ADD UNIQUE (email);

-- 添加检查约束
ALTER TABLE user ADD CONSTRAINT chk_gender CHECK (gender IN ('男', '女'));

-- 添加外键
ALTER TABLE user ADD CONSTRAINT fk_dept
    FOREIGN KEY (dept_id) REFERENCES dept(id);
```

## 主键约束 PRIMARY KEY

**特点**：唯一 + 非空，一张表只能有一个主键

```sql
-- 单列主键
CREATE TABLE user (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

-- 复合主键（多个列共同作为主键）
CREATE TABLE score (
    student_id INT,
    course_id  INT,
    score      INT,
    PRIMARY KEY (student_id, course_id)
);

-- 自增主键（推荐）
CREATE TABLE user (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
);
```

> 自增主键默认从 1 开始，步长为 1

## 外键约束 FOREIGN KEY

**保持数据一致性，防止误操作**

```sql
-- 建表时指定
CREATE TABLE employee (
    id         INT PRIMARY KEY AUTO_INCREMENT,
    name       VARCHAR(20),
    dept_id    INT,
    FOREIGN KEY (dept_id) REFERENCES dept(id)
);

-- 追加外键
ALTER TABLE employee ADD CONSTRAINT fk_dept
    FOREIGN KEY (dept_id) REFERENCES dept(id);
```

### 级联操作

| 选项 | 说明 |
| ---- | ---- |
| `ON DELETE CASCADE` | 删除父表记录，自动删除子表关联记录 |
| `ON DELETE SET NULL` | 删除父表记录，子表关联字段设为 NULL |
| `ON UPDATE CASCADE` | 更新父表主键，子表外键自动更新 |

```sql
CREATE TABLE employee (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    name     VARCHAR(20),
    dept_id  INT,
    FOREIGN KEY (dept_id) REFERENCES dept(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

> 外键会影响性能，**大型项目通常不用物理外键**，而是在代码层面维护引用关系

## 唯一约束 UNIQUE

**列值不能重复，但允许多个 `NULL`**

```sql
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    email    VARCHAR(50) UNIQUE,              -- 单列唯一
    phone    VARCHAR(20)
);

-- 复合唯一（两个列组合不能重复）
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    phone    VARCHAR(20),
    email    VARCHAR(50),
    UNIQUE (phone, email)
);
```

## 非空约束 NOT NULL

**列值不能为 NULL**

```sql
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    name     VARCHAR(20) NOT NULL,
    email    VARCHAR(50)
);

-- 追加非空
ALTER TABLE user MODIFY name VARCHAR(20) NOT NULL;

-- 取消非空
ALTER TABLE user MODIFY name VARCHAR(20) NULL;
```

## 默认约束 DEFAULT

**插入时未指定值则使用默认值**

```sql
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    name     VARCHAR(20),
    age      INT DEFAULT 0,
    status   INT DEFAULT 1 COMMENT '0=禁用 1=启用',
    create_time DATETIME DEFAULT NOW()
);
```

## 检查约束 CHECK

**自定义数据规则（MySQL 8.0.16+ 才真正生效）**

```sql
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    age      INT CHECK (age >= 0 AND age <= 150),
    gender   CHAR(1) CHECK (gender IN ('男', '女')),
    salary   DECIMAL(10,2) CHECK (salary > 0)
);

-- 命名约束（方便删除）
CREATE TABLE user (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    age      INT,
    CONSTRAINT chk_age CHECK (age >= 0 AND age <= 150)
);

-- 删除检查约束
ALTER TABLE user DROP CHECK chk_age;
```

## 删除约束

```sql
-- 删除主键
ALTER TABLE user DROP PRIMARY KEY;

-- 删除唯一约束
ALTER TABLE user DROP INDEX email;

-- 删除外键
ALTER TABLE user DROP FOREIGN KEY fk_dept;

-- 删除默认值
ALTER TABLE user ALTER age DROP DEFAULT;
```

## 约束建议

| 场景 | 推荐约束 |
| ---- | -------- |
| 每条记录的唯一标识 | `PRIMARY KEY` + `AUTO_INCREMENT` |
| 不允许为空 | `NOT NULL` |
| 不允许重复（如邮箱） | `UNIQUE` |
| 有默认值（如状态） | `DEFAULT` |
| 数据范围校验 | `CHECK`（应用层也应该校验） |
| 表间引用关系 | 代码层维护（不推荐物理外键） |

---

**相关笔记**: [[mysql]] · [[ddl]] · [[dql]]
