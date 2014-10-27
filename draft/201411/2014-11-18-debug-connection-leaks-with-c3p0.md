---
layout: post
title: "Debug connection leaks with C3P0"
---

c3p0官方页面: [戳这里](https://github.com/swaldman/c3p0), 最新版本: c3p0-0.9.2.1

c3p0 can help you debug, by letting you know where Connections are checked out that occasionally don't get checked in with below two parameters: 
	debugUnreturnedConnectionStackTraces
	unreturnedConnectionTimeout

参考实践配置如下：
{% highlight xml %}
<bean id="dataSource" 
	class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">
	
	<property name="maxIdleTime">
		<value>60</value>
	</property>

	<property name="debugUnreturnedConnectionStackTraces">
		<value>true</value>
	</property>

	<!-- maxIdleTime + 30 seconds -->
	<property name="unreturnedConnectionTimeout">
		<value>90</value>
	</property>

</bean>
{% endhighlight %}
调试并重现原来情境, 堆栈信息中可轻松定位到JDBC连接泄漏具体位置和代码。

关于参数的详细说明, 可参考官方文档, 值得注意的是: 
debugUnreturnedConnectionStackTraces is intended to be used only for debugging, as capturing a stack trace can slow down Connection check-out.<br>

以上。
<br>