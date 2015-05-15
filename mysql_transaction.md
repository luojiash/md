#MySQL
tags: mysql 事务

---
```sql
#查询事务隔离级别
SELECT @@global.tx_isolation;
SELECT @@session.tx_isolation;
SELECT @@tx_isolation;

#修改隔离级别
SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE}
```

| 隔离级别 | 脏读（Dirty Read） | 不可重复读（NonRepeatable Read） | 幻读（Phantom Read） |
|-|-|-|-|
| 未提交读（Read uncommitted）| 可能   | 可能   | 可能 |
| 已提交读（Read committed）  | 不可能 | 可能   | 可能 |
| 可重复读（Repeatable read） | 不可能 | 不可能 | 可能 |
| 可串行化（Serializable）    | 不可能 | 不可能 | 不可能 |

###1、脏读
事务A 访问了事务B 未提交的修改。

###2、不可重复读
一个事务里两次读取的数据不一致，因为两次读取中间另一个事务修改了数据。

在可重复读的隔离级别下，使用以下语句（加锁）仍能查到其他事务提交的修改，称为当前读（current read）；普通的读取方式是读取历史数据，称为快照读（snapshot read）。
```sql
SELECT * FROM person LOCK IN SHARE MODE;
```

###3、幻读
事务B 修改了数据并提交，事务A 看不到这些修改（select 查不到），但修改实际已经生效了。[^f1]比如事务B 插入一条数据并提交，事务A update 所有数据，就会多了事务B 刚插入的一条数据，而之前是没有这条数据的。再比如事务B 插入一条数据并提交，事务A 插入相同的数据，则会出现Duplicate entry 错误（假设有主键）。


---
参考资料：
[MySQL 四种事务隔离级的说明 - jyzhou - 博客园](http://www.cnblogs.com/zhoujinyi/p/3437475.html)
[Innodb中的事务隔离级别和锁的关系 - 美团技术团队](http://tech.meituan.com/innodb-lock.html)


[^f1]: 理解仍有误差，仅参考。