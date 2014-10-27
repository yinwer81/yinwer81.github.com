---
layout: post
title: "Java Thread状态及转换"
categories: multithread
---

内部枚举类java.lang.Thread.State中定义了线程的六种状态：

	NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING 和 TERMINATED

那么六种状态时如何转换的呢？先看Thread运行机制图（网上找的），再看详细文字解释：

![示例](/images/threadstate.jpg)

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。

