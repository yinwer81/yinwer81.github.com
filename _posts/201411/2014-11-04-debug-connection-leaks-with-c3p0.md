---
layout: post
title: "Debug connection leaks with C3P0"
categories: programming
---

维护项目SCM，系统集成测试时发现有数据库连接泄露发生，历史故事/客观原因等也就不说了，代码量较大，不想也无法通过CodeReview进行排查，导入代码发现用的是C3P0，经查C3P0官方文档：有戏！

目前C3P0代码放在Github[这里](https://github.com/swaldman/c3p0)，最新版本：`c3p0-0.9.2.1`。

文档在`Configuring to Debug and Workaround Broken Client Applications`位置，提到了两个参数可以简单粗暴的快速定位到具体位置。
>* debugUnreturnedConnectionStackTraces
>* unreturnedConnectionTimeout
	
达到最大存活时间后不能被正常收回的数据库连接肯定出现了泄露，参考如下配置：当数据库连接达到最大存活时间maxIdleTime后没有被正常收回，再过30秒，由C3P0主动执行Kill并打印堆栈信息。
{% highlight xml %}
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource" destroy-method="close">
	
	<property name="maxIdleTime">
		<value>60</value>
	</property>

	...

	<property name="debugUnreturnedConnectionStackTraces">
		<value>true</value>
	</property>

	<!-- maxIdleTime + 30 seconds -->
	<property name="unreturnedConnectionTimeout">
		<value>90</value>
	</property>

</bean>
{% endhighlight %}
很容易从堆栈信息定位到具体位置：没有CodeReview，DAO使用不规范导致，继承了HibernateDaoSupport，通过getHibernateTemplate().getSessionFactory().openSession()获得session，session.createSQLQuery()后**没有session.close()**

以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
