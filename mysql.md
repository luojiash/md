#MySQL
tags: mysql

---
优先级：not > and > or

---
*2014年10月31日星期五*
使用exists 或in 代替表连接[^f1]：
```sql
mysql> select post.content from post join topic on post.topicid = topic.id;
+-----------------------------+
| content                     |
+-----------------------------+
| 个人                        |
| 围观                        |
| 规范方法                    |
+-----------------------------+
24 rows in set (0.00 sec)

mysql> select content from post where exists (select * from topic where topic.id=post.topicid);
+-----------------------------+
| content                     |
+-----------------------------+
| 个人                        |
| 围观                        |
| 规范方法                    |
+-----------------------------+
24 rows in set (0.00 sec)

mysql> select content from post where topicid in (select id from topic);
+-----------------------------+
| content                     |
+-----------------------------+
| 个人                        |
| 围观                        |
| 规范方法                    |
+-----------------------------+
24 rows in set (0.00 sec)
```
---
2015年3月23日星期一
mysql NULL与任何值比较的结果都是false
NULL = ‘’ FALSE
NULL != ‘’ FALSE
NULL = NULL FALSE
使用IFNULL 函数将NULL 值转为字符串，再和字符串比较即可产生正确结果：
IFNULL(NULL,'') = ''	TRUE


[^f1]: exists 与in 的区别

MySQL 数据类型

#字符串类型

包括char 和 varchar，注意字符串长度定义是字符数，不是字节数。

## varchar

使用变长空间存储字符串，仅适用必要的空间，所以比char节省空间。
varchar使用1或2个额外字节记录字符串长度，当字符串长度小于等于255字节时，
使用1个字节，否则使用2个字节。假如使用latin1字符集，varchar(10)需要
11字节存储空间，varchar(1000)需要1002字节。

varchar(5)和varchar(200)存储'hello'的空间开销是一样的，那使用短的列有什么优势呢？
> 事实证明有很大的优势。更长的列会消耗更多的内存，因为MySQL通常会分配固
定大小的内存块来保存内部值。尤其是使用内存临时表进行排序或操作时会特别糟
糕。在利用磁盘临时表进行排序时也同样糟糕。
《高性能MySQL》p117


## char

char使用定长存储字符串，适合存储固定长度的值，比如密码的MD5值。
更新频繁的列更适合使用char，不容易产生碎片。

## text

排序时只对最前的max_sort_length字节进行排序。
