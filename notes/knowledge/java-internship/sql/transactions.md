---
created: 2026-05-27
tags:
  - java/internship
  - database/mysql
related:
  - "[[mysql]]"
  - "[[dml]]"
---

# MySQL 事务

> **事务 = 一组不可分割的 SQL 操作，要么全部成功，要么全部失败**

## 事务操作

```sql
-- 方式一：手动控制（推荐）
START TRANSACTION;          -- 开启事务（或 BEGIN）

UPDATE account SET balance = balance - 1000 WHERE id = 1;
UPDATE account SET balance = balance + 1000 WHERE id = 2;

COMMIT;                     -- 提交事务（确认修改）
-- 或
ROLLBACK;                   -- 回滚事务（撤销修改）

-- 方式二：设置自动提交
SELECT @@autocommit;        -- 1=自动提交（默认），0=手动提交
SET autocommit = 0;         -- 关闭自动提交（当前会话生效）
```

### 事务示例

```sql
-- 准备数据
CREATE TABLE account (
    id      INT PRIMARY KEY AUTO_INCREMENT,
    name    VARCHAR(20),
    balance INT
);

INSERT INTO account VALUES
    (1, '张三', 5000),
    (2, '李四', 5000);

-- 转账操作（事务保护）
START TRANSACTION;

-- 扣钱
UPDATE account SET balance = balance - 1000 WHERE id = 1;

-- 出错了（比如服务器断电），执行 ROLLBACK → 张三的钱没少
-- 一切正常，执行 COMMIT → 两个 update 都生效
UPDATE account SET balance = balance + 1000 WHERE id = 2;

COMMIT;
```

> 没有事务的后果：扣钱成功但加钱失败 → 钱凭空消失了

## ACID 四大特性

| 特性 | 英文 | 说明 | 比喻 |
| ---- | ---- | ---- | ---- |
| **原子性** | Atomicity | 事务中的操作要么全做要么全不做 | 转账扣钱+加钱是一体的 |
| **一致性** | Consistency | 事务前后数据总量不变 | 转出1000，对方就+1000 |
| **隔离性** | Isolation | 并发事务互不干扰 | 两个人同时转账不影响 |
| **持久性** | Durability | 提交后数据永久保存 | 重启数据还在 |

> **ACID**：AID 是手段，C（一致性）是最终目的

## 并发事务问题

| 问题 | 说明 | 后果 |
| ---- | ---- | ---- |
| **脏读** | 读到另一个事务**未提交**的数据 | 读到脏数据，对方可能回滚 |
| **不可重复读** | 同一个事务内两次读同一行数据，结果不同 | 被其他事务 **UPDATE** 了 |
| **幻读** | 同一个事务内两次查询结果集行数不同 | 被其他事务 **INSERT/DELETE** 了 |

```sql
-- 脏读示例（隔离级别太低时）
-- 事务A：BEGIN; UPDATE account SET balance = 9000 WHERE id = 1;  -- 未提交
-- 事务B：SELECT balance FROM account WHERE id = 1;              -- 读到9000（脏数据）
-- 事务A：ROLLBACK;                                              -- 实际上没改成功
```

## 隔离级别

| 级别 | 名称 | 脏读 | 不可重复读 | 幻读 |
| ---- | ---- | :--: | :--------: | :--: |
| `READ UNCOMMITTED` | 读未提交 | ❌ | ❌ | ❌ |
| `READ COMMITTED` | 读已提交 | ✅ | ❌ | ❌ |
| `REPEATABLE READ` | 可重复读 | ✅ | ✅ | ❌ |
| `SERIALIZABLE` | 串行化 | ✅ | ✅ | ✅ |

> ✅ = 解决了该问题

```sql
-- 查看当前隔离级别
SELECT @@transaction_isolation;     -- MySQL 8.0 默认 REPEATABLE READ
SELECT @@tx_isolation;              -- MySQL 5.x

-- 设置隔离级别（当前会话）
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- 设置隔离级别（全局）
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### 隔离级别详解

#### READ UNCOMMITTED（读未提交）

**什么都不防，性能最好但基本不用**

```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

> 能读到别人未提交的数据 → 脏读，生产环境**禁止使用**

#### READ COMMITTED（读已提交）

**只能读到已提交的数据，解决脏读**

> Oracle、SQL Server 的默认级别  
> 但同一个事务内两次读可能结果不同 → 不可重复读

#### REPEATABLE READ（可重复读）

**MySQL 默认级别，保证同一个事务内多次读结果一致**

```sql
-- 事务A
BEGIN;
SELECT * FROM account WHERE id = 1;  -- 读到 balance = 5000

-- 此时事务B 把 id=1 的 balance 改成了 8000 并提交

SELECT * FROM account WHERE id = 1;  -- 仍然读到 balance = 5000（可重复读）
COMMIT;
```

> MySQL 的 `REPEATABLE READ` 通过 **MVCC（多版本并发控制）** 实现  
> 注意：InnoDB 的 RR 级别通过 **间隙锁（Gap Lock）** 解决了幻读问题

#### SERIALIZABLE（串行化）

**所有事务串行执行，性能最差**

```sql
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

> 读操作也会加锁，并发性能极低，**除非数据一致性要求极高否则不用**

## MVCC（多版本并发控制）

**MySQL 实现可重复读和已提交读的核心机制**

```
每行数据有多个"快照版本"，事务只能看到"应该看到"的版本
         ↓
READ COMMITTED：每次 SELECT 都生成新的快照
REPEATABLE READ：事务第一次 SELECT 时生成快照，后续都用同一个
```

> MVCC 让读操作**不加锁**也能保证隔离性，大幅提升并发性能

## 锁机制

### 行锁 vs 表锁

| 锁类型 | 说明 | 使用 |
| ------ | ---- | ---- |
| **行锁** | 只锁住某一行 | InnoDB 默认 |
| **表锁** | 锁住整张表 | MyISAM、手动锁表 |

```sql
-- 手动加锁
LOCK TABLES account READ;     -- 读锁（其他人只能读不能写）
LOCK TABLES account WRITE;    -- 写锁（只有自己能读写）
UNLOCK TABLES;                -- 解锁

-- 行锁（InnoDB 自动管理，不需要手动加）
-- SELECT 后面自动加行锁
SELECT * FROM account WHERE id = 1 FOR UPDATE;    -- 排他锁（别人不能读写这行）
SELECT * FROM account WHERE id = 1 LOCK IN SHARE MODE;  -- 共享锁（别人能读不能写）
```

### 死锁

```sql
-- 事务A：锁了 id=1，想锁 id=2
BEGIN;
UPDATE account SET balance = 100 WHERE id = 1;
UPDATE account SET balance = 200 WHERE id = 2;  -- 等待事务B释放id=2
COMMIT;

-- 事务B：锁了 id=2，想锁 id=1
BEGIN;
UPDATE account SET balance = 300 WHERE id = 2;
UPDATE account SET balance = 400 WHERE id = 1;  -- 等待事务A释放id=1
COMMIT;
```

> **预防死锁**：所有事务按**相同的顺序**访问资源（比如都先 id=1 再 id=2）

## 保存点 SAVEPOINT

**回滚到事务中的某个点，而不是全部回滚**

```sql
START TRANSACTION;

INSERT INTO log VALUES ('step 1');
SAVEPOINT sp1;

INSERT INTO log VALUES ('step 2');
SAVEPOINT sp2;

INSERT INTO log VALUES ('step 3');

ROLLBACK TO sp2;   -- 回退到 sp2，step 3 被撤销
-- 或
ROLLBACK TO sp1;   -- 回退到 sp1，step 2 和 step 3 都被撤销

COMMIT;
```

## 事务使用建议

| 场景 | 建议 |
| ---- | ---- |
| 一般业务 | 使用 MySQL 默认 `REPEATABLE READ` |
| 跨系统/高并发 | 考虑 `READ COMMITTED`（减少间隙锁竞争） |
| 银行/金融 | 保持 `REPEATABLE READ` + 代码层面控制 |
| 事务尽量短 | 减少锁持有时间，提高并发 |
| 避免事务中交互 | 不要在事务中等待用户输入 |
| 统一资源访问顺序 | 避免死锁 |

---

**相关笔记**: [[mysql]] · [[dml]] · [[dql]]
