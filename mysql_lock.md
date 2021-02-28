# MySQL 锁

## 基本锁

- shared lock（S lock，共享锁）
- exclusive lock（X lock，排他锁）

## 行锁

### record lock（记录锁）

锁住一条记录。

### gap lock（间隙锁）

锁住索引区间（开区间），在RR（Repeatable Read）隔离级别下才会加gap lock，解决幻读问题。

### next-key lock

record lock和gap lock的结合，左开右闭。

### insert intention lock（插入意向锁）

插入意向锁是一种特殊的间隙锁。多个事务插入同一间隙的不同值时，会获取插入意向锁。插入意向锁之间是兼容的，因而不需要等待，从而提高并发插入的效率。

## 快照读/当前读

快照读：读取记录的可见版本（可能是历史版本），不用加锁，简单的select是快照读。

当前读：读取记录的最新版本，记录会加锁。`select ... lock in share mode`, `select ... for update`, `insert`, `update`, `delete` 都属于当前读，第一条语句加S锁，其他都是加X锁。

## 死锁诊断

查看数据库锁：`select * from information_schema.innodb_locks;`

查看最近一次死锁日志：`SHOW ENGINE INNODB STATUS;`

## 加锁实例分析

```
CREATE TABLE `t` (
  `id` int(11) NOT NULL,
  `c` int(11) DEFAULT NULL,
  `d` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `c` (`c`)
) ENGINE=InnoDB;

INSERT INTO `t` VALUES (0, 0, 0),(5, 5, 5),(10, 10, 10),(15, 15, 15),(20, 20, 20),(25, 25, 25);
```

### 间隙锁

| session1                                      | session2                                            | session3                                               |
| --------------------------------------------- | --------------------------------------------------- | ------------------------------------------------------ |
| begin; select * from t where c=12 for update; |                                                     |                                                        |
|                                               | begin; insert into t values(11,10,100); (**block**) |                                                        |
|                                               |                                                     | begin; insert into t values(9,10,100); (**not block**) |

**分析间隙锁时一定要考虑到二级索引包含了主键字段，数据行间隙由二级索引字段和主键字段一起组成。**

索引`c`的排序结构如下表。session1加间隙锁，锁住id=10和id=15两行数据之间的间隙。session2插入数据在间隙中，所以被阻塞，session3插入数据在id=10左侧，所以不会阻塞。

| c    | 0    | 5    | *10* | *15* | 20   | 25   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| id   | 0    | 5    | *10* | *15* | 20   | 25   |

### 非唯一索引等值查询

| session1                                                | session2                                      | session3                                 |
| ------------------------------------------------------- | --------------------------------------------- | ---------------------------------------- |
| begin; select id from t where c = 5 lock in share mode; |                                               |                                          |
|                                                         | update t set d=d+1 where id=5; (**Query OK**) |                                          |
|                                                         |                                               | insert into t values(7,7,7); (**block**) |

1. **加锁的基本单位是next-key lock**，session1加next-key lock(0,5]。
2. **等值查询，向右第一个不满足条件记录，next-key lock退化为间隙锁**，即(5,10]退化为(5,10)，session3被该间隙锁阻塞。
3. **只有访问到的对象才会加锁**，session1使用了覆盖索引，不会访问主键索引，所以主键索引上不会加锁，故session2不会阻塞。如果使用for update，系统认为你要更新数据，因此会给主键索引上满足条件的行加上行锁。

### 行锁（非唯一索引范围查询）

| session1                                                | session2                                          |
| ------------------------------------------------------- | ------------------------------------------------- |
| begin; select * from t where c>=10 and c<11 for update; |                                                   |
|                                                         | begin; update t set d=d+1 where c=15; (**block**) |

session1**从左向右扫描，第一条不符合条件的记录仍会被加行锁，但其右间隙不加锁**，所以session1加间隙锁`(5,10)(10,15)`，行锁`10 15`。session2阻塞于session1对`15`的行锁。

| session1                                                     | session2                                          |
| ------------------------------------------------------------ | ------------------------------------------------- |
| begin; select * from t where c>=15 and c<20 order by c desc for update; |                                                   |
|                                                              | begin; update t set d=d+1 where c=10; (**block**) |

session1**从右向左扫描，第一条不符合条件的记录仍会被加锁，同时其左间隙也会加锁**，所以session1加锁`(5,10)(10,15)(15,20)`，行锁`10 15`。session2阻塞于session1对`10`的行锁。

## 参考资料

- [Innodb中的事务隔离级别和锁的关系 - 美团技术团队](https://tech.meituan.com/2014/08/20/innodb-lock.html)

- [mysql insert锁机制](http://yeshaoting.cn/article/database/mysql%20insert%E9%94%81%E6%9C%BA%E5%88%B6/)

- [何登成的技术博客 » MySQL 加锁处理分析](http://hedengcheng.com/?p=771)

- 21讲为什么我只改一行的语句，锁这么多

