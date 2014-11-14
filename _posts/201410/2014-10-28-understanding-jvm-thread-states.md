---
layout: post
title: "JVM线程状态及转换"
categories: concurrency
---

从Java 1.5以来，内部枚举类java.lang.Thread.State中定义了如下六个线程状态：

	NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING 和 TERMINATED

那么六个状态时如何转换的呢？借用线程状态图如下：

![picture](/images/thread states.gif)

JavaDoc就不翻译了，下面是更通俗和容易理解的解释：

**NEW**：用以标识**已创建和初始化但尚未启动的线程**。 

**RUNNABLE**：用以标识**可运行的线程**，对应操作系统(OS)中线程的就绪(Ready)和执行(Running)，OS中线程调度(就绪和执行)不在此文范围，如上图中所示。

**TERMINATED**：已终止线程状态，线程已经结束执行。 

**BLOCKED**：用以标识**等待监视器锁而被阻塞的线程**，JavaDoc说：某一处于此状态的线程正在等待监视器锁以进入一个同步块/方法，或在调用Object.wait()后以重入同步块/方法。很难理解吧，那就对了。在继续理解前，得先搞清楚什么是Monitor(监视器)。

在Java并发编程中，Monitor机制用以实现线程之间的互斥与协作，它保证了在某一时刻，对一个指定对象实例的某一同步方法只能由一个线程来调用，注意：每一个实例对象都关联一个Monitor。借用Java Monitor图如下：

![picture](/images/thread monitor.png)

从图中可以看出，某个对象实例的Monitor在某个时刻，只能被一个线程拥有，该线程就是`Active Thread`，而其它线程都是`Waiting Thread`，分别在两个队列`Entry Set`和`Wait Set`里面等候。

在`Entry Set`中等待的线程状态是`Waiting for monitor entry`，说明此线程通过`synchronized(obj) {……}`进入了临界区，从而进入了图中的`Entry Set`队列，但该obj对应的Monitor被其他线程拥有，所以线程在`Entry Set`队列中等待。

`Wait Set`中等待的线程状态就是`in Object.wait()`，当线程获得了Monitor进入了临界区后，如果发现线程继续运行的条件没有满足，它则调用对象(一般就是被synchronized的对象)的wait()方法，放弃Monitor进入`Wait Set`队列。只有当别的线程在该对象上调用了notify()或者notifyAll()，`Wait Set`队列中线程才得到机会去竞争，但是只有一个线程获得对象的Monitor。

所以，线程只有拥有了实例对象的Monitor才可以调用`wait(),notify(),notifyAll()`等方法，这也是为什么`wait(),notify(),notifyAll()`等方法必须写在同步块中的原因，否则会报IllegalMonitorStateException。

**WAITING**：等待的线程状态。某一线程因为调用了：
>* 不带超时值的Object.wait()
>* 不带超时值的Thread.join()
>* LockSupport.park()

而处于等待状态。例如，已经在某一实例对象上调用了`Object.wait()`的线程或已经调用了Thread.join()的线程正在等待指定线程终止。 

**TIMED_WAITING**：定时等待的线程状态，但被指定了正等待时间。某一线程因为调用了：
>* Thread.sleep()
>* 带有超时值的Object.wait()
>* 带有超时值的Thread.join()
>* LockSupport.parkNanos()
>* LockSupport.parkUntil()

而处于定时等待状态。

线程BLOCKED和WAITING状态的区别，借用[Difference between lock state WAIT and lock state BLOCKED](http://stackoverflow.com/questions/15680422/difference-between-lock-state-wait-and-lock-state-blocked)，简单明了不译：

In the BLOCKED state, a thread is about to enter a synchronized block, but there is another thread currently running inside a synchronized block on the same object. The first thread must then wait for the second thread to exit its block.

In the WAITING state, a thread is waiting for a signal from another thread. This happens typically by calling Object.wait(), or Thread.join(). The thread will then remain in this state until another thread calls Object.notify(), or dies.

还可以参考网文[Synchronizing Threads with Java Monitors](http://www.csc.villanova.edu/~mdamian/threads/javamonitors.html)学习。

就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
