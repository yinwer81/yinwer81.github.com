---
layout: post
title: "Monitor sql with p6spy and irontracksql"
categories: programming
---
#### 本篇记录在开发阶段怎样使用p6spy和irontracksql打印和监控SQL执行情况。
<br />
目前p6spy主要有两个版本1.3和2.x，项目托管在github，[p6spy](https://github.com/p6spy/p6spy)传送门。

我这里基于1.3版本，例子项目[s3h3p6spy](https://github.com/yinwer81/s3h3p6spy)，经测试可以正常运行。

如果使用最新2.x版本，spy.properties配置文件不一样，需要注意。

例子程序环境如下（参考pom.xml）：
>* jdk.version 1.7
>* servlet.version 2.5
>* mysql.version 5.1.26
>* spring.version 3.2.4.RELEASE
>* hibernate.version 3.6.10.Final
>* dbcp.version 1.4
>* p6spy.version 1.3

#### 怎样将例子程序需跑起来：
<br />
修改jdbc.properties配置文件中关于数据库的配置参数：
>* 数据库驱动使用p6spy替代
>* 使用EncryptedDataSource产生加密的数据库连接用户名和密码
>* 如果不需要加密数据库连接用户名和密码，修改s3h3p6spy-servlet.xml中dataSource的配置

注释掉s3h3p6spy-servlet.xml中hibernate的以下参数配置，避免每次都重建数据库：
{% highlight xml %}
<prop key="hibernate.hbm2ddl.auto">create</prop>
{% endhighlight %}

spy.properties中关于irontracksql的配置如下：

	# uncomment these two lines if you want to use irongrid - start
	module.ibeam=com.irongrid.ibeam.server.IBeamFactory
	monitorport=2000
	# uncomment these two lines if you want to use irongrid - end

irontracksql包亦上传，在任意位置运行以下命令启动irontracksql监控sql执行情况：
{% highlight java %}
java -jar irontracksql.jar
{% endhighlight %}

irontracksql运行界面如下图，默认输出PreparedStatement语句，可在这里切换输出sql语句：

    菜单：View->Show Queries->Bound Variables

![示例](/images/IronTrackSQL.png)

以上，您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
