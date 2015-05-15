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
