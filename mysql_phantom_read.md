# MySQL幻读

## 幻读的定义

[MySQL :: MySQL 8.0 Reference Manual :: 15.7.4 Phantom Rows](https://dev.mysql.com/doc/refman/8.0/en/innodb-next-key-locking.html)

> The so-called phantom problem occurs within a transaction when the same query produces different sets of rows at different times. For example, if a SELECT is executed twice, but returns a row the second time that was not returned the first time, the row is a “phantom” row.

幻读指在一个事务中，后一次查询返回前一次查询没有的行。幻读是select产生的现象，和update无关。

## 分析

隔离级别：RR

分两种情况分析幻读：快照读和当前读。

普通的select语句是快照读，读取数据快照，不会加锁数据行。由可重复读的标准可知前后两次select一定会返回相同的数据行。

`select for update`属于当前读，当前读会读取数据的最新版本，并对数据加锁。SQL标准RR隔离级别下仍可能发生幻读，但MySQL实现和标准有所不同[^1]。MySQL通过间隙锁解决了幻读问题，间隙锁会锁住数据间隙，阻塞其他事务的在间隙的insert，从而保证两次查询返回相同的结果。

[^1]: [Innodb 中 RR 隔离级别能否防止幻读？ · Issue #42 · Yhzhtk/note · GitHub](https://github.com/Yhzhtk/note/issues/42)

