---
created: 2026-05-27
tags:
  - java/internship
  - database/mysql
related:
  - "[[mysql]]"
  - "[[dql]]"
  - "[[ddl]]"
---

# 多表查询

> **多表查询 = 一次查询从多个表中获取数据**

## 连接查询 JOIN

### 准备表结构

```sql
-- 部门表
CREATE TABLE dept (
    id   INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
);

-- 员工表
CREATE TABLE emp (
    id       INT PRIMARY KEY AUTO_INCREMENT,
    name     VARCHAR(20),
    age      INT,
    dept_id  INT
);

INSERT INTO dept VALUES (1, '技术部'), (2, '市场部'), (3, '财务部'), (4, '运营部');
INSERT INTO emp VALUES
    (1, '张三', 25, 1),
    (2, '李四', 30, 1),
    (3, '王五', 28, 2),
    (4, '赵六', 35, 2),
    (5, '孙七', 22, 3),
    (6, '周八', 40, NULL);   -- 未分配部门
```

### 内连接 INNER JOIN

**只取两张表的交集（匹配到的数据）**

```sql
-- 写法一
SELECT e.name, d.name AS dept_name
FROM emp e
INNER JOIN dept d ON e.dept_id = d.id;

-- 写法二（INNER 可省略）
SELECT e.name, d.name AS dept_name
FROM emp e
JOIN dept d ON e.dept_id = d.id;
```

```
结果：
张三 | 技术部
李四 | 技术部
王五 | 市场部
赵六 | 市场部
孙七 | 财务部
-- 周八没出来（dept_id = NULL，没匹配到）
```

### 左连接 LEFT JOIN

**左表全保留，右表没有匹配的填 NULL**

```sql
SELECT e.name, d.name AS dept_name
FROM emp e
LEFT JOIN dept d ON e.dept_id = d.id;
```

```
结果：
张三 | 技术部
李四 | 技术部
王五 | 市场部
赵六 | 市场部
孙七 | 财务部
周八 | NULL     ← 周八没有部门，dept_name 为 NULL
```

> **常用场景**：查所有员工及其部门，没部门的员工也要显示

### 右连接 RIGHT JOIN

**右表全保留，左表没有匹配的填 NULL**

```sql
SELECT e.name, d.name AS dept_name
FROM emp e
RIGHT JOIN dept d ON e.dept_id = d.id;
```

```
结果：
张三   | 技术部
李四   | 技术部
王五   | 市场部
赵六   | 市场部
孙七   | 财务部
NULL   | 运营部    ← 运营部没有员工
```

> **常用场景**：查所有部门及员工，空部门也要显示

### 自连接 SELF JOIN

**同一张表自己连接自己，必须用别名**

```sql
-- 员工表结构：id, name, manager_id（上级ID）
-- 查询每个员工及其上级姓名
SELECT e.name AS 员工, m.name AS 上级
FROM emp e
LEFT JOIN emp m ON e.manager_id = m.id;
```

```
结果：
张三 | NULL     ← 老板没有上级
李四 | 张三
王五 | 张三
赵六 | 李四
```

> **常用场景**：树形结构（组织架构、分类目录、评论回复）

### JOIN 执行顺序

```
FROM emp e         ← 确定左表
JOIN dept d        ← 确定右表
ON e.dept_id = d.id ← 确定匹配条件
```

> 先做笛卡尔积，再按 ON 条件过滤

### 连接类型对比

```
INNER JOIN                LEFT JOIN                RIGHT JOIN
 ┌─────┐                  ┌─────┐                  ┌─────┐
 │ ║  ║ │                 │ ║  ║ │                 │ ║  ║ │
 │ ║ab║ │                 │ ║ab║ │                 │ ║ab║ │
 │ ║  ║ │                 │ ║  ║ │                 │ ║  ║ │
 └─────┘                  └──║──┘                  └──║──┘
  交集                     左表全保留                 右表全保留
```

## 子查询

### 标量子查询（返回单个值）

```sql
-- 查询工资最高的员工
SELECT * FROM emp
WHERE salary = (SELECT MAX(salary) FROM emp);

-- 查询在"技术部"工作的员工
SELECT * FROM emp
WHERE dept_id = (SELECT id FROM dept WHERE name = '技术部');
```

### 行子查询（返回一行）

```sql
-- 查询和"张三"同部门同龄的员工
SELECT * FROM emp
WHERE (dept_id, age) = (SELECT dept_id, age FROM emp WHERE name = '张三');
```

### 表子查询（返回多行多列，当作临时表）

```sql
-- 查询各部门平均工资，过滤出平均工资 > 5000 的部门
SELECT d.name, tmp.avg_salary
FROM dept d
JOIN (
    SELECT dept_id, AVG(salary) AS avg_salary
    FROM emp
    GROUP BY dept_id
    HAVING avg_salary > 5000
) tmp ON d.id = tmp.dept_id;
```

### EXISTS 子查询

**检查是否存在，返回布尔值**

```sql
-- 查询有员工的部门
SELECT * FROM dept d
WHERE EXISTS (SELECT 1 FROM emp e WHERE e.dept_id = d.id);

-- 查询没有员工的部门
SELECT * FROM dept d
WHERE NOT EXISTS (SELECT 1 FROM emp e WHERE e.dept_id = d.id);
```

> `EXISTS` 找到第一条匹配就停止，效率通常高于 `IN`

### IN 子查询

```sql
-- 查询有部门的员工
SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept);

-- 查询没有部门的员工
SELECT * FROM emp WHERE dept_id NOT IN (SELECT id FROM dept WHERE id IS NOT NULL);
```

> ⚠️ `NOT IN` 如果子查询结果中有 `NULL`，整个查询结果为空！
> 推荐用 `NOT EXISTS` 替代 `NOT IN`

## 联合查询 UNION

**合并多个 SELECT 的结果**

```sql
-- UNION：自动去重
SELECT name FROM emp
UNION
SELECT name FROM dept;

-- UNION ALL：不去重（效率更高）
SELECT name FROM emp
UNION ALL
SELECT name FROM dept;
```

> **要求**：每个 SELECT 的列数必须相同，对应列的类型要兼容

## 实战示例

### 多表联查

```sql
-- 需求：查询每个员工的姓名、部门名、薪资等级
SELECT
    e.name,
    d.name AS dept_name,
    s.grade
FROM emp e
JOIN dept d ON e.dept_id = d.id
JOIN salary_grade s ON e.salary BETWEEN s.low_salary AND s.high_salary;
```

### 子查询 + JOIN

```sql
-- 需求：查询各部门工资最高的员工
SELECT d.name AS 部门, e.name AS 员工, e.salary
FROM dept d
JOIN emp e ON e.dept_id = d.id
WHERE e.salary = (
    SELECT MAX(salary) FROM emp WHERE dept_id = d.id
);
```

### 自连接 + 子查询

```sql
-- 需求：查询所有员工的上级，如果没上级显示"老板"
SELECT
    e.name AS 员工,
    COALESCE(m.name, '老板') AS 上级
FROM emp e
LEFT JOIN emp m ON e.manager_id = m.id;
```

### UNION 实现完整列表

```sql
-- 需求：所有部门及员工名单（部门名 + 人名混合显示）
SELECT name AS 名称 FROM dept
UNION ALL
SELECT name FROM emp;
```

---

**相关笔记**: [[mysql]] · [[dql]] · [[ddl]]
