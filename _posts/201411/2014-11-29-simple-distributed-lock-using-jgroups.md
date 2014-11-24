---
layout: post
title: "Simple distributed lock using jgroups"
categories: distributed
---
我们在做分布式(多JVM实例)/集群应用设计时，通常会尽量避免分布式锁，但在某些需求场景中，还是会存在对它的需求，并且使用分布式锁需要考虑的问题更多，例如怎样保证某个进程在持有锁时意外终止后，其它进程也能够正常地获得锁等。

Java中的关键字synchronized，Singleton单例以及concurrent locks并发锁等并不能满足。我们一般会引入`第三者`实现分布式锁服务，如zookeeper, redis, memcached等。

这里使用简单轻量的JGroups(可靠性消息传输的工具包)实现一个分布式锁，从[JGroups主页](http://www.jgroups.org/)可以了解到更多详细信息。

首先，定义Lock接口，最基本的两个方法：
{% highlight java %}
package com.dunkcoder;

public interface Lock {
	void lock(String name);

	void release(String name);
}
{% endhighlight %}

接着，是JGroups Protocol Stacks协议栈，默认UDP：
{% highlight xml %}
<config xmlns="urn:org:jgroups" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="urn:org:jgroups http://www.jgroups.org/schema/JGroups-3.3.xsd">
	<UDP mcast_port="${jgroups.udp.mcast_port:12345}" receive_on_all_interfaces="true" />
	<PING />
	<MERGE2 />
	<FD_SOCK />
	<FD_ALL />
	<VERIFY_SUSPECT />
	<BARRIER />
	<pbcast.NAKACK2 />
	<UNICAST2 />
	<pbcast.STABLE />
	<pbcast.GMS />
	<UFC />
	<MFC />
	<FRAG2 />
	<RSVP />
	<pbcast.STATE_TRANSFER />
	<CENTRAL_LOCK />
</config>
{% endhighlight %}
基于此协议栈，实现Lock接口：
{% highlight java %}
package com.dunkcoder;

import org.jgroups.JChannel;
import org.jgroups.blocks.locking.LockService;

public class JgroupsLock implements Lock {

	private JChannel channel;
	private LockService lockService;

	public JgroupsLock() {
		try {
			channel = new JChannel("udp-lock-stack.xml");
			lockService = new LockService(channel);
			channel.connect("LockCluster");
		} catch (Exception e) {
			throw new IllegalStateException(e);
		}
	}

	public void close() {
		lockService.unlockAll();
		channel.close();
	}

	public void lock(String name) {
		lockService.getLock(name).lock();
	}

	public void release(String name) {
		lockService.getLock(name).unlock();
	}
}
{% endhighlight %}

我们起一个Swing界面来测试一下：
{% highlight java %}
package com.dunkcoder;

import javax.imageio.ImageIO;
import javax.swing.*;

import java.awt.event.ActionEvent;

@SuppressWarnings("serial")
public class LockWindow extends JFrame {

	private final static Icon LOCKED_ICON;
	private final static Icon UNLOCKED_ICON;

	static {
		try {
			ClassLoader cl = LockWindow.class.getClassLoader();
			LOCKED_ICON = new ImageIcon(ImageIO.read(cl.getResource("locked.png")));
			UNLOCKED_ICON = new ImageIcon(ImageIO.read(cl.getResource("unlocked.png")));
		} catch (Exception e) {
			throw new IllegalStateException(e);
		}
	}

	private final Lock lock;
	private final String lockName;

	public LockWindow(Lock lock, String lockName) {
		super("Click the icon to see distributed lock");
		this.lock = lock;
		this.lockName = lockName;
		Action toggleAction = new ToggleLockAction();
		JButton toggleButton = new JButton(toggleAction);
		add(toggleButton);
		setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
		pack();
		setVisible(true);
	}

	private class ToggleLockAction extends AbstractAction {
		private boolean hasLock = false;

		private ToggleLockAction() {
			putValue(Action.LARGE_ICON_KEY, UNLOCKED_ICON);
		}

		public void actionPerformed(ActionEvent e) {
			if (hasLock) {
				lock.release(lockName);
				hasLock = false;
				putValue(Action.LARGE_ICON_KEY, UNLOCKED_ICON);
			} else {
				lock.lock(lockName);
				hasLock = true;
				putValue(Action.LARGE_ICON_KEY, LOCKED_ICON);
			}
		}
	}
}

package com.dunkcoder;

import javax.swing.*;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;

public class Main {
	public static void main(String[] args) {
		new Main();
	}

	private final JgroupsLock lockManager;
	private LockWindow lockWindow;

	public Main() {
		lockManager = new JgroupsLock();
		try {
			SwingUtilities.invokeAndWait(new Runnable() {
				public void run() {
					lockWindow = new LockWindow(lockManager, "LockName");
					lockWindow.addWindowListener(new WindowAdapter() {
						@Override
						public void windowClosed(WindowEvent e) {
							lockManager.close();
						}
					});
				}
			});
		} catch (Exception e) {
			throw new IllegalStateException(e);
		}

	}
}
{% endhighlight %}

jgroupsLock项目代码在[这里]()，切换到目录执行命令`mvn clean package`打包成jgroupsLock-1.0.one-jar.jar后，启动两个以上实例，点击Icon测试分布式锁。

	java -jar -Djava.net.preferIPv4Stack=true jgroupsLock-1.0.one-jar.jar

其它zookeeper方式可以参考下面几篇文章：<br/>
[Distributed Lock](http://whitesock.iteye.com/blog/1138579)<br/>
[JGroups 入门实践](http://www.cnblogs.com/fangfan/p/4042823.html)<br/>
[zookeeper分布式锁的实现](http://graduter.iteye.com/blog/2024190)


就酱，嗯。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
