---
created: 2026-05-27
tags:
  - java/internship
  - database/mysql
related:
  - "[[sql/mysql]]"
  - "[[sql/ddl]]"
  - "[[sql/dml]]"
  - "[[sql/dql]]"
---

# DCL — 权限控制

> **DCL = Data Control Language，控制数据库的访问权限**

## 用户管理

```sql
-- 查询所有用户
SELECT user, host FROM mysql.user;

-- 创建用户
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';

-- 删除用户
DROP USER 'username'@'localhost';

-- 修改密码
ALTER USER 'username'@'localhost' IDENTIFIED BY 'newpassword';
```

> `'username'@'localhost'` — `localhost` 表示只允许本地登录
> `'username'@'%'` — `%` 表示允许任意主机远程登录

## 权限授权 GRANT

```sql
-- 授予指定权限
GRANT SELECT ON db_name.* TO 'username'@'localhost';

-- 授予多个权限
GRANT SELECT, INSERT, UPDATE, DELETE ON db_name.* TO 'username'@'localhost';

-- 授予所有权限（除 GRANT OPTION 外）
GRANT ALL PRIVILEGES ON db_name.* TO 'username'@'localhost';

-- 授予所有数据库的所有权限（管理员权限，慎用）
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost';

-- 允许该用户将权限转授给其他人
GRANT ALL PRIVILEGES ON db_name.* TO 'username'@'localhost' WITH GRANT OPTION;
```

## 权限回收 REVOKE

```sql
-- 回收指定权限
REVOKE SELECT ON db_name.* FROM 'username'@'localhost';

-- 回收所有权限
REVOKE ALL PRIVILEGES ON db_name.* FROM 'username'@'localhost';

-- 回收转授权限
REVOKE GRANT OPTION ON db_name.* FROM 'username'@'localhost';
```

## 刷新权限

```sql
-- 修改权限后立即生效
FLUSH PRIVILEGES;
```

## 查看权限

```sql
-- 查看当前用户权限
SHOW GRANTS;

-- 查看指定用户权限
SHOW GRANTS FOR 'username'@'localhost';
```

## 常用权限

| 权限 | 说明 |
| --- | --- |
| `CREATE` | 创建数据库/表 |
| `DROP` | 删除数据库/表 |
| `ALTER` | 修改表结构 |
| `SELECT` | 查询数据 |
| `INSERT` | 插入数据 |
| `UPDATE` | 更新数据 |
| `DELETE` | 删除数据 |
| `INDEX` | 创建/删除索引 |
| `CREATE VIEW` | 创建视图 |
| `CREATE USER` | 创建用户 |
| `ALL PRIVILEGES` | 所有权限（除 GRANT OPTION） |

## 常用示例

```sql
-- 创建只读用户
CREATE USER 'reader'@'%' IDENTIFIED BY 'read123';
GRANT SELECT ON mydb.* TO 'reader'@'%';
FLUSH PRIVILEGES;

-- 创建读写用户
CREATE USER 'writer'@'%' IDENTIFIED BY 'write123';
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'writer'@'%';
FLUSH PRIVILEGES;

-- 创建管理员
CREATE USER 'admin'@'localhost' IDENTIFIED BY 'admin123';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

---

**相关笔记**: [[sql/mysql]] · [[sql/ddl]] · [[sql/dml]] · [[sql/dql]]
