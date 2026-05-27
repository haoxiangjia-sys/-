---
created: 2026-05-27
tags:
  - java/internship
  - database/mysql
related:
  - "[[sql/mysql]]"
  - "[[sql/ddl]]"
  - "[[sql/dml]]"
---

# DQL — 数据查询

> **DQL = Data Query Language，查询数据的语言**

## 基本查询

```sql
-- 查询所有列
SELECT * FROM user;

-- 查询指定列
SELECT name, age FROM user;

-- 查询常量表达式
SELECT 1 + 1;          -- 结果: 2
SELECT 'hello';        -- 结果: hello

-- 起别名
SELECT name AS 姓名, age AS 年龄 FROM user;

-- 去重查询
SELECT DISTINCT dept_id FROM user;
```

## 条件查询 WHERE

```sql
-- 比较运算符
SELECT * FROM user WHERE age > 18;
SELECT * FROM user WHERE age >= 18;
SELECT * FROM user WHERE age = 18;
SELECT * FROM user WHERE age != 18;
SELECT * FROM user WHERE age <> 18;   -- != 的另一种写法

-- 逻辑运算符
SELECT * FROM user WHERE age > 18 AND dept_id = 1;
SELECT * FROM user WHERE age > 18 OR dept_id = 1;
SELECT * FROM user WHERE NOT age > 18;

-- 范围查询
SELECT * FROM user WHERE age BETWEEN 18 AND 30;
SELECT * FROM user WHERE age IN (18, 20, 25);

-- 模糊查询
SELECT * FROM user WHERE name LIKE '张%';   -- 以"张"开头
SELECT * FROM user WHERE name LIKE '%三';   -- 以"三"结尾
SELECT * FROM user WHERE name LIKE '%三%';  -- 包含"三"
SELECT * FROM user WHERE name LIKE '张_';   -- "张"后面一个字符

-- 空值判断
SELECT * FROM user WHERE email IS NULL;
SELECT * FROM user WHERE email IS NOT NULL;
```

> **`NULL` 不能用 `= NULL` 判断，必须用 `IS NULL` 或 `IS NOT NULL`**

## 排序 ORDER BY

```sql
-- 升序（默认）
SELECT * FROM user ORDER BY age;

-- 降序
SELECT * FROM user ORDER BY age DESC;

-- 多字段排序
SELECT * FROM user ORDER BY dept_id ASC, age DESC;
```

## 分页 LIMIT

```sql
-- 前 5 条
SELECT * FROM user LIMIT 5;

-- 跳过 10 条，取 5 条（第 11~15 条）
SELECT * FROM user LIMIT 5 OFFSET 10;

-- 简写：LIMIT 偏移量, 条数
SELECT * FROM user LIMIT 10, 5;   -- 等价于上方
```

## 聚合函数

| 函数 | 作用 |
| --- | --- |
| `COUNT(列)` | 计数 |
| `SUM(列)` | 求和 |
| `AVG(列)` | 平均值 |
| `MAX(列)` | 最大值 |
| `MIN(列)` | 最小值 |

```sql
SELECT COUNT(*) FROM user;                -- 总行数
SELECT COUNT(email) FROM user;            -- email 非空的行数
SELECT COUNT(DISTINCT dept_id) FROM user; -- 去重后部门数
SELECT SUM(age) FROM user;                -- 年龄总和
SELECT AVG(age) FROM user;                -- 平均年龄
SELECT MAX(age), MIN(age) FROM user;      -- 最大、最小年龄
```

## 分组 GROUP BY / HAVING

```sql
-- 按部门统计人数
SELECT dept_id, COUNT(*) AS cnt FROM user GROUP BY dept_id;

-- 分组后筛选（HAVING，不能用 WHERE）
SELECT dept_id, COUNT(*) AS cnt FROM user
GROUP BY dept_id
HAVING cnt > 5;

-- 分组 + 排序
SELECT dept_id, AVG(age) AS avg_age FROM user
GROUP BY dept_id
ORDER BY avg_age DESC;
```

> **`WHERE` 过滤行 → `GROUP BY` 分组 → `HAVING` 过滤组 → `ORDER BY` 排序**

## 多表查询

> 详见 [[sql/joins]] — JOIN、子查询、UNION

## 执行顺序

```
FROM       → 确定数据源
WHERE      → 过滤行
GROUP BY   → 分组
HAVING     → 过滤组
SELECT     → 选择列
ORDER BY   → 排序
LIMIT      → 分页
```

---

**相关笔记**: [[sql/mysql]] · [[sql/ddl]] · [[sql/dml]] · [[sql/joins]]
