---
layout: post
title: "理解Java的4种引用类型"
categories: programming
---
Java中的4种引用类型，中文名为强引用，软引用，弱引用以及虚引用，虚引用也就是传说中的幽灵引用。
![示例](/images/fourReferences.png)

Java的4种引用类型与GC有着密切的关系，接下来逐一来看看其定义和使用场景。

StrongReference是Java的默认引用，它尽可能长时间的存活于JVM堆中，当没有任何对象指向它时，GC后会被回收。JVM采用Finalizer来管理每个强引用对象，将其被标记要清理时加入ReferenceQueue后逐一调用该对象的finalize()方法。

SoftReference, WeakReference和PhantomReference继承自Reference类。当垃圾回收器正在考察的对象只能通过某个Reference才`可获得`时，它们为垃圾回收器提供了不同级别的间接指示。 

对象是`可获得的(reachable)`指该对象在程序中某处被直接或间接的引用了。如果一个对象是`可获得的`，垃圾回收器就不能释放它，因为它仍在程序中使用。如果一个对象不是`可获得的`，表示程序中已经不存在对该对象的引用，这时将其回收是安全的。 

SoftReference, WeakReference和PhantomReference由强到弱对应不同级别的`可获得性`：SoftReference用于实现内存敏感的高速缓存；而WeakReference用于实现`规范映射`而设计，`规范映射`中对象的实例可以在程序的多处使用以节省空间。

SoftReference是只在内存紧张时才收集软可及对象。所以SoftReference比WeakHashMap采用的弱引用策略更适合于实现缓存机制。WeakReference适用于`占有大量内存，但通过垃圾回收功能回收以后很容易重新创建`的场景。

我们说，HashMap采用的是类似强引用的强键来管理，也就是说即使作为key的对象已经没有被任何对象引用了，它仍然会保存在 HashMap中，在某些情况（如内存缓存）下，这些过期的对象会造成内存泄露。WeakHashMap采取的策略是，只要作为key的对象已经超出了生命周期，就不会阻止垃圾回收器对该对象的处理，即使在内存并不紧张的情况下。不过因为GC是一个优先级很低的线程，所以不能保证会立刻被清除。

测试以下代码试试看：
{% highlight java %}
package com.dunkcoder.test;

import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertNotNull;
import static org.junit.Assert.assertNull;
import static org.junit.Assert.assertSame;
import static org.junit.Assert.assertTrue;

import java.lang.ref.PhantomReference;
import java.lang.ref.Reference;
import java.lang.ref.ReferenceQueue;
import java.lang.ref.SoftReference;
import java.lang.ref.WeakReference;
import java.util.Map;
import java.util.WeakHashMap;

import org.junit.Test;

public class ReferenceTest {

	class BigObject {

		private static final int SIZE = 100000;
		@SuppressWarnings("unused")
		private double[] dblArr = new double[SIZE];
		private String name;

		public BigObject(String name) {
			this.name = name;
		}

		public BigObject(String name, int size) {
			this.name = name;
			dblArr = new double[size];
		}

		public String toString() {
			return this.name;
		}

		// 覆盖finalize()函数，在finalize()中输出打印信息，方便追踪
		protected void finalize() {
			System.out.println("Finalizing " + this.name);
		}
	}

	@Test
	public void strongReferenceNotGc() {

		Object referent = new BigObject("strongReferenceNotGC");
		// StrongReference
		Object strongReference = referent;
		assertSame(referent, strongReference);

		referent = null;
		System.gc();

		// GC后不会被回收
		assertNotNull(strongReference);
	}

	@Test
	public void strongReferenceGc() {

		Object referent = new BigObject("strongReferenceGC");
		referent = null;
		System.gc();

		// GC后被回收
		assertNull(referent);
	}

	@Test
	public void weakReference() {
		Object referent = new BigObject("weakReference");
		WeakReference<Object> weakReference = new WeakReference<Object>(referent);

		assertSame(referent, weakReference.get());

		referent = null;
		System.gc();

		// 被包装成WeakReference的对象，在没有强引用的时候，GC会将它们标记为可以被回收，在被回收之前会执行finalize()方法  
		// 应用场景：缓存，key一旦失去了其他的引用，缓存里的东西就可以随时消失
		assertNull(weakReference.get());
	}

	@Test
	public void weakHashMap() throws InterruptedException {
		Map<Object, Object> weakHashMap = new WeakHashMap<Object, Object>();
		Object key = new BigObject("weakHashMap key");
		Object value = new Object();
		weakHashMap.put(key, value);

		assertTrue(weakHashMap.containsValue(value));

		key = null;
		System.gc();

		// 等待无效entries进入ReferenceQueue，以便WeakHashMap.getTable()时被清理
		Thread.sleep(1000);

		// key设置为null后，相关的entries在GC后被回收
		assertFalse(weakHashMap.containsValue(value));
	}

	@Test
	public void softReference() {

		SoftReference<Object> softRerference = new SoftReference<Object>(new BigObject("softReference"));
		assertNotNull(softRerference.get());

		System.gc();
		assertNotNull(softRerference.get());

		// OutOfMemory ...
		int i = 0;
		while (true) {
			try {
				++i;
				new BigObject("OutOfMemory", 100000000);
			} catch (Throwable e) {
				System.out.println("OutOfMemory after " + i + " times");
				e.printStackTrace();
				break;
			}
		}

		// SoftReference只有在OutOfMemory之前才会被回收，即在OOM前也就不会执行finalize()方法
		assertNull(softRerference.get());
	}

	@Test
	public void phantomReferenceAlwaysNull() {
		// PhantomReference唯一的用处就是跟踪referent何时被入队(enqueue)到ReferenceQueue中
		ReferenceQueue<Object> queue = new ReferenceQueue<Object>();
		PhantomReference<Object> phantomReference = new PhantomReference<Object>(new BigObject("phantomReferenceAlwaysNull"), queue);

		assertNull(phantomReference.get());

		assertNull(queue.poll());
		System.gc();
		assertNull(queue.poll());
	}

	@Test
	public void referenceQueueWithWeakReference() throws InterruptedException {
		Object referent = new BigObject("referenceQueueWithWeakReference");
		ReferenceQueue<Object> referenceQueue = new ReferenceQueue<Object>();
		Reference<Object> ref = new WeakReference<Object>(referent, referenceQueue);

		assertFalse(ref.isEnqueued());
		Reference<? extends Object> polled = referenceQueue.poll();
		assertNull(polled);

		referent = null;
		System.gc();

		assertTrue(ref.isEnqueued());
		Reference<? extends Object> removed = referenceQueue.remove();
		assertNotNull(removed);
		assertSame(ref, removed);
		assertNull(removed.get());
	}

	@Test
	public void referenceQueueWithSoftReference() throws InterruptedException {
		Object referent = new BigObject("referenceQueueWithWeakReference");
		ReferenceQueue<Object> referenceQueue = new ReferenceQueue<Object>();
		Reference<Object> ref = new SoftReference<Object>(referent, referenceQueue);

		assertFalse(ref.isEnqueued());
		Reference<? extends Object> polled = referenceQueue.poll();
		assertNull(polled);

		referent = null;
		// OutOfMemory ...
		int i = 0;
		while (true) {
			try {
				++i;
				new BigObject("OutOfMemory", 100000000);
			} catch (Throwable e) {
				System.out.println("OutOfMemory after " + i + " times");
				e.printStackTrace();
				break;
			}
		}
		assertTrue(ref.isEnqueued());
		Reference<? extends Object> removed = referenceQueue.remove();
		assertNotNull(removed);
		assertSame(ref, removed);
		assertNull(removed.get());
	}

}
{% endhighlight %}
以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。