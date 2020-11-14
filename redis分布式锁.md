# Redis分布式锁

`SET key value NX EX seconds`这条命令用于获取一个分布式锁，`key`是要访问资源的唯一名称，`value`是一个随机字符串，`NX`表示`key`不存在时才能设置成功，`EX seconds`表示这个锁的自动失效时间。

如何释放锁？很容易想到通过调用`DEL key`释放锁，其实这么做是不安全的，考虑下面的执行序列：
1. client 1获得锁L
2. client 1因为某些原因阻塞了很长一段时间
3. 锁L超时自动失效
4. client 2获得锁L
5. client 1从阻塞中恢复，释放了client 2持有的锁L

这个执行序列中client 1错误地释放了client 2持有的锁，所以需要一种机制保证客户端只释放自己持有的锁。
可以利用Redis lua脚本执行的原子性来实现我们的需求，在脚本里判断`value`是否当前客户端生成的，如果是再释放锁。
所以保证`value`的唯一性就非常重要了，使用固定字符串作为`value`是个错误的做法。
从[基于Redis的分布式锁到底安全吗（上）？ - 铁蕾的个人博客](http://zhangtielei.com/posts/blog-redlock-reasoning.html)
复制一段代码：

```
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```
lua脚本使用`EVAL`命令执行，`KEYS`、`ARGV`是内置变量，具体用法参考文档。