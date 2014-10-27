---
layout: post
title: "Apache+Tomcat集群"
categories: programming
---

#### 本篇记录在Windows 2008R2 64bits环境下，基于Apache+Tomcat搭建和配置简单集群。
<br />
所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1o6kcmwE)：
>* apache-tomcat-7.0.56-windows-x64.zip
>* httpd-2.2.25-win32-x86-no_ssl.msi
>* probe-2.3.3.zip
>* Wireshark-win64-1.12.1.exe
>* tomcat-connectors-1.2.39-windows-i386-httpd-2.2.x.zip

集群主要作用在高可用(High Availability)和负载均衡(Load Balance)两个方面。

我准备将Apache + Tomcat集群测试环境搭建在三台Windows 2008R2 64bits环境机器上，分别是A（172.16.3.40），B（172.16.3.41）和C（172.16.3.42），A,B和C分别搭建Tomcat环境，部署probe.war，安装Wireshark，另外前置机Apache安装在A机器。这样一来，对用户来说，A机器的Apache地址是透明的，用户无需知道也不关心服务到底由谁来提供，接下来正式开始配置集群环境。

#### 配置Apache前置机Load Balancer
<br />
在A机器上安装Apache后，启动并测试`It works!`页面是否正常打开，确保Apache正常工作。

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

	worker.tomcat3.type=ajp13
	worker.tomcat3.host=172.16.3.42
	worker.tomcat3.port=8009
	worker.tomcat3.lbfactor=10

	worker.balancer.type=lb
	worker.balancer.balance_workers=tomcat1,tomcat2,tomcat3

	worker.stat.type=status
#### 搭建Tomcat环境和部署probe
<br />

集群中的工作单元，除了权重值和路由标识外，部署的应用必须完全相同。那么，只需先在A机器上搭建Tomcat环境和部署probe后，拷贝到其它两台机器即可。下面开始：

在A机器上安装JDK环境，搭建Tomcat环境后，启动并测试以确保Tomcat正常工作。

参考以下

<Engine name="Catalina" defaultHost="localhost" jvmRoute="jvm1">         
    --> 
<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat1">

**我们要注意的是：**

以上，本文是参考[Tomcat Clustering系列5篇](http://www.ramkitech.com/2012/10/tomcat-clustering-series-simple-load.html)的测试和实践，有梯子的朋友看完整英文版本会更明白。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
