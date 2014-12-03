---
layout: post
title: "Fix mysql 8hs problem with tomcatJdbcPool"
categories: programming
---

刚刚交付一个业务自助系统，运行环境是SpringMVC, JPA(Hibernate), MySql(Tomcat jdbcPool)。

每天凌晨0点到早上9点是没人访问的，于是每天上班均收到通知：系统报错，不能登录。经查日志，系统报错大致如下：

    org.hibernate.SessionException: Session is closed!
    Caused by: com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure ...

祭出谷歌大神一搜索才知道是mysql的8小时问题，官方说明在[这里](http://dev.mysql.com/doc/refman/5.0/en/server-system-variables.html#sysvar_wait_timeout)，大致意思是：mysql在默认配置下（28800秒）会主动断开闲置的连接。

找到了RootCause，问题很好解决：加一个类似心跳机制即可，在28800秒问题发生之前，定时和mysql心跳一下，剩下的事就是查Tomcat JdbcPool的文档了。

经测试，以下JdbcPool配置可正常工作，时间单位均为毫秒，可根据不同情况调整：
{% highlight xml %}
<bean id="dataSource" class="org.apache.tomcat.jdbc.pool.DataSource" destroy-method="close">
    <!-- Connection Info -->
    <property name="driverClassName" value="${jdbc.driver}" />
    <property name="url" value="${jdbc.url}" />
    <property name="username" value="${jdbc.username}" />
    <property name="password" value="${jdbc.password}" />

    <!-- Connection Pooling Info -->
    <property name="maxActive" value="${jdbc.pool.maxActive}" />
    <property name="maxIdle" value="${jdbc.pool.maxIdle}" />
    <property name="minIdle" value="0" />
    <property name="defaultAutoCommit" value="false" />

    <property name="timeBetweenEvictionRunsMillis" value="900000" />
    <property name="minEvictableIdleTimeMillis" value="1800000" />
    <property name="validationQuery" value="SELECT 1" />
    <property name="validationInterval" value="600000" />
    <property name="testWhileIdle" value="true" />
    <property name="testOnBorrow" value="true" />

</bean>
{% endhighlight %}

记录在此，其他连接池也有类似的处理方法。

就酱，嗯。您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
