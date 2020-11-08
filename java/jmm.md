
### 重排序

1) 没有数据依赖的不同操作可以乱序执行（JMM 程序顺序规则使同一线程的操作不会乱序？）。
2) 线程将缓存刷新到主内存的时序不同，会使不同线程看到的操作执行顺序不同。

### Happens-Before

Java 内存模型为程序中所有的操作定义了一个偏序关系，称之为Happens-Before。[^1]

> 程序顺序规则。如果程序中操作A在操作B之前，那么在线程中A操作将在B操作之前执行。
>
> 监视器锁规则。在监视器锁上的解锁操作必须在同一监视器锁上的加锁操作之前执行。
>
> volatile变量规则。对 volatile 变量的写入操作必须在对该变量的读操作之前执行。
>
> 线程启动规则。在线程上对 Thread.Start 的调用必须在线程中执行任何操作之前执行。
>
> 线程结束规则。线程中的任何操作都必须在其他线程检测到该线程该线程已经结束之前执行，或者从 Thread.join 中成功返回，或者在调用 Thread.isAlive 时返回 false。
>
> 中断规则。当一个线程在另一个线程上调用 interrupt 时，必须在被中断线程检测到 interrupt 调用之前执行（通过抛出 InterruptedException，或者调用 isInterrupted 和 interrupted）。
>
> 终结器规则。对象的构造函数必须在启动该对象的终结器之前执行完成。
>
> 传递性。如果操作A在操作B之前执行，并且操作B在操作C之前执行，那么操作A必须在操作C之前执行。

Java 多线程相关的库大都依赖于 happens-before 规则。比如 CountDownLatch 使用了
AQS 的 volatile 变量 state 作为计数值，依赖 volatile 变量规则保证了 countdown
操作在 await 中返回之前执行。

FutureTask#outcome 保存了任务执行结果，该变量并没有使用 volatile 修饰，那怎么实现
可见性呢？`private Object outcome; // non-volatile, protected by state reads/writes`
从注释可知依赖于另一个 volatile 变量 state 实现可见性，分析源码对 outcome 的
操作，由程序顺序规则得到：write outcome -> write state，read state -> read outcome，
由 volatile 变量规则得到 write state -> read state，再由传递性得到 write outcome ->
read outcome，这就保证了 outcome 的可见性。FutureTask#callable 也应用了相同的规则
实现了可见性，有兴趣读源码分析下。

### final

final 域在 JMM 中有特殊的语义，final 域能确保初始化过程的安全性，访问 final 域时
不需要同步，前提是持有 final 域的对象被正确初始化（this 引用没有逸出）。例如以下代码[^2]
是线程安全的，多线程下不会得到未初始化完成的 SafeStates 对象，若 states 不是 final，
则可能得到未初始化完成的对象。可以理解为 final 域的写入和对象引用赋值给一个变量有
happens-before 的关系。

可见final和volatile实现了相同的内存语义。

```
public class SafeStates {
	private final Map<String, String> states;
	public SafeStates() {
		states = new HashMap<String, String>();
		states.put("alaska", "AK");
		states.put("alabama", "AL");
		states.put("wyoming", "WY");
	}
	public String getAbbreviation(String s) {
		return states.get(s);
	}
}
```

[^1]:《Java 并发编程实战》 16.1.3
[^2]:《Java 并发编程实战》 16.3