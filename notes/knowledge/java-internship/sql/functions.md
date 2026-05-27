---
created: 2026-05-27
tags:
  - java/internship
  - database/mysql
related:
  - "[[mysql]]"
  - "[[dql]]"
---

# MySQL 函数

## 字符串函数

| 函数 | 说明 | 示例 |
| ---- | ---- | ---- |
| `CONCAT(s1, s2, ...)` | 拼接字符串 | `CONCAT('Hello', ' ', 'World')` → `Hello World` |
| `LOWER(str)` | 转小写 | `LOWER('Hello')` → `hello` |
| `UPPER(str)` | 转大写 | `UPPER('Hello')` → `HELLO` |
| `TRIM(str)` | 去两端空格 | `TRIM('  hello  ')` → `hello` |
| `LENGTH(str)` | 字符串长度（字节） | `LENGTH('hello')` → `5` |
| `CHAR_LENGTH(str)` | 字符数（中英文都算1） | `CHAR_LENGTH('你好')` → `2` |
| `SUBSTRING(str, pos, len)` | 截取子串 | `SUBSTRING('hello', 2, 3)` → `ell` |
| `REPLACE(str, from, to)` | 替换 | `REPLACE('hello', 'l', 'x')` → `hexxo` |
| `INSTR(str, substr)` | 返回子串首次出现位置 | `INSTR('hello', 'l')` → `3` |
| `LPAD(str, len, pad)` | 左填充到指定长度 | `LPAD('1', 3, '0')` → `001` |
| `RPAD(str, len, pad)` | 右填充 | `RPAD('1', 3, '0')` → `100` |

```sql
-- 拼接
SELECT CONCAT(last_name, first_name) AS full_name FROM user;

-- 手机号脱敏（中间4位用*代替）
SELECT CONCAT(LEFT(phone, 3), '****', RIGHT(phone, 4)) AS masked FROM user;

-- 截取
SELECT SUBSTRING('hello world', 1, 5);   -- hello

-- 替换
SELECT REPLACE('2023-01-01', '-', '/');  -- 2023/01/01

-- 填充（编号补零）
SELECT LPAD(id, 5, '0') AS order_no FROM orders;  -- 00001, 00002...
```

## 数值函数

| 函数 | 说明 | 示例 |
| ---- | ---- | ---- |
| `ROUND(x, d)` | 四舍五入（d 位小数） | `ROUND(3.14159, 2)` → `3.14` |
| `CEIL(x)` | 向上取整 | `CEIL(3.14)` → `4` |
| `FLOOR(x)` | 向下取整 | `FLOOR(3.14)` → `3` |
| `ABS(x)` | 绝对值 | `ABS(-10)` → `10` |
| `MOD(a, b)` | 取模（a % b） | `MOD(10, 3)` → `1` |
| `RAND()` | 随机数 0~1 | `RAND()` → `0.12345` |
| `TRUNCATE(x, d)` | 截断（不四舍五入） | `TRUNCATE(3.14159, 2)` → `3.14` |

```sql
-- 四舍五入保留2位小数
SELECT ROUND(3.14159, 2);    -- 3.14

-- 向上取整
SELECT CEIL(3.01);           -- 4

-- 随机排序
SELECT * FROM user ORDER BY RAND();

-- 随机取3条
SELECT * FROM user ORDER BY RAND() LIMIT 3;
```

## 日期函数

| 函数 | 说明 | 示例 |
| ---- | ---- | ---- |
| `NOW()` | 当前日期时间 | `NOW()` → `2026-05-27 10:30:00` |
| `CURDATE()` | 当前日期 | `CURDATE()` → `2026-05-27` |
| `CURTIME()` | 当前时间 | `CURTIME()` → `10:30:00` |
| `YEAR(date)` | 提取年 | `YEAR('2026-05-27')` → `2026` |
| `MONTH(date)` | 提取月 | `MONTH('2026-05-27')` → `5` |
| `DAY(date)` | 提取日 | `DAY('2026-05-27')` → `27` |
| `DATE_FORMAT(date, fmt)` | 格式化日期 | `DATE_FORMAT(NOW(), '%Y-%m')` → `2026-05` |
| `DATEDIFF(d1, d2)` | 日期差（天） | `DATEDIFF('2026-06-01', '2026-05-27')` → `5` |
| `DATE_ADD(date, INTERVAL n unit)` | 日期加 | `DATE_ADD(NOW(), INTERVAL 7 DAY)` |
| `DATE_SUB(date, INTERVAL n unit)` | 日期减 | `DATE_SUB(NOW(), INTERVAL 1 MONTH)` |

```sql
-- 当前时间
SELECT NOW();              -- 2026-05-27 10:30:00
SELECT CURDATE();          -- 2026-05-27

-- 格式化
SELECT DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s');  -- 2026-05-27 10:30:00
SELECT DATE_FORMAT(NOW(), '%Y年%m月%d日');         -- 2026年05月27日

-- 日期计算
SELECT DATE_ADD(NOW(), INTERVAL 7 DAY);           -- 7天后
SELECT DATE_SUB(NOW(), INTERVAL 3 MONTH);         -- 3个月前

-- 查询最近7天注册的用户
SELECT * FROM user WHERE create_time >= DATE_SUB(NOW(), INTERVAL 7 DAY);

-- 查询本月数据
SELECT * FROM orders WHERE YEAR(create_time) = YEAR(NOW())
  AND MONTH(create_time) = MONTH(NOW());

-- 年龄计算
SELECT name, TIMESTAMPDIFF(YEAR, birth_date, CURDATE()) AS age FROM user;
```

### 常用日期格式化符

| 符号 | 含义 | 示例 |
| ---- | ---- | ---- |
| `%Y` | 四位数年份 | `2026` |
| `%y` | 两位数年份 | `26` |
| `%m` | 月份（01~12） | `05` |
| `%d` | 日（01~31） | `27` |
| `%H` | 时（00~23） | `10` |
| `%i` | 分（00~59） | `30` |
| `%s` | 秒（00~59） | `00` |
| `%W` | 星期名 | `Wednesday` |

## 流程函数

| 函数 | 说明 |
| ---- | ---- |
| `IF(expr, v1, v2)` | 表达式为真返回 v1，否则 v2 |
| `IFNULL(v1, v2)` | v1 非空返回 v1，否则 v2 |
| `COALESCE(v1, v2, ...)` | 返回第一个非空值 |
| `CASE WHEN ... THEN ... END` | 多条件分支 |

```sql
-- IF
SELECT name, IF(age >= 18, '成年', '未成年') AS status FROM user;

-- IFNULL
SELECT name, IFNULL(email, '未填写') AS email FROM user;

-- COALESCE（多个备选）
SELECT COALESCE(phone, email, '无联系方式') FROM user;

-- CASE WHEN
SELECT
    name,
    salary,
    CASE
        WHEN salary < 5000 THEN '低'
        WHEN salary < 10000 THEN '中'
        ELSE '高'
    END AS level
FROM employee;

-- CASE 等值判断
SELECT
    name,
    CASE dept_id
        WHEN 1 THEN '技术部'
        WHEN 2 THEN '市场部'
        WHEN 3 THEN '财务部'
        ELSE '其他'
    END AS dept_name
FROM user;
```

## 聚合函数

> 详见 [[dql#聚合函数]]

| 函数 | 说明 |
| ---- | ---- |
| `COUNT(列)` | 计数 |
| `SUM(列)` | 求和 |
| `AVG(列)` | 平均值 |
| `MAX(列)` | 最大值 |
| `MIN(列)` | 最小值 |

---

**相关笔记**: [[mysql]] · [[dql]]
