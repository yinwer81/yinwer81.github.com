---
layout: post
title: "Load balancer with apache and tomcat cluster"
categories: programming
---

#### 本篇记录在Windows 2008R2 64bits环境下，基于Apache+Tomcat搭建和配置简单集群。
<br />
所用软件和版本如下，都已搜集到[度盘这里](http://pan.baidu.com/s/1o6kcmwE)：
>* apache-tomcat-7.0.56-windows-x64.zip
>* httpd-2.2.25-win32-x86-no_ssl.msi
>* probe-2.3.3.zip
>* Wireshark-win64-1.12.1.exe
>* tomcat-connectors-1.2.39-windows-i386-httpd-2.2.x.zip

集群主要作用在高可用(High Availability)和负载均衡(Load Balance)两个方面。

我准备将Apache + Tomcat集群测试环境搭建在两台Windows 2008R2 64bits环境机器上，分别是A（172.16.3.40）和B（172.16.3.41），AB分别搭建Tomcat环境，部署probe.war，安装Wireshark，另外前置机Apache安装在A机器。这样一来，对用户来说，A机器的Apache地址是透明的，用户无需知道也不关心服务到底由谁来提供，接下来正式开始配置集群环境。

#### 配置Apache前置机Load Balancer
<br />
在A机器上安装Apache后，启动并测试`http://A_IP/`80端口首页`It works!`是否正常打开，确保Apache正常工作。

这里选用mod_jk做负载均衡器，mod_jk默认使用非常简单的Round Robin Scheduling算法分发前端请求。<br/>安装配置mod_jk connector，从[这里](http://tomcat.apache.org/download-connectors.cgi)可下载对应版本，将mod_jk.so拷贝到Apache的modules目录下，接着修改Apache的httpd.conf文件以配置mod_jk（粘贴以下内容到文件末尾并保存退出）：

	# 确认mod_jk.so文件在Apache的modules目录
	LoadModule jk_module modules/mod_jk.so

	# 即将创建workers.properties文件到Apache的conf目录
	JkWorkersFile conf/workers.properties

	JkLogFile     logs/mod_jk.log

	# Set the jk log level [debug/error/info]
	JkLogLevel    emerg
	# Select the log format
	JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "
	# JkOptions indicate to send SSL KEY SIZE,
	JkOptions     +ForwardKeySize +ForwardURICompat -ForwardDirectories

	# JkRequestLogFormat set the request format
	JkRequestLogFormat     "%w %V %T %p %q %r %v %U"

	# balancer必须与workers.properties文件中worker.list的名字相同
	JkMount /* balancer
	# stat必须与workers.properties文件中worker.list的名字相同
	JkMount /status stat

在Apache的conf目录（与httpd.conf位置相同）创建workers.properties文件，粘贴以下内容，保存退出。

	worker.list=balancer,stat

	worker.tomcat1.type=ajp13
	worker.tomcat1.host=172.16.3.40
	worker.tomcat1.port=8009
	worker.tomcat1.lbfactor=10

	worker.tomcat2.type=ajp13
	worker.tomcat2.host=172.16.3.41
	worker.tomcat2.port=8009
	worker.tomcat2.lbfactor=10

	worker.balancer.type=lb
	worker.balancer.balance_workers=tomcat1,tomcat2

	worker.stat.type=status
此时，重启Apache服务，再次确认`http://A_IP/`80端口首页`It works!`是否正常打开，确保Apache正常工作。
#### 搭建Tomcat环境和部署probe
<br />

集群中的工作单元，除了权重值和路由标识外，部署的应用必须完全相同。那么，只需先在A机器上搭建Tomcat环境和部署probe后，拷贝到其它两台机器即可。下面开始：

在A机器上安装JDK环境，搭建Tomcat环境后，启动并测试以确保Tomcat正常工作。

接下来，修改tomcat中`conf\server.xml`配置，请参考：

打开注释并设置jvmRoute，这样Tomcat的SessionId就会长成这样：`<Random Value like before>.<jvmRoute>`，请注意，这里需要改成tomcatB。
{% highlight xml %}
<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcatA">
{% endhighlight %}
接下来，配置Cluster和广播地址以实现Session Replication，将Cluster配置到Engine中，其中D类(范围：224.0.0.0 to 239.255.255.255)地址，这里是228.0.0.4为广播地址，Windows默认已开启广播，参考以下内容：
{% highlight xml %}
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster" channelSendOptions="8">
	<Channel className="org.apache.catalina.tribes.group.GroupChannel">
		<Membership className="org.apache.catalina.tribes.membership.McastService" address="228.0.0.4" port="45564" frequency="500" dropTime="3000" /> 
		<Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
			<Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender" /> 
	  	</Sender>
	  	<Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver" address="auto" port="4000" autoBind="100" selectorTimeout="5000" maxThreads="6" /> 
	  	<Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector" /> 
	  	<Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor" /> 
  	</Channel>
  	<Valve className="org.apache.catalina.ha.tcp.ReplicationValve" filter="" /> 
  	<Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve" /> 
  	<ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener" /> 
  	<ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener" /> 
</Cluster>
{% endhighlight %}
最后，在Application的web.xml中，配置`<distributable/>`标签。

这里以probe.war为例，初次启动tomcat后，在tomcat中webapps下找到解开后的probe，将`<distributable/>`标签配置到web.xml中，同时，修改tomcat中cong/tomcat-users.xml，增加以下内容：
{% highlight xml %}
<role rolename="manager"/>
<role rolename="admin"/>
<user username="admin" password="" roles="admin,manager"/>
{% endhighlight %}

将以上内容复制到B机器，并记得修改对应的jvmRoute，启动A和B机器上的tomcat，通过链接`http://A_IP/probe`访问probe，可测试并验证Tomcat Cluster是否配置成功。

在A机器上安装Wireshark，启动后点击工具栏第一个按钮`List the available capture interfaces ...`，弹出的对话框中选择对应的网卡，并点击Start按钮，开始监控网络包，Filter中填写`ip.addr == B_IP`，Apply后可以看到大约每隔0.5秒(默认)的数据包，内容大约如下：

	Source: B_IP, Destination: 228.0.0.4, Protocal: UDP, Source port: 45564 Destination port: 45564

需要注意的是：
>* Session-Sticky的局域网内广播有一定开销，当Cluster中机器较多时也容易有Broadcast Storm Problem问题。建议尽量使用Non-Sticky方式。 
>* Cluster只能保证服务可用，只有从Application层面满足了Stateless才能实现无缝切换，即Request FailOver

本文参考[Tomcat Clustering系列5篇](http://www.ramkitech.com/2012/10/tomcat-clustering-series-simple-load.html)进行测试和实践，有梯子的最好看原版。

就酱，您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
