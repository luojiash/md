# Java AbstractQueuedSynchronizer

### 实现原理

AQS 使用了两个队列，sync queue 和 condition queue，独占锁和共享锁都会使用 sync queue，condition queue 只有独占锁才会使用。

两个队列的元素都用内部类 Node 表示，根据 waitStatus 的值确定一个节点是在哪个队列，conditionqueue 节点的 waitStatus 只能是0（初始状态）或-2（CONDITION）。

sync queue 是个双向链表，使用 Node 的 prev 和 next 字段连接节点，nextWaiter 标识节点是共享还是独占模式（isShared 方法判断）。condition queue 是单向链表，使用 nextWaiter 字段来连接节点。nextWaiter 没有用 volatile 修饰，因为操作 condition queue 时一定已经获取了独占锁，不需要支持并发。

```
/**
 * Returns true if node is waiting in shared mode.
 */
final boolean isShared() {
    return nextWaiter == SHARED;
}
```

### 获取独占锁

调用 acquire、acquireInterruptibly、tryAcquireNanos 获取独占锁，这些方法都调用了 tryAcquire（由子类实现）。

调用 release 释放独占锁，该方法调用了 tryRelease（由子类实现）。

### 获取共享锁

调用方法 acquireShared、acquireSharedInterruptibly、tryAcquireSharedNanos 获取共享锁，这些方法都调用了 tryAcquireShared（由子类实现）。

调用 releaseShared 释放共享锁，该方法调用了 tryReleaseShared（由子类实现）。

### ReentrantLock

ReentrantLock 是 AQS 的一个应用实例，它实现和公平锁和非公平锁。AQS 的实现使用了一个 FIFO 的队列，使用队列的特性可以实现公平的排队等待，在获取锁之前先判断队列中是否已经有线程在等待，若有则入队等待。被唤醒再次尝试获取锁时，同样要判断在当前节点之前是否还有其他节点在排队。

非公平锁则使用抢占的方式获取锁，即先使用 CAS 尝试获取锁，失败后再走常规的获取锁流程。再次尝试获取锁依然使用抢占的方式，不需要判断当前节点是否队列的第一个节点。