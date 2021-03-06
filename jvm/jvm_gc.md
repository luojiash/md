# Java 垃圾回收

- 引用计数法（不能解决循环依赖）
- 可达性分析

GC Roots：
1. 虚拟机栈中引用的对象
2. 本地方法栈中引用的对象
3. 方法区中静态变量引用的对象
4. 方法区中常量引用的对象


## 垃圾回收算法

### 标记-清除（Mark-Sweep）

先标记不可达的对象，回收这些对象占用的内存。
缺点：标记和清除效率都不高；容易产生内存碎片，内存使用率低。

### 标记-整理（Mark-Compact）

标记不可达的对象，移动可达对象使内存使用紧凑。
内存使用率高，但需要额外的内存复制操作。

### 复制算法（Copying）

分配两块内存区域，每次只使用其中一块。内存满后，将可达对象复制到另一块内存，回收旧内存区域。
只能使用一半的内存空间，内存使用率低。优点：实现简单，运行高效，无内存碎片。

### 分代收集算法

新生代对象存活时间短，GC频繁，使用复制算法。
老年代对象大，GC频率小，使用标记整理算法。

## 垃圾回收器

### Serial

新生代，单线程。
简单高效，没有线程交互的开销。

### ParNew

Serial的多线程版本。

### Parallel Scavenge

和ParNew类似，更关注吞吐量。

### Serial Old

Serial的老年代版本。

### Parallel Old

Parallel Scavenge的老年代版本。

### CMS(Concurrent Mark Sweep)

- 初始标记
- 并发标记
- 重新标记
- 并发清除

初始标记和重新标记仍需要Stop The World，但时间都很短。并发标记和并发清除可以和用户线程一起运行。

初始标记只标记根对象。并发标记完成了标记阶段的大部分工作，但因为和用户线程是并行执行的，会产生误标和漏标，所以需要STW进行重新标记，修复错误的标记。标记完成后，进行清除工作，清除可以和用户线程并行执行。

优点：停顿时间短。
缺点：1. 占用CPU资源；2. GC线程和用户线程并发执行，需要预留内存空间供用户线程使用（对象内存分配）。无法处理浮动垃圾，预留空间不足时，会启用Serial Old收集器，进行一次Full GC，导致停顿时间增长。3. 使用标记-清除算法，产生大量空间碎片。可设置参数进行内存碎片整理，但该过程无法并发，增长了停顿时间。

### G1(Garbage First)

G1物理内存不分代，而是使用逻辑分代。整个堆分为一个个大小相同的Region，分为三种类型：Eden、Survivor、Old，每种类型的Region不需要是连续的，且大小不是固定的，这和其他垃圾收集器的堆划分有较大区别。

1. 初始标记
2. 并发标记
3. 最终标记
4. 筛选回收

2阶段可以和用户线程并发执行，其余阶段会Stop The World。

优点：
1. G1使用标记整理和复制算法，不会产生很多内存碎片。
2. 分Region进行GC，每次只回收部分Region，保证了较短的停顿时间。
3. 停顿时间还增加了预测机制，实现可控的停顿时间。

缺点：
1. 并发收集占用CPU，降低吞吐量。
2. 无法处理浮动垃圾，并发期间老年代空间不足分配对象时，降级为Serial，进行Full GC，停顿时间大大提高。
3. 需要消耗更多内存，记录Remember Sets（每个区块都有，记录指向当前区块对象的指针，以实现区块单独进行GC）和Collection Sets（将要被回收的区块集合）。

### 收集器组合

CMS(Serial Old) + ParNew 关注最短停顿时间
Parallel Scavenge + Parallel Old 关注吞吐量

[Conflicting collector combinations in option list](http://bigdataer.net/?p=474)