---
layout: post
title: "JVM线程状态及转换"
categories: multithread
---

内部枚举类java.lang.Thread.State中定义了如下六种线程状态：

	NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING 和 TERMINATED

那么六种状态时如何转换的呢？我们先看Thread运行机制图（暂借网图，后将重新画过）：

![示例](/images/ThreadMechanism.jpg)

JavaDoc的翻译如下：

**NEW**：已创建和初始化，但尚未启动的线程状态。 

**RUNNABLE**：可运行的线程状态。某一处于此状态的线程正在Java虚拟机中运行，但它可能正在等待操作系统中的其他资源，比如CPU。 

**BLOCKED**：等待监视器锁而受阻塞的线程状态。某一处于此状态的线程正在等待监视器锁以进入一个同步块/方法，或在调用Object.wait()后以重入同步块/方法。

**WAITING**：等待的线程状态。某一线程因为调用了不带超时值的Object.wait()，不带超时值的Thread.join()或LockSupport.park()而处于等待状态。例如，已经在某一对象上调用了 Object.wait()的线程正等待另一个线程，以便在该对象上调用Object.notify()或Object.notifyAll()；已经调用了Thread.join()的线程正在等待指定线程终止。 

**TIMED_WAITING**：定时等待的线程状态，但被指定了正等待时间。某一线程因为调用了Thread.sleep()或带有超时值的 Object.wait()或带有超时值的 Thread.join()或LockSupport.parkNanos()或LockSupport.parkUntil()而处于定时等待状态。

**TERMINATED**：已终止线程状态，线程已经结束执行。 
<br/>
<br/>
JavaDoc已经讲得很清楚了，线程状态只有以上六种，而Running并不是线程状态之一。更进一步说，在任一时间点，一个线程只能处于以上六种其一，但这些状态是JVM虚拟机状态，并不反映所有操作系统线程状态。 

于是，我用Statechart画了一下JVM线程状态及其转换图，如下（后面补图）：

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

