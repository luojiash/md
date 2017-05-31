# Redis

---

## 数据结构

简单动态字符串（simple dynamic string, SDS）
: SDS保存了free/len/buf。
  获取字符串长度的复杂度为O(1)
  API是安全的，不会造成缓冲区溢出
  减少修改字符串长度带来的内存重分配次数
  二进制安全
  可以使用一部分<string.h>库中的函数

双向链表
: 

字典（dict）
: Redis数据库就是使用字典作为底层实现的。

跳跃表（skiplist）
: 支持平均O(logN)、最坏O(n)复杂度的节点查找。在一个跳跃表中，各个节点保存的成员对象
  必须是唯一的。Redis在两个地方用到了跳跃表：一个是实现有序集合键，另一个是在集群
  节点中用作内部数据结构。

整数集合（intset）
:

压缩列表（ziplist）
: 用于实现列表、有序集合、哈希。


## 对象

字符串：字符串
列表：压缩列表、双向列表
哈希：压缩列表、字典

集合
编码是整数集合（intset）或字典（hashtable）

有序集合（zset）
1. 使用压缩列表（ziplist）实现
2. 使用跳跃表（skiplist）和字典（dict）实现



## 命令行

集群模式连接Redis
redis-cli -p 7000 -c
`-c` Enable cluster mode (follow -ASK and -MOVED redirections).

> OBJECT ENCODING msg



Redis分布式锁
SET lock_key "locked" EX lock_timeout NX

NX表示当且仅当lock_key不存在才设置，EX设置超时时间
