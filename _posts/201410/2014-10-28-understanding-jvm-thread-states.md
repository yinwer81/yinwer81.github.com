---
layout: post
title: "JVM线程状态及转换"
categories: multithread
---

从Java 1.5以来，内部枚举类java.lang.Thread.State中定义了如下六个线程状态：

	NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING 和 TERMINATED

那么六个状态时如何转换的呢？我们先看Thread.State状态图（暂借网图，后将重新画过）：

![示例](/images/ThreadMechanism.jpg)

JavaDoc就不翻译了，我认为下面是更通俗和容易理解的解释：

**NEW**：用以标识**已创建和初始化但尚未启动的线程**。 

**RUNNABLE**：用以标识**可运行的线程**，对应操作系统中线程的就绪(Ready)和执行(Running)，如上图中所示。

**TERMINATED**：已终止线程状态，线程已经结束执行。 

OK，理解剩下的3个线程状态之前，我们得先温习几个基础概念：

1. 什么是Monitor？翻译过来监视器，Java中指监视器锁，


**BLOCKED**：等待监视器锁而受阻塞的线程状态。某一处于此状态的线程正在等待监视器锁以进入一个同步块/方法，或在调用Object.wait()后以重入同步块/方法。

**WAITING**：等待的线程状态。某一线程因为调用了不带超时值的Object.wait()，不带超时值的Thread.join()或LockSupport.park()而处于等待状态。例如，已经在某一对象上调用了 Object.wait()的线程正等待另一个线程，以便在该对象上调用Object.notify()或Object.notifyAll()；已经调用了Thread.join()的线程正在等待指定线程终止。 

**TIMED_WAITING**：定时等待的线程状态，但被指定了正等待时间。某一线程因为调用了Thread.sleep()或带有超时值的 Object.wait()或带有超时值的 Thread.join()或LockSupport.parkNanos()或LockSupport.parkUntil()而处于定时等待状态。


<br/>
<br/>
JavaDoc已经讲得很清楚了，线程状态只有以上六种，而Running并不是线程状态之一。更进一步说，在任一时间点，一个线程只能处于以上六种其一，但这些状态是JVM虚拟机状态，并不反映所有操作系统线程状态。 

于是，我用Statechart画了一下JVM线程状态及其转换图，如下（后面补图）：

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

