---
layout: post
title: "使用hibersap从SAP中读数据"
categories: programming
---

应用系统Syncer的主要职责是按业务规则将部分数据从SAP系统同步到Mysql数据库中（ScheduleJob和手工两种方式），供其它周边业务应用系统读取分析。

与SAP系统交换数据的途径有很多种，我们对WebService和JCo比较熟悉。本文记录怎样基于JCo同步SAP数据，WebService会有另文再讲。

WebService是无状态的，所以适用于较简单，数据交换小，即时调用的场景，而JCo则是通过在应用侧缓存SAP账号和密码（*.jcoDestination文件），保持和SAP的连接，适合于定时任务，大数据量交换和同步的场景。

基于Windows7 64bits环境，所用软件和版本如下，都已搜集到[这里](http://pan.baidu.com/s/1o6kcmwE)：
>* forge-distribution-1.4.4.Final.zip
>* plugin-hibersap-master.zip

JCO即Java Connector，SAP在sapjcoX.dll/sapjcoX.so基础上做JNI封装提供了sapjcoX.jar。在实际项目中，我们一般是创建SapJCoUtil类对sapjcoX.jar进行再次包装。我这里不打算这么做：

下载并解压缩plugin-hibersap到`C:\plugin-hibersap-master`，稍微修改一下pom.xml，使用`mvn clean package -Dmaven.test.skip=true`命令打包，没有问题：

	<properties>
    	<jco.version>3.0.10</jco.version>
    	<servlet-api.version>3.0.1</servlet-api.version>
    	<validation-api.version>1.0.0.GA</validation-api.version>
    	<hibersap.version>1.2.0-Beta03</hibersap.version>
    	<forge.api.version>1.4.4.Final</forge.api.version>
	</properties>

	# 使用jaxb-impl 2.2.7版本
	<dependency>
      <groupId>com.sun.xml.bind</groupId>
      <artifactId>jaxb-impl</artifactId>
      <version>2.2.7</version>
    </dependency>

使用命令`mvn archetype:create -DgroupId=com.dunkcoder -DartifactId=testHibersap`创建Maven Java项目，修改pom.xml和创建hibersap.xml：
	
	# pom.xml中增加sap-jco引用
	<dependency>
      <groupId>com.sap</groupId>
      <artifactId>sap-jco</artifactId>
      <version>3.0.10</version>
    </dependency>

    # 暂且hibersap.xml先长成这样，放在testHibersap\src\main\resources\META-INF\目录下
    <?xml version="1.0" encoding="UTF-8"?>
	<hibersap xmlns="urn:hibersap:hibersap-configuration:1.0">
		<session-manager name="sapenvtest">
			<context>org.hibersap.execution.jco.JCoContext</context>
			<properties>
				<property name="jco.client.client" value="jco_client" />
				<property name="jco.client.user" value="jco_user" />
				<property name="jco.client.passwd" value="jco_passwd" />
				<property name="jco.client.lang" value="jco_lang" />
				<property name="jco.client.ashost" value="jco.ashost" />
				<property name="jco.client.sysnr" value="jso.sysnr" />
				<property name="jco.destination.pool_capacity" value="pool_capacity" />
			</properties>
			<annotated-classes>

			</annotated-classes>
		</session-manager>
	</hibersap>

下载并解压缩forge到`C:\forge-distribution-1.4.4.Final`，双击forge.bat启动，没有问题。接下来安装plugin-hibersap，默认会安装到%USER_HOME%\.forge\plugins目录，如果需要重头初始化forge环境，删除.forge目录重新初始化即可。
	
	# 本地安装方式
	[no project]  $ forge source-plugin C:\plugin-hibersap-master\

	# ... ... 需要多等一会，没有问题，plugin-hibersap安装成功
	The following plugins have been activated: [generate-sap-entities]

	# 切换到testHibersap项目
	[no project]  $ cd C:\testHibersap
	[testHibersap] testHibersap $

	# 查看命令帮助
	[testHibersap] testHibersap $ help generate-sap-entities

	# 在hibersap plugin的config目录下初始化sap-connection.properties文件，控制台会打印出当前各个配置项及值
	[testHibersap] testHibersap $ generate-sap-entities list-properties

	jco.destination.pool_capacity=1
	jca.connectionspec.factory=org.hibersap.execution.jca.cci.SapBapiJcaAdapterConnectionSpecFactory
	session-manager.name=SM001
	jco.client.lang=en
	jco.client.ashost=some.sap-system.com
	jco.client.user=sapuser
	jco.client.sysnr=00
	jco.client.passwd=password
	jco.context=org.hibersap.execution.jco.JCoContext
	jca.context=org.hibersap.execution.jco.JCAContext
	jco.client.client=001
	jca.connection.factory=java:/eis/sap/A12

	# jca两个配置项用不到，删除
	generate-sap-entities delete-property --key jca.connectionspec.factory
	generate-sap-entities delete-property --key jca.connection.factory

	# 逐个修改SAP连接配置，也可以直接修改sap-connection.properties文件
	# 类似your_passwd之类比较复杂，可用双引号""包括起来
	generate-sap-entities set-property --key session-manager.name --value sapenvtest
	generate-sap-entities set-property --key jco.client.client --value your_client
	generate-sap-entities set-property --key jco.client.user --value your_user
	generate-sap-entities set-property --key jco.client.passwd --value your_passwd
	generate-sap-entities set-property --key jco.client.lang --value your_lang
	generate-sap-entities set-property --key jco.client.ashost --value your_ashost
	generate-sap-entities set-property --key jco.client.sysnr --value your_sysnr
	generate-sap-entities set-property --key jco.destination.pool_capacity --value your-pool_capacity

OK，环境配置好了。

ABAP程序员会告诉你应该调用哪些SAP RFC或BAPI函数，怎么传参，返回结果如何判断等。接下来是重头戏，以下面3个为例，按照向导提示逐步往下生成Java代码进行测试（*号为通配符）。按照我的步骤来，整个过程不会有什么错误（代码生成向导中选择了`SessionManager：saptestenv`的话会自动修改hibersap.xml配置，添加annotated-classes值项）：

	[testHibersap] testHibersap $ generate-sap-entities --name-pattern *STFC_CONNECTION*
	[testHibersap] testHibersap $ generate-sap-entities --name-pattern *BAPI_COMPANYCODE_GETLIST*
	[testHibersap] testHibersap $ generate-sap-entities --name-pattern *RFC_SYSTEM_INFO*
**如果无论如何都不能正常生成代码，是SAP方RFC或BAPI的问题，本人就曾经遭遇过此种情况。**

在eclipse中导入testHibersap项目，当然，自动生成的代码有些小瑕疵：BapiCompanycodeGetlist和RfcSystemInfo的无参构造函数少了大括号，手工补上。接下来写SapJCoUtil类并测试：
{% highlight java %}
package com.dunkcoder.util;

import java.util.List;

import org.hibersap.configuration.AnnotationConfiguration;
import org.hibersap.session.Session;
import org.hibersap.session.SessionManager;

import com.dunkcoder.hibersap.BapiCompanycodeGetlist;
import com.dunkcoder.hibersap.CompanycodeList;
import com.dunkcoder.hibersap.RfcSystemInfo;
import com.dunkcoder.hibersap.RfcsiExport;
import com.dunkcoder.hibersap.StfcConnection;

public class SapJCoUtil {

	public static String echoTextOfStfcConnection() {

		AnnotationConfiguration config = new AnnotationConfiguration("sapenvtest");
		SessionManager sessionManager = config.buildSessionManager();

		final Session session = sessionManager.openSession();
		StfcConnection conn = new StfcConnection("requtext");
		session.execute(conn);
		session.close();

		return conn.get_echotext();

	}

	public static RfcsiExport getSystemInfo() {

		AnnotationConfiguration config = new AnnotationConfiguration("sapenvtest");
		SessionManager sessionManager = config.buildSessionManager();

		final Session session = sessionManager.openSession();
		RfcSystemInfo info = new RfcSystemInfo();
		session.execute(info);
		session.close();

		return info.get_rfcsiExport();

	}

	public static List<CompanycodeList> getCompanyCodeList() {

		AnnotationConfiguration config = new AnnotationConfiguration("sapenvtest");
		SessionManager sessionManager = config.buildSessionManager();

		final Session session = sessionManager.openSession();
		BapiCompanycodeGetlist list = new BapiCompanycodeGetlist();
		session.execute(list);
		session.close();

		return list.get_companycodeList();

	}

	public static void main(String[] args) {
		echoTextOfStfcConnection();
		getSystemInfo();
		getCompanyCodeList();
	}
	
}
{% endhighlight %}

嗯，还可以，简单清爽，省却了包装sapjcoX.jar代码的烦恼，具体项目中可将AnnotationConfiguration和SessionManager改为注入方式。

测试项目testhibersap的完整代码在[这里](https://github.com/yinwer81/testHibersap)。


以上。

您有任何问题或建议，请给我写[邮件](mailto:yinwer81@gmail.com)。
