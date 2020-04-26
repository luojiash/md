# 《Java并发编程实战》

标签（空格分隔）： 未分类

---
p103 6.3.2 携带结果的任务Callable与Future
Runnable和Callable描述的都是抽象的计算任务。这些任务通常是有范围的，即都有
一个明确的起始点，并且最终会结束。Executor执行的任务有4个生命周期阶段:创建、提
交、开始和完成。由于有些任务可能要执行很长的时间，因此通常希望能够取消这些任务。在
Executor框架中，已提交但尚未开始的任务可以取消，但对于那些已经开始执行的任务，只有
当它们能响应中断时，才能取消。取消一个已经完成的任务不会有任何影响。
(Future的)get方法的行为取决于任务的状态(尚未开始、正在运行、已完成)。如果任务已经完成，
那么get会立即返回或者抛出一个Exception，如果任务没有完成，那么get将阻塞并直到任务
完成。如果任务抛出了异常，那么get将该异常封装为ExecutionException并重新抛出。如果
任务被取消，那么get将抛出CancellationException。如果get抛出了ExecutionException，那
么可以通过getCause来获得被封装的初始异常。
可以通过许多种方法创建一个Future来描述任务。ExecutorService中的所有submit方法
都将返回一个Future，从而将一个Runnable或Callable提交给Executor，并得到一个Future用
来获得任务的执行结果或者取消任务。还可以显式地为某个指定的Runnable或Callable实例化
一个FutureTask。(由于FutureTask实现了Runnable，因此可以将它提交给Executor来执行，
或者直接调用它的run方法。)



p72 5.2.1
> 只有当应用程序需要加锁Map以进行独占访问时，才应该放弃使用ConcurrentHashMap。

使用ConcurrentHashMap、HashTable、Collections.synchronizedMap的场景？

p234
当需要一些高级功能时才应该使用ReentrantLock，这些功能包括：可定时的、可轮询的与可中断的所获取操作，公平队列，以及非块结构的锁。否则，还是应该优先使用synchronized。



