---
created: 2026-05-27
tags:
  - java/internship
  - database/mysql
related:
  - "[[sql/mysql]]"
  - "[[sql/ddl]]"
---

# DML — 数据操作

> **DML = Data Manipulation Language，操作表中数据的语言**

## 插入数据 INSERT

```sql
-- 全字段插入（按表字段顺序）
INSERT INTO user VALUES (1, '张三', 20, 'zhangsan@email.com', 1);

-- 指定字段插入
INSERT INTO user (name, age) VALUES ('李四', 22);

-- 批量插入
INSERT INTO user (name, age) VALUES ('王五', 23), ('赵六', 24), ('孙七', 25);
```

## 更新数据 UPDATE

```sql
-- 更新所有记录（慎用！）
UPDATE user SET age = 18;

-- 带条件更新
UPDATE user SET age = 21 WHERE name = '张三';

-- 更新多个字段
UPDATE user SET age = 21, email = 'zhangsan@new.com' WHERE name = '张三';
```

> ⚠️ **`UPDATE` 不加 `WHERE` 会更新整张表！**

## 删除数据 DELETE

```sql
-- 删除指定记录
DELETE FROM user WHERE id = 1;

-- 删除所有记录（表还在）
DELETE FROM user;

-- 清空表（效率更高，自增主键重置）
TRUNCATE TABLE user;
```

> ⚠️ **`DELETE` 不加 `WHERE` 会删除全表数据！**

## 区别对比

| 操作 | 关键字 | 可恢复 | 自增主键 |
| --- | --- | --- | --- |
| **条件删除** | `DELETE ... WHERE` | ✅ 事务内可回滚 | 不重置 |
| **全表删除** | `DELETE` | ✅ 事务内可回滚 | 不重置 |
| **清空表** | `TRUNCATE` | ❌ 不可回滚 | 重置 |

---

**相关笔记**: [[sql/mysql]] · [[sql/ddl]]
