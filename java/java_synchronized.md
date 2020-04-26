# Java synchronized

synchronized 实现了可重入的内置锁，锁的状态保存在对象头，即每个对象都可以作为一个锁。
synchronized 修饰静态方法时，使用 Class 对象作为锁，独立于实例对象的锁。通过实例对象
调用 synchronized static 方法，也需要获得 Class 对象锁，所以一个线程获得 Class 对象锁
后，其他线程以哪种方式调用 synchronized static 方法都会阻塞。
